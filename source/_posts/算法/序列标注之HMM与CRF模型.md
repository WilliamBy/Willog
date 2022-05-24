---
categories:
  - 算法
abbrlink: 568ec988
toc: true
tags:
  - AI
  - NLP
sidebar:
    left:
        sticky: true
    right:
        sticky: false
widgets: 
	- 
		position: left
		type: toc
plugins:
	katex: true
thumbnail: "https://migrate-1301429536.cos.ap-nanjing.myqcloud.com/img/20200728191026.png"
date: 2020-6-15
top: 100
---
{% label 机器学习 primary %}
{% label HMM info %}
{% label CRF success %}

## 绪论

​	从文本翻译到文字、语音识别，再到命名实体识别，词性标注，序列标注问题已然渗透到社会生活中的方方面面。不少自然语言处理的问题都可以想方设法转化为序列标注问题：对邮件等”大块“的信息做命名实体识别，识别出信息中的表单项用于自动填写表单，可以有效地提高工作效率，节约时间；对音频文件进行标注分析，可以用来进行语音识别；抽取出来的电子病历文本中的疾病、治疗、检查等类型的实体，可以用以对电子病历进行分类归档......想要处理好一般性的自然语言处理问题到序列标记问题的转化，关键在于处理好如何定义标记的问题。<!-- more -->

​	作为自然语言处理中常见的问题之一，序列标注问题已经由来已久，并在经历了一系列的发展变革后逐渐壮大健全。最早的有传统的隐马尔可夫模型、最大熵模型和条件随机场模型，最大熵模型抛弃了隐马尔科夫模型的独立输出假设，而条件随机场模型又在最大熵模型的基础上进一步将标签的转移扩大为全局特征，使得上下文之间的联系更加紧密。无论是在理论还是实践结果上来看，条件随机场的整体效果都要优于前两种方法。步入深度学习发展的时代后，新的诸如$RNN$和$LSTM$模型也相继用于序列标注问题，有效地改善了传统方法在处理更长的上下文依赖、过拟合、依赖启发式知识等方面的不足之处。本文主要研究和探讨了传统的序列标注模型——隐马尔可夫模型和条件随机场模型——的理论基础和应用实践。

## 正文

### 定义

​	序列标注是指将输入的一串观测序列 $x_1x_2x_3...x_n$ 转化为一串标记序列 $y_1y_2y_3...y_n$ 的过程，要解决序列标记问题，实际上就是要找到一个观测序列到标记序列的映射 $f(x_i)\rightarrow y_i\space (i=1,2,...,n)$。

### HMM模型

#### 马尔可夫链

{% alert info %}
	**马尔可夫链**（Markov chain）[^1]，又称离散时间马尔可夫链，为状态空间中经过从一个状态到另一个状态的转换的随机过程。该过程要求具备“无记忆”的性质：下一状态的概率分布只能由当前状态决定，在时间序列中它前面的事件均与之无关。
{% endalert %}

[^1]: 维基百科中文：马尔科夫链

​	从数学形式上来看，对于随机变量序列$X_1X_2X_3...X_n$，满足
$$
P(X_n=x_n|X_1=x_1,X_2=x_2,X_3=x_3...,X_{n-1}=x_{n-1})=P(X_n=x_n|X_{n-1}=x_{n-1})
$$
，则称该序列为一个一阶马尔可夫链。

​	从宏观意义上来看，Markov链说明了在状态转移过程中，“未来”与“过去”无关而只与“现在”有关。

​	Markov链常常被认为是**时齐**的，即转移概率与n无关。

​	类似的可以定义**m阶Markov链**，满足
$$
\begin{aligned}
&P(X_n=x_n|X_1=x_1,X_2=x_2,X_3=x_3...,X_{n-1}=x_{n-1})\\
=&P(X_n=x_n|X_{n-m}=x_{n-m},X_{n-m+1}=x_{n-m+1}...,X_{n-1}=x_{n-1})
\end{aligned}
$$

#### 隐马尔可夫模型

