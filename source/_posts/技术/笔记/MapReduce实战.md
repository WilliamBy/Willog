---
categories:
  - 技术
  - 笔记
---
# MapReduce 实现贝叶斯分类器

## 理论基础

**朴素贝叶斯公式**求后验概率
$$
P(c_i|d)=\frac{P(d|c_i)P(c_i)}{P(d)}
$$
其中 $d$ 为文档，$c_i$ 为分类。文档是词的合集，提取特征 $d=<t_1, t_2, ... ,t_n>$，即文档由n个词 *token*（可重复）组成。做**独立性假设**后公式可化简为：
$$
P(c_i|d)=\frac{\prod_{j=1}^{n} P(t_j|c_i)P(c_i)}{P(d)}
$$
由于需要知道的是文档最有可能的分类，因此实际上是求 $\arg\max_{c_i}P(c_i|d)$，又因为 $P(d)$ 不变，因此也就是求：
$$
\arg\max_{c_i}\prod_{j=1}^{n}P(t_j|c_i)P(c_i) \\
\propto \arg\max_{c_i}(\sum_{j=1}^{n}\log{P(t_j|c_i)}+\log{P(c_i)})
$$
$P(t_j|c_i)$ 用词 $t_j$ 在类别 $c_i$ 的文档中的占比来**估计**，$P(c_i)$ 用类别 $c_i$ 的文档在所有文档中比重来**估计**。并且为了避免个别 $P(t_j|c_i)=0$ 导致错误，对单词频数加1进行平滑：
$$
\hat{P}(t_j|c_i) & = \frac{N(t=t_j,\space c=c_i)+1}{\sum_{t_k \in V_i} (N(t=t_k,\space c=c_i)+1)} \\
& = \frac{N(t=t_j,\space c=c_i)+1}{N(c=c_i)+|V_i|}
$$

$$
\hat{P}(c_i)=\frac{N(c_i)}{N}
$$

其中 $N(t=t_j,c=c_i)$ 为 $c_i$ 类文章中 $t_j$ 出现的次数， $V_i$ 为出现在所有类型为 $c_i$ 的文档中的单词集合（不重复），$|V_i|$ 为不重复单词数。

## 整体框架

- Job1：统计$N(c=c_i)$
  - 输入训练集（分好类的文档，不同类别的文档在不同的目录中，目录名即为类别名）
  - `Mapper<Text, Writable, Text, Writable>`
    - 使用自定义的`InputFormat`，只读取文件所在目录名而不读取内容，并且规定split不会切分文件，防止统计冗余
    - k1=k2：继承InputFormat的输出键值对，类别名=目录名
    - v1=v2：`1`
  - `Reducer<Text, List<Writable>`
    - 输出 `<类别, 总数>`
- Job2：统计 $N(t=t_j,c=c_i)$
  - 输入训练集
  - `Mapper<LongWritable, Text, Text, IntWritable>`
    - 使用默认的`TextInputFormat`
    - `RecordReader` 按行读取（使用`TextInputFormat`），一行一个单词，同时读取split包含的文件名作为类别，将类别和单词用`-`拼接成一个Text类型变量作为Key输出
    - Value 输出取 `1` 即可
  - `Reducer< Text, List<IntWritable> >`
    - 输出`< 类别-单词, 总数 >`
- Job3: 统计$|V_i|$
  - 输入job2的output
  - `Mapper<Text, IntWritable>`
    - RecordReader 读入每一行，Map成`<类别，个数>`

  - Reducer 输入`<类别, list{个数}>`，输出`<类别, 个数求和>`



- Job3：测试
  - 输入测试集（待分类的文档）
  - 输出文档类别
  - 评估分类效果

## 环境准备

### 安装 Hadoop

- 官网给的都是基于 Linux 系统构建的，如果要在 Windows 系统上使用，需要补全 bin 中的`winutils.exe`，`hadoop.dll`等文件
  - 或者下载源码，按照说明自己在 Windows 上构建
- 需要指定相应版本的 JDK （一般都是 Java 8）
- 正确设置环境变量 `%HADOOP_HOME%`
  - 使用 `Cygwin` 环境执行 Hadoop 命令会产生路径问题，导致无法正确加载类

