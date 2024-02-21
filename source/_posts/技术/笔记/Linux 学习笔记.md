# Part 1 规则与安装

## C4 首次登陆与在线求助

| 命令                         | 说明                                                 | 示例                                                         |
| ---------------------------- | ---------------------------------------------------- | ------------------------------------------------------------ |
| `startx`                     | 启动 **X Window**（GUI）                             |                                                              |
| `\<enter>`                   | 命令延展到下一行（转移换行）                         |                                                              |
| `locale`                     | 查看语系（与字符输出格式相关）                       |                                                              |
| `LANG=xxx`                   | 变更语言，当输出呈现编码错误的时候可以试着改变LANG值 | `LANG=en_US.utf`                                             |
| `date`, `cl`                 | 时间、日历                                           | `cal 9 2023`                                                 |
| `bc`                         | 计算器                                               | `scale=3`，变更精度（小数点），`quit`退出                    |
| `<C-C>`                      | 中断                                                 |                                                              |
| `<C-D>`                      | 离开命令行（同`exit`)                                |                                                              |
| `--help`, `man`, `info`      | 在线帮助                                             |                                                              |
| `who`                        | 查看在线用户                                         |                                                              |
| `netstat`                    | 网络联机状态                                         | `netstat -a`                                                 |
| `ps`                         | 进程状态 process                                     | `ps -aux`                                                    |
| `sync`                       | 将为保存的数据存入硬盘（内存与硬盘的同步）           | `sync; sync; sync; shutdown`                                 |
| `shutdown`                   | 关机                                                 | `shutdown [-krhc] [时间] [警告信息]`<br>`shutdown -c `取消进行中的shutdown<br>`shutdown -k`只是警告，并不关机 |
| `reboot`, `halt`, `poweroff` | 系统重启、系统停止、关闭电源（均调用`systemctl`）    |                                                              |

---

### 在线帮助

- `--help`
- `man`
  - 查找命令、配置文件甚至是系统函数和库的帮助文档 *man-page*
  - **section number** 章节代号
    - `man-page` 左上角括号内的代号具有不同含义，表示不同章节下的帮助文档，例如——`1-用户命令, 5-配置文件或某些文件格式, 8-admin命令`
    - `man man` 查看代号含义
    - `man 1 date` 查看 section 1 （表示用户命令）下的`date`的 *man-page*
    - `man -f` & `whatis` 查找词条有哪些相关的 *man-page*
    - `man -k` & `apropos` 根据 key word 搜索 *man-page*
  - 文档位置：`/usr/share/man`
  - 配置：`/etc/man_db.conf /etc/man.config /etc/manpath.conf`
- `info`
  - 以链接的形式组织帮助文档（树状，文档内有链接可供跳转）
  - 文档位置：`/usr/share/info`

- `/usr/share/doc`

# Part 2 文件、目录与磁盘格式

## C5 文件权限与目录配置

### 文件属性

- 身份：用户 User、用户组 Group、其他人 Other
  - 用户账户一般在`/etc/passwd`，密码在`/etc/shadow`
  - `chgrp` change group `chgrp <group> <file-or-dir>`
  - `chown` change owner `chown [-R] [user][:][group] <file-or-dir> `
    - `chown :grp file` 等价于 `chgrp grp file`
    - `-R` 递归改
- 文件权限 `ls -l`