​	**隐马尔可夫模型**（Hidden Markov Model 缩写：HMM）基于Markov链，不同与一般的Markov链，HMM 又加入了**隐变量**这一概念，即该模型包含了观测序列$X_1X_2X_3...X_n$和与它一一对应的标记序列$Y_1Y_2Y_3...Y_n$。该模型用到了两个基本假设：

1. 时齐的Markov性假设，如2.2.1所述
2. 独立输出假设，输出状态的概率仅与输入状态相关，而与输入序列的前后文无关

以图形表示的隐马尔可夫模型如下所示：

<img src="https://migrate-1301429536.cos.ap-nanjing.myqcloud.com/img/20200728191122.png" alt="graph 1"  />

HMM 模型解决了被用来解决序列标注问题中的三个基本问题：预测、平滑和解码。本文主要讨论的解码问题，即求解给定观测序列，给出最有可能的标记序列的问题。本文使用$\lambda(W,T,\pi,\beta,A,B)$来表征一个序列标注问题的HMM模型，其中$W$表示观测序列，$T$表示输出的标记序列，$\pi$表示标记之间的概率转移转移矩阵，$\beta$表示标记到观测值的发射概率，$A$表示输入对象的集合，$B$表示标记的集合。

#### 概率模型

问题归结于求解下式
$$
arg_{T}max\space P(T=t_1t_2...t_n|W=w_1w_2...w_n,\lambda)\qquad (t_i\in B, w_i\in A)\tag{1}
$$
由贝叶斯概率公式
$$
P(T|W)=\frac{P(W|T)P(T)}{P(W)}\propto P(W|T)P(T)\tag{2}
$$
因为 $P(W)=const$ 故而可以省略不予以考虑

由独立输出假设可知
$$
P(W|T)=P(w_1|t_1)P(w_2|t_2)...P(w_n|t_n)\tag{3}
$$
由一阶时齐的Markov性假设可知
$$
P(T)=P(t_1)P(t_2|t_1)P(t_3|t_2)...P(t_{n}|t_{n-1})\tag{4}
$$
联立方程 $(2)(3)(4)\Rightarrow$
$$
P(T|W)\sim [P(t_1)P(w_1|t_1)][P(t_2|t_1)P(w_2|t_2)]...[P(t_n|t_n-1)P(w_n|t_n)]\tag{5}
$$
其中转移概率由条件概率公式可知
$$
P(t_i|t_{i-1})=\frac{P(t_i,t_{i-1})}{P(t_{i-1})}\tag{6}
$$
等式$(6)$中标记值的概率和等式$(5)$中的独立输出概率可由**统计方法**或**规则方法**得出，下面介绍两种方法的异同：

| 项目 | 统计方法                       | 规则方法                                       |
| ---- | ------------------------------ | ---------------------------------------------- |
| 介绍 | 通过统计得出频率估计概率的方法 | 通过既定的规则来估计概率的方法                 |
| 优点 | 覆盖面广，稳定性高             | 精确，对于特殊现象的支持度好                   |
| 缺点 | 对于特殊现象的支持度差         | 规则库庞大，编写和维护复杂，覆盖面不及统计方法 |

能够计算出特定$T^*$的概率值$P(T^*|W)$还不够，还要要找到最大的概率对应的$T$。这个问题可以转化成一个求解**最短加权路径**的图论问题。

<img src="https://migrate-1301429536.cos.ap-nanjing.myqcloud.com/img/20200728191133.png" alt="graph 2" style="zoom:80%;" />

可构建像 *graph 1* 所示的图模型（其余路径未画出）。显然，如果标记的取值有$m$种可能，序列的长度为$n$，那么枚举所有路径然后取加权值最大的路径的算法复杂度为 $O(n^m)$ ，随着序列长度增长而呈指数级增长。为了降低复杂度这里一般采用*维特比算法* (Viterbi algorithm) 。