### 启用 Hadoop 环境

- 注意启用 SSH Server 服务

``` shell
PowerShell> net start sshd
```

- 运行相应的启动脚本以启动 Hadoop 相应服务：`$ hadoop-3.3.5\sbin\start-xxx.cmd`
  - 例如启动 **hdfs** 则执行 `sbin\start-dfs.cmd`
  - 注意使用**管理员权限**运行 Shell

- Win 下使用 `hdfs dfs` 命令行的 `-put` 和 `-get` 命令时，指定本地路径时不能使用相对路径
- 开启dfs后无法写入：离开NameNode安全模式 `hdfs dfsadmin -safemode leave`

## 数据准备

- 选择`NBCorpus\Country\AUSTR`、`NBCorpus\Country\BRAZ`和`BCorpus\Country\CANA`三个目录中的文件作为训练数据，分类的class为`AUSTER`和`CANA`
- 编写python脚本，按照**8:2**的比例划分训练集和测试集，并存入hdfs

| 类别  | 训练集数目 | 测试集数目 |
| ----- | ---------- | ---------- |
| AUSTR | 244        | 61         |
| BRAZ  | 160        | 40         |
| CANA  | 211        | 52         |

## 程序编写

- 主要编写两个 Java 文件，一个是 MapReduce 程序 `NaiveBayes.java`，实现朴素贝叶斯分类器算法。另一个是本地控制台程序 `Predictor.java` ，根据测试集预测并输出评估指标。
- `NaiveBayes.java` 实现[三个 job](##整体框架)，输入分好词的文件（训练集），以文本形式将统计结果输出到三个文件夹中。
- `Predictor.java` 根据测试集文件夹内容对每个文件进行预测，产生混淆矩阵，并计算每个类别对应的 Precision、Recall和F1值，最后还会输出指标平均值。

## 编译运行

- 执行以下命令编译 `NaiveBayes.java` 文件（或者在IDEA中编译），获得一系列字节码文件 `NaiveBayes*.class`

``` sh
$ hadoop com.sun.tools.javac.Main -Xlint:deprecation -encoding UTF-8 NaiveBayes.java
```

- 打包 jar 并部署到 hadoop 运行

``` sh
$ jar cf nb.jar NaiveBayes*.class
$ hadoop jar D:\Workspace\MapReduce-Bayes\src\main\java\nb.jar NaiveBayes /bayes/dataset/train /bayes/output/job1 /bayes/output/job2 AUSTR CANA BRAZ
```

- 将输出结果复制到本地

``` sh
$ hdfs dfs -get /bayes/output/job1/part-r-00000 D:\Workspace\MapReduce-Bayes\model\job1
$ hdfs dfs -get /bayes/output/job2/part-r-00000 D:\Workspace\MapReduce-Bayes\model\job2
$ hdfs dfs -get /bayes/output/job3/part-r-00000 D:\Workspace\MapReduce-Bayes\model\job3
```

- 三个 job 的输出结果预览如下所示

``` txt job1
AUSTR	244
BRAZ	160
CANA	210
```

``` txt job2
...
AUSTR-0.020	2
AUSTR-0.02goodman	1
AUSTR-0.02macraes	1
AUSTR-0.03	3
AUSTR-0.0310yrbond	1
... (省略)
BRAZ-ymos	1
BRAZ-york	7
BRAZ-zagallo	1
BRAZ-zamorano	2
BRAZ-zero	1
BRAZ-zinc	3
CANA-0	26
CANA-0-8	1
CANA-0.0	2
CANA-0.02	1
CANA-0.03	3
CANA-0.043	1
...
```

``` txt job3
AUSTR	50361
BRAZ	20346
CANA	34270
```

- IDEA下编译运行 `Predictor.java` 查看预测结果

## 实验结果

### 预测评估

- **Predictor** 的控制台输出如下，从上到下依次是**混淆矩阵**、各个类别的 **Precision、Recall和F1**分数，最后是**均值**。

![image-20240119121000767](https://picgo-1301429536.cos.ap-nanjing.myqcloud.com/img/image-20240119121000767.png)