![](https://picgo-1301429536.cos.ap-nanjing.myqcloud.com/img/20231001204926.png)

![](https://picgo-1301429536.cos.ap-nanjing.myqcloud.com/img/20231001204958.png)

- 修改文件权限、SUID、SGID、SBIT等特性

  - `chmod <mod> <file-or-dir>`

  - `-R` 递归改
  
  - `<mod>` 两种表示：数字和符号表示
- 数字：`<mod>=abc`，三位权限数字每一位分别对应用户、组、其他人所拥有的权限，其中每一位的计算方法：rwx 分别对应 421，每一位权限数字为所拥有的权限对应数字之和。*e.g*. `chmod 700`  中 **User** 的权限数字为 `7=4+2+1` 拥有 **rwx** 即全部权限
  
- 符号 *e.g.* `chmod u=rwx,go=rx` 表示 **user** 拥有 rwx，**group** 和 **others** 拥有 rx

![](https://picgo-1301429536.cos.ap-nanjing.myqcloud.com/img/20231001215825.png)

- 权限说明
  - 理解：抽屉与铁盒的比喻——目录相当于抽屉，文件相当于抽屉中的铁盒
  - 目录 rw 针对目录内文件名，包括文件的删除、创建、重命名和移动等
  - 目录 x 表示可以 `cd`
  - 文件 rw 仅针对文件内容，不表示文件能否被删除和创建   

- 文件类型<a name="file-type"></a>
  - `[-]` ***regular*** 常规文件
    - 纯文本、二进制、数据文件
    
  - `[d]` ***directory*** 目录文件
  
  - `[l]` ***link*** 链接文件
  
  - `[b]` ***block*** 块读写-设备文件（可按块随机读写的设备，*e.g.* 硬盘软盘）
  
  - `[c]` ***character*** 字符/串行端口-设备文件（*e.g.* 键盘鼠标）
  
  - `[s]` ***sockets*** 数据接口文件
  
  - `[p]` ***pipe/FIFIO*** 数据输送文件
  
### 目录配置

- 规范依据—— **FHS** *FileSystem Hierarchy Standard*
  - 目录分类：横向——static、variable，纵向——shareable、unshareable
  - 三层主目录：`/` `/usr` `/var`

![](https://picgo-1301429536.cos.ap-nanjing.myqcloud.com/img/20231001210245.png)

## C6 文件与目录管理

| 命令                 | 说明                                                 | 示例                                                         |
| -------------------- | ---------------------------------------------------- | ------------------------------------------------------------ |
| `cd`                 | Change directory                                     | `cd -` 切换到上一个工作目录                                  |
| `ls`                 | list file and directory                              | `ls -d <dir>` 显示目录本身的信息而不要展开<br>`-a` 全部，包括隐藏文件<br>`-l` 详细内容<br>`-i` 列出 [*inode*](#C7 磁盘与文件系统管理) 号码 |
| `pwd`                | Print working directory                              | `pwd -P` 显示链接后真实的当前工作目录                        |
| `mkdir` `rmdir`      | 创建、删除空目录                                     | `mkdir -p ./d1/d2` 创建目录2同时创建路径上的目录1<br>`mkdir -m <mod> ./dir` 指定新创建的 dir 的[权限mod](#C5 文件权限与目录配置) |
| `cp`                 | 复制                                                 | `cp [-iprs] <src1> <src2> ... <dst>`<br>`-i` *interactive* 询问是否覆盖<br>`-p` *permission* 权限也复制<br>`-r` *recursive* 递归<br>`-s` *symbolic link* 创建符号链接<br>`-l` *hard link* 创建硬链接<br>`-u` *update* dst 比 src 新时才复制，常用于备份<br>`-d` 复制 slink 文件本身而非原文件 |
| `mv`                 | move                                                 | `-i` interactive 询问是否覆盖<br>`-u` update                 |
| `basename` `dirname` | 获取文件名、路径名                                   | `dirname <file or dir>`                                      |
| `chattr`             | 修改文件隐藏属性 *attribute*（权限ext2/3/4文件系统） |                                                              |
| `lsattr`             | 显示文件隐藏属性 *attr*ibute（权限ext2/3/4文件系统） |                                                              |
| `file`               | 显示[文件类型](#file-type)                           | `file <file-or-dir>`                                         |
| `which`              | 查找命令完整文件名（基于$PATH)                       | `which -a <cmd>` 查找所有`<cmd>`实例                         |
| `touch`              | 修改文件时间或新建文件                               | `touch [-acdmt] <file>`<br>`-d` 指定date，`-t`指定时间       |

---

### 文件读取

- `cat` 读取文件内容并打印到输出
  - `tac`：`cat` 反转
  - `nl` 显示行号的 `cat`
- 翻页读取 `more` & `less`
- 截断读取 `head -n <num> <file>` 读取文件前`<num>`行
  - `-f` 实时刷新（当文件动态更新时），按 `ctrl+c` 退出 ，可以用于 `tail -n 5 /var/log/message` 来动态的显示实时日志信息
  - `tail` 读取后面几行

- 非纯文本文件读取 `od` *octal dump*

  - `od -t <type> <file>`

  - `<type>` 可由下面几种组合而成
- `c` ASCII、`d[size]` decimal、`f[size]` float、`o[size]` octal、`x[size]` hex
  
- e.g. `od -t oCc /etc/issue`，其中`size = C`表示 Char 类型占据的字节数

### 文件时间

- `mtime` *modifed time* 上次修改内容的时间
  - `ls -l`（ls 默认显示 mtime）
  - `touch -m`
- `atime` *access time* 上次读取的时间
  - `ls -l --time=atime`
  - `touch -a`
- `ctime` *last status time* 上次更新状态（文件属性、权限）的时间
  - `ls -l --time=ctime`
- **creation time** 文件创建时间
  - `ls -l --time=birth`
  - `touch -c`

### 默认权限

- 权限掩码 `umask`

  - `umask [-S]` 查看当前掩码（`-S`表示以符号的形式）

  - `umask 002` 设置掩码为`002`
  - 默认umask一般在bashrc之类的配置文件中指定

- 默认权限 `default`

  - 文件：`-rw-rw-rw-`
  - 目录：`drwxrwxrwx`

- 新建文件/文件夹的权限 = `default` - `umask`

### 特殊权限

- **SUID** *Set User ID*
  - `u+s`：`s`替换`owner`部分的`x`位置
  - 作用对象：二进制文件
  - 执行该文件时用户暂时获得该文件拥有者的权限
- **SGID** *Set Group ID*
  - `g+s`：`s`替换`group`部分的`x`位置
  - 作用对象：二进制文件、目录
  - 执行二进制文件时用户暂时获得该文件所属用户组的权限
  - 在该目录下用户的有效用户组暂时变为目录的Group，且建立的文件所属的用户组也与目录的用户组一致
- **SBIT** *Sticky Bit*
  - `o+t`：`t`替换`other`部分的x位置
  - 作用对象：目录
  - 对该目录有写入权限的用户在该目录下新建的文件仅能被该文件/目录的建立者或者root删除

- 设置方式

  - 数字法
    - SUID=4, SGID=2, SBIT=1
    - *e.g.* `chmod 4755 <file>` ~ `-rwsr-xr-x`

  - 符号法
    - `u+s` `g+s` `o+t`
    - *e.g.* `chmod u=rwxs,g=rwxs <file>` `chmod o=rwxt <dir>`
    - 注意：因为`s`、`t`是要覆盖`x`位置的，如果原来的`x`不存在，则`s`、`t`会显示为`S`、`T`，这和空值是等价的

### 文件查找

- `whereis` 基于几个特定目录
  - `-l` 列出查找目录
  - `-m` 仅搜索 `man`，`-b` 仅搜索 `bin`，`-s` 仅搜索 `src`，`-u` 搜索前述内容之外的内容
- `locate` 基于数据库
  - 数据库：**/var/lib/mlocate**
  - `updatedb` 根据 `/etc/updatedb.conf` 更新 `/var/lib/mlocate` 数据库文件
- `find` 基于文件系统（功能丰富但硬盘IO高）

## C7 磁盘与文件系统管理

| 命令       | 说明                                                         | 示例                                                       |
| ---------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| *`<dev>`   | 表示设备文件名                                               | `/dev/sda1`                                                |
| *`<mount>` | 表示挂载点目录名                                             | `/`                                                        |
| `dumpe2fs` | 查询**ext**系列文件系统的超级区块信息                        | `dumpe2fs [-bh] <dev>`<br>`-h` 仅列出超级区块的内容        |
| `xfs_info` | 查询**xfs**文件系统信息                                      | `xfs_info <mount-or-dev>`                                  |
| `blkid`    | 查询块设备信息（可以用于查看分区的文件系统等信息）           |                                                            |
| `df`       | 查看文件系统的整体磁盘使用量（基于查询超级区块信息，快）     | `df [-ahikHTm] <dir-or-file>`<br>`-h` 便于阅读             |
| `du`       | 估算某个目录或文件的磁盘使用量（基于文件系统，搜索算法，慢） | `du [-ahskm] <file-or-dir>`<br>`-s` 仅列出目录总量，不递归 |

### 磁盘分区与文件系统概述

- *e.g.* `/dev/sda1` 表示第一个被扫描到的磁盘设备a上的第1个分区，`/dev/vdb3` 表示第2个被扫描到的虚拟磁盘设备b的第3个分区

- 一个分区对应一个文件系统
- 索引式文件系统组成
  - **超级区块** *superblock*：记录文件系统整体元数据（inode 和 datablock 数目，使用量，文件系统格式等）
  - **inode** *indexnode*：记录文件属性，对应数据区块位置和顺序
  - **数据区块** *datablock* ：存放文件内容

- 索引式 FS （如 `ext2`）能够通过读取inode一次性获取所有数据区块的位置和顺序，可以方便磁盘进行顺序读取。而 FAT FS 需要读取一个区块才能知道下一个区块的位置（链表），因此如果文件数据过于离散会导致读写头反复横跳，需要时不时进行**碎片整理**以恢复效率。

### ext2 文件系统

- 文件系统格式化后就固定好`inode`和`datablock`，这些`inode`和`datablock`资源后续是否被占用由`bitmap`来登记

- 认识 ext2
  - 每一个ext2文件系统都有一个启动扇区，如此一来就不用覆盖整块磁盘唯一的MBR也能制作多重引导
  - 超级区块一般只出现在 **Group1**，其他 Group 中的超级区块仅作为备份

<img src="https://picgo-1301429536.cos.ap-nanjing.myqcloud.com/img/ext2-structure.drawio.png" alt="ext2 文件系统结构"  />

- 认识 inode 和 datablock 的关系

![inode 存储结构](https://picgo-1301429536.cos.ap-nanjing.myqcloud.com/img/image-20231104113234794.png)

- 认识目录文件

  - 目录也相当于一个文件，也占用一个 inode，只是其 datablock 中记录的是所包含的子文件的 inode 和 filename 的 pair

  - 文件 inode 不记录其名字，名字由父目录记录
  - **挂载点**即为文件系统/分区的入口

- 日志式文件系统（ext3、ext4）
  - 增加一个**日志记录区块**，记录每次写入操作的内容
  - 只有在`meta`和`data`均写入完成后才在日志中标记为已完成
  - 解决突发故障情况下，元数据区块和数据区块不一致问题

### XFS 文件系统

> ext2 需要一开始就分配好inode和datablock，格式化速度超慢，而xfs是基于动态分配的，更加适合高容量磁盘和巨型文件。

- 数据区 *data section*
  - 同 ext 系列存储区域群组类似，只是基于动态配置 inode 和 datablock
- 文件系统活动登陆区 *log section*
  - 类似于日志区
- 实时运行区 *realtime section*
  - 新建文件会先写入这里的 **extent 区块**然后再写入数据区

### Linux VFS

> 不同分区如果有着不同的文件系统，那么怎么保证用户访问文件的一致性体验呢？答案是VFS已经帮你在不知不觉中做好了！

- 虚拟文件系统切换机制 *Virtual FileSystem Switch*
- 自动管理所有文件系统，省去用户自行设置读取文件系统的行为

<img src="https://picgo-1301429536.cos.ap-nanjing.myqcloud.com/img/image-20231104120207705.png" alt="VFS 架构"  />

### 文件链接