> The **Viterbi algorithm**[^2] is a [dynamic programming](https://en.wikipedia.org/wiki/Dynamic_programming) [algorithm](https://en.wikipedia.org/wiki/Algorithm) for finding the most [likely](https://en.wikipedia.org/wiki/Likelihood_function) sequence of hidden states—called the **Viterbi path**—that results in a sequence of observed events, especially in the context of [Markov information sources](https://en.wikipedia.org/wiki/Markov_information_source) and [hidden Markov models](https://en.wikipedia.org/wiki/Hidden_Markov_model) (HMM).

[^2]: wikipedia: Viterbi algorithm

维特比算法是一种动态规划算法，用于找出隐藏的最大可能路径（维特比路径）。维特比算法的核心思想在于**全局最优包含局部最优**。考虑从起点到某个中间点的最大加权路径，那么该路径所经过的该中间点的前一个点在同时间上所有点中也一定是加权最大的点。下面给出了Viterbi算法的伪代码。其中$T_1,T_2$分别为保存当前节点最大累计权值的矩阵和保存该节点最大累计权值路径上的上一个节点的矩阵。以下给出了维特比算法的算法伪代码：

<img src="https://migrate-1301429536.cos.ap-nanjing.myqcloud.com/img/20200728191129.png" alt="graph3" style="zoom:80%;" />

{% alert warning %}
下溢问题：$(5)$ 式中的连乘积中因子有可能过小而下溢为零从而造成整个式子变为0，为了避免这种情况可以考虑取对数化乘积为加法的技巧。
{% endalert %}

#### 应用

​	绝大多数自然语言处理问题（Natural Language Processing Problem，简称NLP问题），都可以转化为一个序列标注问题。想要处理好NLP问题到序列标记问题的转化关键在于处理好如何定义标记的问题。例如对于中文分词问题，可以定义标签集合 $T=\{B,M,E,S\}$ ，其中 $B(begin),M(middle),E(end),S(single)$ 分别表示词的开头，中间，结尾和单个字成词；对于词性标注问题，可以在分词处理的基础上让一个词对应一个词性标签。对于命名实体识别问题，可以定义标签集合 $T=\{BL,ML,EL,BP,MP,EP,BO,MO,EO,N\}$ 其中 $B,M,E$ 后面跟着的 $L(location),P(person),O(organization)$ 分别表示地名，人民，机构名，而 $N$ 表示不属于实体的部分。定义好标记之后输入训练好的模型后问题即可求解。

### CRF 模型

#### 随机场、马尔可夫随机场、条件随机场

​	考虑一个空间内各个区域按照某种分布被赋予了某个随机值，这样的场称之为“随机场”。而马尔可夫随机场则是一类特殊的随机场，场中每个点的赋值不仅与自身位置有关，还和相邻位置的赋值相关。如果考虑马尔科夫性的同时将点的赋值概率看做是条件概率，则称该马尔科夫场为条件随机场（Conditional random fields 简称：CRF）。如维基百科[^3]中所述:

> Whereas a [classifier](https://en.wikipedia.org/wiki/Statistical_classification) predicts a label for a single sample without considering "neighboring" samples, a CRF can take context into account.

[^3]: wikipedia: Conditional_random_field

​	CRF模型和那些仅仅只考虑自身来进行分类的模型不同之处就在于CRF模型将样本点的“邻居”也考虑在内，这个特点使得CRF模型有能力处理上下文有关联的序列标注的问题。

#### 线性链条件随机场

线性链的条件随机场（Linear conditional random fields）是对条件随机场的简化模型：$X=(X1,X2,...Xn),Y=(Y1,Y2,...Yn)$ 均为线性链表示的随机变量序列，在给定随机变量序列$X$的情况下，随机变量$Y$的条件概率分布 $P(Y|X)$ 构成条件随机场，即满足马尔科夫性：
$$
P(Yi|X,Y1,Y2,...Yn)=P(Yi|X,Yi−1,Yi+1)\tag{7}
$$
则称 $P(Y|X)$ 为线性链条件随机场。　

#### 概率模型

​	为了建立可供学习的CRF模型，可以定义两类特征函数（这些特征函数用来评价一个序列在某些特征方面是否“靠谱”，只有 $1, 0$ 两种取值，即判断是否满足条件）：一类只与当前节点有关，表示为$s_l(y_i,x,i)\space (l=1,2...,m)$；另一类还与前一个节点有关，表示为$t_k(y_{i-1},y_i,x,i)\space (k=1,2...,n)$。引入需要学习的权重参数（所谓“权重”即用来衡量一个判断依据的重要程度的参数）$\lambda_l,\mu_k$，则条件概率表示为
$$
\begin{aligned}
P(y|x)&=\frac{1}{Z(x)}exp(\sum_{i,k}λ_kt_k(y_{i−1},y_i,x,i)+\sum_{i,l}μ_ls_l(y_i,x,i))\\
&\propto exp(\sum_{i,k}λ_kt_k(y_{i−1},y_i,x,i)+\sum_{i,l}μ_ls_l(y_i,x,i))
\end{aligned}\tag{8}
$$
$Z(x)$为规范化因子，是所有可能的标注序列的概率和，使得等式右边规范为一个概率。
$$
Z(x)=\sum_{y}exp(\sum_{i,k}λ_kt_k(y_{i−1},y_i,x,i)+\sum_{i,l}μ_ls_l(y_i,x,i))\tag{9}
$$
为了使得表示更加方便，将参数 $\lambda,\mu$ 均表示为 $w$ ，特征函数 $t, s$ 均表示为 $f$ ，即有
$$
w_{k}=\left\{
\begin{aligned}
&\lambda_k\qquad &(0 < k \leqslant m)\\
&\mu_{k-m}\qquad &(m < k \leqslant m+n)
\end{aligned}\tag{10}
\right.
$$
$$
f_{k}(y,x)=\left\{
\begin{aligned}
&\sum_{i}s_k(y_i,x,i)\qquad &(0 < k \leqslant m)\\
&\sum_{i}t_{k-m}(y_{i-1},y_i,x,i)\qquad &(m < k \leqslant m+n)
\end{aligned}\tag{11}
\right.
$$

由 $(11)(12)$ 式可将 $(8)$ 式简化为
$$
P(y|x)=\frac{exp(\sum_k w_kf_k(y,x))}{\sum_yexp(\sum_k w_kf_k(y,x))}\tag{12}
\propto exp(\sum_k w_kf_k(y,x))
$$

#### 参数学习

以下采用梯度下降法来训练参数。

定义损失函数
$$
\begin{aligned}
L&=-M\\
&=-log\prod_{x,y}P(y|x)^{\overline{P}(x,y)}\\
&=-\sum_{x,y}\overline{P}(x,y)logP(y|x)\\
&=\sum_{x}\overline{P}(x)logZ(x)-\sum_{x,y}\overline{P}(x,y)\sum_k w_kf_k(y,x)\\
&=\sum_{x}\overline{P}(x)log\sum_{y}exp\big(\sum_k w_kf_k(y,x)\big)-\sum_{x,y}\overline{P}(x,y)\sum_k w_kf_k(y,x)
\end{aligned}\tag{13}
$$
其中$\overline{P}$为先验概率，可以通过统计方法得出。

对损失函数求导可得：
$$
\frac{\partial L}{\partial w_k}=\sum_{x,y}\overline{P}(x)P(y|x)f_k(y,x)-\sum_{x,y}\overline{P}(x,y)f_k(y,x)\tag{14}
$$

有了损失函数的导数之后就可以进行迭代优化参数，直到达到收敛条件：
$$
w_k:=w_k-\eta\cdot\frac{\partial L}{\partial w_k}\tag{15}
$$
梯度下降法的算法流程为：定义损失函数 $L(w)$、学习率 $\eta\rightarrow$计算梯度 $\frac{\partial L}{\partial w_k}\rightarrow$更新 $w\rightarrow$计算梯度 $\frac{\partial L}{\partial w_k}\rightarrow$更新 $w\rightarrow...\rightarrow$达到收敛条件，得到训练后参数 $w$

同样的，将训练好的CRF模型用于解码问题也可以采用维特比算法，此处将不再赘述。

