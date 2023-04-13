# 命令格式

Shell 俗称壳，用来区别于核。是围绕在 Linux 内核之外的一个“壳”程序，用户在操作系统上完成的所有任务都是通过 Shell 与 Linux 系统内核的交互来实现的。

> 当用户登录系统后，Shell  命令行启动，“#”表示登录的用户是超级用户，“$”表示登录的用户是普通用户。

**语法**：

Shell 命令的一般格式为：

```shell
# 命令行
command [options][arguments]
# 命令 [选项][参数]
```

command : 命令，表示可执行的二进制格式文件或者脚本程序文件。

options : 选项，可以调整命令的运行，[ ] 中的内容可以省略，一般命令支持一个或者多个选项。

arguments : 参数，表示命令作用的对象，可以是文件名、用户/组名、IP 等，支持多个参数。

**作用**：

    为了能够使系统识别用户输入的脚本命令所设置的命令规范。

**案例**：

```shell
echo 'Hello World!' // 在屏幕上实现标准输出字符串等信息
```

# 一、常用操作以及概念

[**命令大全**]([Linux常用命令(手册)大全 - C语言网](https://www.dotcpp.com/course/linuxcmd/))

## 快捷键

- Tab：命令和文件名补全；

- Ctrl + C：中断正在运行的程序；

- Ctrl + D：结束键盘输入（End Of File，EOF）；

## 求助

### 1. --help

指令的基本用法与介绍。

### 2. man

[manual 的缩写，将指令的具体信息（帮助信息）显示出来]([Linux man命令:查看帮助信息 - Linux命令 - C语言网](https://www.dotcpp.com/course/661))

当执行 `man date` 时，有 DATE(1) 出现，其中，数字代表指令的类型，常用如下：

| 代号  | 类型                          |
| --- | --------------------------- |
| 1   | 用户在 shell 环境中可以操作的指令或者可执行文件 |
| 5   | 配置文件                        |
| 8   | 系统管理员可以使用的管理指令              |

### 3. info

info 与 man 类似，但 info 将文档分成一个个页面，每个页面可以进行跳转。

### 4. doc

/usr/share/doc 存放着软件的一整套说明文件。

## 关机

### 1. who

[在关机前需要先使用 who 命令查看有没有其他用户在线]([Linux who命令:显示目前登录用户信息 - Linux命令 - C语言网](https://www.dotcpp.com/course/662))

### 2. sync

为了加快对磁盘文件的读写速度，位于内存中的文件数据不会立即同步到磁盘上，因此关机之前需要先进行 sync 同步操作。

### 3. shutdown

[用于安全地关闭或重启系统]([Linux shutdown命令:关机或重启 - Linux命令 - C语言网](https://www.dotcpp.com/course/670))

```html
## shutdown [-krhc] 时间 [信息]
-k: 不会关机，只是发送警告信息，通知所有在线的用户
-r: 将系统的服务停掉后就重新启动
-h: 将系统的服务停掉后就立即关机
-c: 取消已经在进行的 shutdown 指令内容
```

## PATH

可以在环境变量 PATH 中声明可执行文件的路径，之间用 `冒号 :` 分隔。

```html
/usr/local/bin:/usr/bin:/usr/local/sbin
```

## sudo

sudo 允许一般用户使用 root 可执行的命令，不过只有在 /etc/sudoers 配置文件中添加的用户才可以使用该指令。

## 包管理工具

RPM 和 DPKG 为最常见的两类软件包管理工具：

- RPM（Redhat Package Manager），最早由 Red Hat 公司制定实施，随后被 GNU 开源操作系统接受并成为很多 Linux 系统 (RHEL) 的既定软件标准。

- 与 RPM 进行竞争的是基于 Debian 操作系统 (Ubuntu) 的 DEB 软件包管理工具 DPKG，全称为 Debian Package，功能方面与 RPM 相似。

> YUM 基于 RPM，具有依赖管理功能，并具有软件升级的功能。

### 1. 发行版

Linux 发行版是 Linux 内核及各种应用软件的集成版本。

| 基于的包管理工具 | 商业发行版   | 社区发行版           |
|:--------:|:-------:|:---------------:|
| RPM      | Red Hat | Fedora / CentOS |
| DPKG     | Ubuntu  | Debian          |

## VIM 三个模式

- 一般指令模式（Command mode）：默认模式，用于移动游标；

- 编辑模式（Insert mode）：按 “i” 等键之后进入，用于文本编辑；

- 指令列模式（Bottom-line mode）：按键 “:” 之后进入，用于保存退出等操作；

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-18-10-46-08-image.png" title="" alt="" data-align="center">

### 1. 离开或保存

在指令列模式下，以下命令用于离开或保存文件：

| 命令   | 作用                              |
|:----:|:-------------------------------:|
| :w   | 写入磁盘                            |
| :w!  | 当文件为只读时，写入磁盘。能不能写入，与用户对该文件的权限有关 |
| :q   | 离开                              |
| :q!  | 强制离开不保存                         |
| :wq  | 写入磁盘后离开                         |
| :wq! | 强制写入磁盘后离开                       |

## GNU

GNU 计划，译为革奴计划，它的目标是创建一套完全自由的操作系统，称为 GNU，其内容软件完全以 GPL 方式发布。其中 GPL 全称为 GNU 通用公共许可协议，包含了以下内容:

- 以任何目的运行此程序的自由；

- 再复制的自由；

- 改进此程序，并公开发布改进的自由。

# 二、磁盘

## 磁盘接口

### 1. IDE

IDE(ATA)全称 Advanced Technology Attachment，接口速度最大为 133MB/s，因为并口线的抗干扰性太差，且排线占用空间较大，不利电脑内部散热，已逐渐被 SATA 所取代。

![](D:\Download\MarkText\workspace\image\2022-08-19-10-57-39-image.png)

### 2. SATA

SATA 全称 Serial ATA，也就是使用串口的 ATA 接口，抗干扰性强，且对数据线的长度要求比 ATA 低很多，支持热插拔等功能。SATA-II 的接口速度为 300MiB/s，而新的 SATA-III 标准可达到 600MiB/s 的传输速度。SATA 的数据线也比 ATA 的细得多，有利于机箱内的空气流通，整理线材也比较方便。

### 3. SCSI

SCSI 全称是 Small Computer System Interface(小型机系统接口)，经历多代的发展，从早期的 SCSI-II 到目前的 Ultra320 SCSI 以及 Fiber-Channel(光纤通道)，接口型式也多种多样。SCSI 硬盘广为工作站级个人电脑以及服务器所使用，因此会使用较为先进的技术，如碟片转速 15000rpm 的高转速，且传输时 CPU 占用率较低，但是单价也比相同容量的 ATA 及 SATA 硬盘更加昂贵。

### 4. SAS

SAS(Serial Attached SCSI)是新一代的 SCSI 技术，和 SATA 硬盘相同，都是采取序列式技术以获得更高的传输速度，可达到 6Gb/s。此外也透过缩小连接线改善系统内部空间等。

## 磁盘的文件名

Linux 中每个硬件都被当做一个文件，包括磁盘。磁盘以磁盘接口类型进行命名，常见磁盘命名如下：

- IDE 磁盘：/dev/hd[a-d]

- SATA / SCSI / SAS 磁盘：/dev/sd[a-p]

其中文件名后面的序号的确定与系统检测到磁盘的顺序有关，而与磁盘所插入的插槽位置无关。

# 三、分区

## 分区表

磁盘分区表主要有两种格式，一种是限制较多的 `MBR` 分区表，一种是较新且限制较少的 `GPT` 分区表。 

### 1. MBR

MBR 中，第一个扇区最重要，里面有主要开机记录 (Master boot record, MBR) 及分区表(partition table)，其中主要开机记录占 446 bytes，分区表占 64 bytes。

分区表只有 64 bytes，最多只能存储 4 个分区，这 4 个分区为**主分区** (Primary) 和**扩展分区** (Extended) 。其中扩展分区只有一个，它使用其它扇区用记录额外的分区表，因此通过扩展分区可以分出更多分区，这些分区称为**逻辑分区**。

Linux 也把分区当成文件，分区文件的命名方式为: **磁盘文件名 + 编号**，例如 /dev/sda1。注意，逻辑分区的编号从 5 开始。

### 2. GPT

不同的磁盘有不同的扇区大小，例如 512 bytes 和最新磁盘的 4 k。GPT 为了兼容所有磁盘，在定义扇区上使用逻辑区块地址(Logical Block Address, LBA)，LBA 默认大小为 512 bytes。

GPT 第 1 个区块记录了主要开机记录(MBR)，紧接着是 33 个区块记录分区信息，并把最后的 33 个区块用于对分区信息进行备份。这 33 个区块第一个为 GPT 表头记录，这个部份记录了分区表本身的位置与大小和备份分区的位置，同时放置了分区表的校验码 (CRC32)，操作系统可以根据这个校验码来判断 GPT 是否正确。若有错误，可以使用备份分区进行恢复。

GPT **没有扩展分区**概念，**都是主分区**，每个 LAB 可以分 4 个分区，因此总共可以分 4 * 32 = 128 个分区。

MBR 不支持 2.2 TB 以上的硬盘，GPT 则最多支持到 233 TB = 8 ZB。

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-19-11-10-55-image.png" title="" alt="" data-align="center">

## 开机检测程序

### 1. BIOS

BIOS(Basic Input/Output System，基本输入输出系统)，它是一个固件(嵌入在硬件中的软件)，BIOS 程序存放在断电后内容不会丢失的只读内存中。

- BIOS 是开机时计算机执行的第一个程序，这个程序知道可以开机的磁盘，并读取磁盘第一个扇区的主要开机记录(MBR)，由主要开机记录(MBR)执行其中的开机管理程序，这个开机管理程序会加载操作系统的核心文件。

- 主要开机记录(MBR)中的开机管理程序提供以下功能: 选单、载入核心文件以及转交其它开机管理程序。转交这个功能可以用来实现了多重引导，只需要将另一个操作系统的开机管理程序安装在其它分区的启动扇区上，在启动开机管理程序时，就可以通过选单选择启动当前的操作系统或者转交给其它开机管理程序从而启动另一个操作系统。

下图中，第一扇区的主要开机记录(MBR)中的开机管理程序提供了两个选单: M1、M2，M1 指向了 Windows 操作系统，而 M2 指向其它分区的启动扇区，里面包含了另外一个开机管理程序，提供了一个指向 Linux 的选单。

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-19-11-13-24-image.png" title="" alt="" data-align="center">

安装多重引导，最好先安装 Windows 再安装 Linux。因为安装 Windows 时会覆盖掉主要开机记录(MBR)，而 Linux 可以选择将开机管理程序安装在主要开机记录(MBR)或者其它分区的启动扇区，并且可以设置开机管理程序的选单。

### 2. UEFI

**BIOS 不可以读取 GPT 分区表，而 UEFI 可以。**

# 四、文件系统

## 分区与文件系统

对分区进行格式化是为了在分区上建立文件系统。一个分区通常只能格式化为一个文件系统，但是磁盘阵列等技术可以将一个分区格式化为多个文件系统。

## 组成

最主要的几个组成部分如下：

- inode : 一个文件占用一个 inode，记录文件的属性，同时记录此文件的内容所在的 block 编号；

- block : 记录文件的内容，文件太大会占用多个 block 。

- superblock : 记录文件系统的整体信息，包括 inode 和 block 的总量、剩余量、使用量、以及文件系统的格式与相关信息等；

- block bitmap : 记录 block 是否被使用的位域。

![](D:\Download\MarkText\workspace\image\2022-08-20-14-23-22-image.png)

## 文件读取

对于 Ext2 文件系统，当要读取一个文件的内容时，先在 inode 中查找文件内容所在的所有 block ，然后把所有 block 的内容读出来。

![](D:\Download\MarkText\workspace\image\2022-08-20-14-24-53-image.png)

而对于 FAT 文件系统，它没有 inode ，每个 block 中存储着下一个 block 的编号。

![](D:\Download\MarkText\workspace\image\2022-08-20-14-25-57-image.png)

## 磁盘碎片

指一个文件内容所在的 block 过于分散。

## block

在 Ext2 文件系统中所支持的 block 大小有 1K、2K 及 4K 三种，不同的大小限制了单个文件系统的最大大小。

| 大小     | 1KB  | 2KB   | 4KB  |
|:------:|:----:|:-----:|:----:|
| 最大单一文件 | 16GB | 256GB | 2TB  |
| 最大文件系统 | 2TB  | 8TB   | 16TB |

一个 block 只能被一个文件所使用的，未使用的部分直接浪费了。因此如果需要存储大量的小文件，最好选用较小的 block。

## inode

inode 具体包含以下信息：

- 权限（ read / write / excute ）；

- 拥有者与群主（ owner / group ）;

- 容量;

- 建立或状态改变的时间（ ctime ）;

- 最近一次的读取时间（ atime ）;

- 最近修改的时间（ mtime ）;

- 定义文件特性的旗标（ flag ），如 SetUID... ;

- 该文件真正内容的指向（ pointer ）。

inode 具有以下特点：

- 每个 inode 大小均固定位 128 bytes（新的 ext4 与 xfs 可设定到 256 bytes）；

- 每个文件都仅会占用一个 inode。

inode 中记录了文件内容所在的 block 编号，但是每个 block 非常小，一个大文件随便都要几十万的 block。而一个 inode 大小有限，无法直接引用这么多 block 编号，因此引入了间接、双间接、三间接引用。间接引用是指，让inode 记录的引用 block 块记录引用信息。

![](D:\Download\MarkText\workspace\image\2022-08-20-14-46-09-image.png)

## 目录

建立一个目录时，会分配一个 inode 与至少一个 block。block 记录的内容是目录下所有文件的 inode 编号以及文件名。

文件的 inode 本身不记录文件名，记录在目录中，因此新增、删除、更改文件名操作与目录的 x 权限有关。

## 日志

如果突然断电，那么文件系统会发生错误，如断电前只修改了 block bitmap，而还没有将数据真正写入 block 中。

ext3/ext4 文件系统引入了日志功能，可以利用日志来修复文件系统。

## 挂载

挂载利用目录作为文件系统的进入点，即进入目录之后就可以读取文件系统的数据。

## 目录配置

为了使不同 linux 发行版本的目录结构保持一致性，Filesystem Hierarchy Standard（FHS）规定了 Linux 的目录结构。基础目录如下：

- / (root, 根目录)

- /usr (unix software resource): 所有系统默认软件都会安装到这个目录

- /var (variable): 存放系统或程序运行过程中的数据文件

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-22-08-47-18-image.png" title="" alt="" data-align="inline">

# 五、文件

## 文件属性

用户分为三种：文件拥有者、群组以及其他人，不同的用户有不同的文件权限。

使用 `ls` 查看一个文件时，会显示一个文件的信息，例如：

` drwxr-xr-x. 3 root root 17 May 6 00:14 .config `

- drwxr-xr-x : 文件类型以及权限，第 1 位为文件类型字段，后 9 位为文件权限字段；

- 3 : 链接数；

- root : 文件拥有者；

- root : 所属群组；

- 17 : 文件大小；

- May 6 00:14 : 文件最后被修改的时间；

- .config : 文件名。

常见的文件类型及其含义有：

- d : 目录；

- `-` : 文件；

- l : 链接文件。

9 位的文字权限字段中，每 3 个为一组，每一组分别代表对文件拥有者、所属群组以及其他人的文件权限。一组权限中的 3 位分别为 r、w、x 权限，表示可读、可写、可执行。

文件时间有以下三种：

- modification time（mtime）: 文件的内容更新就会更新；

- status time（ctime）: 文件的状态（权限、属性）更新就会更新；

- access time（atime）: 读取文件时就会更新。

<<<<<<< HEAD
## 文件与目录操作

常用命令

- `ls`（list files）: 列出目录及文件名

- `cd`（change directory）：切换目录

- `pwd`（print work directory）：显示当前目录

- `mkdir`（make directory）：创建一个新目录

- `rmdir`（remove directory）：删除一个空目录

- `cp`（copy file）: 复制文件或目录

- `rm`（remove）: 删除文件或目录

- `mv`（move file）: 移动文件与目录，或修改文件与目录名称
=======
## 文件与目录的基本操作
>>>>>>> 8ecdf2e35d8794f235f84b4a00f3623796ae7c01

### 1. ls

[列出当前工作文件或者目录的信息，目录的信息就是其中包含的文件]([Linux ls命令:显示指定工作目录下的内容 - Linux命令 - C语言网](https://www.dotcpp.com/course/655))

<<<<<<< HEAD
```shell
[root@www ~] ls [-aAdfFhilnrRSt] 目录名称
[root@www ~] ls [--color={never,auto,always}] 目录名称
[root@www ~] ls [--full-time] 目录名称
```

- `-a` ：全部文件，连同隐藏文件一起列出来；

- `-d` ：仅目录本身；

- `-l` ：长数据串列出，包含文件属性与权限等数据。

=======
```cs
## ls [-aAdfFhilnrRSt] file | dir
-a : 列出全部文件
-d : 仅列出目录本身
-l : 以长数据串行列出，包含文件的属性与权限等数据
```

>>>>>>> 8ecdf2e35d8794f235f84b4a00f3623796ae7c01
### 2. cd

[更换当前目录]([Linux cd命令:切换工作目录 - Linux命令 - C语言网](https://www.dotcpp.com/course/657))

<<<<<<< HEAD
```shell
# 绝对路径
[root@www ~] cd /root/runoob/
# 相对路径
[root@www ~] cd ./runoob/
# /root 目录
[root@www runoob] cd ~
# 上一级目录
[root@www ~] cd ..
=======
```cs
cd [相对路径或绝对路径]
>>>>>>> 8ecdf2e35d8794f235f84b4a00f3623796ae7c01
```

### 3. mkdir

[创建目录]([Linux mkdir命令:创建目录 - Linux命令 - C语言网](https://www.dotcpp.com/course/679))

<<<<<<< HEAD
```shell
mkdir [-mp] test1/test2/test3/test4

[root@www tmp] mkdir -m 711 test2
```

- `-m` ：配置文件的权限！直接配置；
- `-p` ：包含上一级目录，递归创建。

### 4. rmdir

```shell
 rmdir [-p] 目录名称
```

- `-p` ：删除多级**空目录**。
=======
```cs
## mkdir [-mp] 目录名称
-m : 配置目录权限
-p : 递归创建目录
```

### 4. rmdir

删除目录，目录必须为空

```cs
## rkdir [-p] 目录名称
-p : 递归删除目录
```
>>>>>>> 8ecdf2e35d8794f235f84b4a00f3623796ae7c01

### 5. touch

[更新文件时间或者建立新文件]([Linux touch命令:修改文件的时间 - Linux命令 - C语言网](https://www.dotcpp.com/course/684))

```cs
## touch [-acmdt] filename
-a : 更新 actime
-c : 更新 ctime ，若文件不存在则不建立新文件
-m : 更新 mtime 
-d : 后接当前日期或更新日期，可使用 --date="日期或时间"
-t : 后接更新日期而不使用当前日期，格式为 [YYYYMMDDhhmm]
```

### 6. cp

[复制文件，若源文件有两个以上，则目的文件一定要是目录才行]([Linux cp命令:复制文件或目录 - Linux命令 - C语言网](https://www.dotcpp.com/course/687))

<<<<<<< HEAD
```shell
[root@www ~] cp [-adfilprsu] 来源档(source) 目标档(destination)
[root@www ~] cp [options] source1 source2 source3 .... directory

## cp [-adfilprsu] source destination
-a : 相当于 -dr --preserve=all
--preserve=all : 除了 -p 的权限相关参数外，还加入 SELinux 的属性、links、xattr 等
```

- `-a` ：相当 **-pdr**；

- `-d` ：若来源档为链接档(link file)，则复制链接档属性而非文件本身；

- `-f` ：强制(force)，若目标文件已经存在且无法开启，则移除后再尝试一次；

- `-i` ：若目标档(destination)已经存在时，覆盖时会先询问；

- `-l` ：进行硬式链接(hard link)的链接档创建，而非复制文件本身；

- `-p` ：同文件属性一起复制；

- `-r` ：递归(recursion)复制；

- `-s` ：复制成为符号链接档 (symbolic link)文件；

- `-u` ：若 destination 比 source 旧才升级 destination。

```cs

```

### 7. rm

[删除某个目录及其下的所有文件及子目录，而对于链接文件，只是断开了链接，原文件内容保持不变](https://www.dotcpp.com/course/683)

```shell
rm [-fir] 文件或目录
```

- `-f` ：忽略不存在的文件，不会出现警告信息；
- `-i` ：删除前会询问；
- `-r` ：递归删除。

=======
```cs
## cp [-adfilprsu] source destination
-a : 相当于 -dr --preserve=all
-d : 若来源文件为链接文件，则复制链接文件属性而非文件本身
-i : 若目标文件已存在，则覆盖前会先询问
-p : 连同文件的属性一起复制过去
-r : 递归持续复制
-u : desition 比 source 旧才更新 desition，或 desition 不存在的情况下才复制

--preserve=all : 除了 -p 的权限相关参数外，还加入 SELinux 的属性、links、xattr 等
```

### 7. rm

[删除某个目录及其下的所有文件及子目录，而对于链接文件，只是断开了链接，原文件内容保持不变]([Linux rm命令:删除文件或目录 - Linux命令 - C语言网](https://www.dotcpp.com/course/683))

```cs
## rm [-fir] 文件或目录
-r : 递归删除
```

>>>>>>> 8ecdf2e35d8794f235f84b4a00f3623796ae7c01
### 8. mv

[将文件或目录改名，或移动文件]([Linux mv命令:移动或重命名文件或目录 - Linux命令 - C语言网](https://www.dotcpp.com/course/690))

<<<<<<< HEAD
```shell
[root@www ~] mv [-fiu] source destination
[root@www ~] mv [options] source1 source2 source3 .... directory
```

- `-f` ：不会询问而直接覆盖；
- `-i` ：询问是否覆盖；
- `-u` ：若目标文件已存在，且 source 比较新，才会升级 (update)。

=======
```cs
## mv [-fiu] source destination
## mv [options] source1 source2 source3 .... directory
-f : force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖
```

>>>>>>> 8ecdf2e35d8794f235f84b4a00f3623796ae7c01
## 修改权限

[chmod 管理文件或目录的权限]([Linux chmod命令:权限管理 - Linux命令 - C语言网](https://www.dotcpp.com/course/699))

可以将一组权限用数字表示，此时一组权限的 3 个位当做二进制数字的位，从左到右每个位的1权值为 4、2、1，即每个权限（读、写、执行）对应的数字权值为 r : 4、w : 2、x : 1。

```cs
## chmod [-R] xyz dirname/filename
```

示例：将 .bashrc 文件的权限修改为 -rwxr-xr--。

```cs
## chmod 745 .bashrc
## chmod u=rwx,g=rx,o=r .bashrc
```

也可以使用符号来限定权限

```cs
## chmod [ugoa] [+-=] [rwx] dirname/filename
- u : 拥有者
- g : 所属群组
- o : 其他人
- a : 所有人
- - : 移除权限
- + : 添加权限
- = : 设定权限

// 为 .bashrc 文件的所有用户添加写权限
## chmod a+w .bashrc
// 为 .bashrc 文件的所有用户移除可执行权限
## chmod a-x .bashrc
```

## 默认权限

- 文件默认权限：文件默认没有可执行权限，因此为 666，即 -rw-rw-rw；

- 目录默认权限：目录必须要能够进入，也就是必须有可执行权限，因此为 777，即 drwxrwxrwx。

可以通过 umask 设置或查看文件的默认权限，通常以掩码的形式来表示，例如 002 表示其他用户的权限去除了一个 2 的权限，也就是写权限，因此建立新文件时默认的权限是 -rw-rw-r-- 

## 目录权限

文件名不是存储在一个文件的内容中，而是存储在一个文件的所在目录中。因此拥有文件的 w 权限并不能对文件名进行修改。

目录存储文件列表，一个目录的权限也就是对其文件列表的权限。

因此，目录的 r 权限表示可以读取文件列表；w 权限可以修改（添加、删除、改文件名）文件列表；x 权限可以让该目录成为工作目录，x 权限是 r 和 w 权限的基础，如果不能使一个目录成为工作目录，也就没办法读取文件列表以及对文件列表进行修改了。

## 链接

```cs
## ln [-sf] source_filename dist_filename
-s : 默认是 hard link，加 -s 为 symbolic link
-f : 如果目标文件存在时，则先删除文件
```

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-25-16-38-18-image.png" title="" alt="" data-align="center">

### 1. 实体链接（硬连接）

- 通过索引节点号来进行连接；

- 允许一个文件拥有多个有效路径名（只有当最后一个连接失效，文件数据块及目录的连接才会被释放）。

    在目录下创建一个条目，记录着文件名与 inode 编号，该编号就是源文件的 inode；

    删除任意一个条目，文件还是存在，只要引用数量不为 0；

    限制：不能跨越文件系统，不能对目录进行链接。

```cs
## ln /etc/a .
## li -i /etc/a a 
34474855 -rw-r--r--. 2 root root 451 Jun 10 2014 a
34474855 -rw-r--r--. 2 root root 451 Jun 10 2014 /etc/a
```

### 2. 符号链接（软连接）

    符号链接文件保存着源文件所在的绝对路径，在读取时会定位到源文件上（类似 Windows 的快捷方式）；当源文件被删除了，链接文件就打不开了。

```cs
## ll -i /etc/crontab /root/crontab2
34474855 -rw-r--r--. 2 root root 451 Jun 10 2014 /etc/crontab
53745909 lrwxrwxrwx. 1 root root 12 Jun 23 22:31 /root/crontab2 -> /etc/crontab
```

## 获取文件内容

### 1. cat

<<<<<<< HEAD
[取得文件内容并输出到标准输出，同时还可以用于合并文件](https://www.dotcpp.com/course/681)

```cs
## cat [-AbEnTv] filename
```

- `-A` ：-vET 的整合选项，可列出一些特殊字符而不是空白；

- `-b` ：列出行号，仅针对非空白行做行号显示；

- `-E` ：显示结尾的断行字节 $ ；

- `-n` ：列印出行号，空白行也有；

- `-T` ：将 [tab] 按键以 ^I 显示出来；

- `-v` ：列出特殊字符。

=======
[取得文件内容并输出到标准输出，同时还可以用于合并文件]([Linux cat命令:连接文件并打印到标准输出设备上 - Linux命令 - C语言网](https://www.dotcpp.com/course/681))

```cs
## cat [-AbEnTv] filename
-n : 打印出行号，连空白行也会有行号，但 -b 不会
```

>>>>>>> 8ecdf2e35d8794f235f84b4a00f3623796ae7c01
### 2. tac

是 cat 的反向操作，从最后一行开始打印

<<<<<<< HEAD
```shell
[root@www ~] tac /etc/issue

Kernel \r on an \m
CentOS release 6.4 (Final)
```

### 3. more

[和 cat 不同的是，它可以一页一页的查看文件内容，适合大文档的查看](https://www.dotcpp.com/course/680)

- `空白键 (space)` ：代表向下**翻一页**；

- `Enter` ：代表向下**翻一行**；

- `/字串` ：代表在这个显示的内容当中，向下搜寻『字串』关键字；

- `:f` ：立刻显示出档名以及目前显示的行数；

- `q` ：立刻离开；

- `b 或 [ctrl]-b`  ：代表**往回翻页**，只对文件有用，对管线无用。

### 4. less

[和 more 类似，但是多了一个向前翻页的功能，用于分页显示文件或其他输出](https://www.dotcpp.com/course/692)

- `空白键`    ：向下翻一页；

- `[pagedown]` ：向下翻一页；

- `[pageup]` ：向上翻一页；

- `/字串` : 向下搜寻；

- `?字串` ：向上搜寻；

- `n` ：重复前一个搜寻 (与 / 或 ? 有关)；

- `N` ：反向重复前一个搜寻 (与 / 或 ? 有关)；

- `q` ：离开。

### 5. head

[查看文件前几行](https://www.dotcpp.com/course/693)
=======
### 3. more

[和 cat 不同的是，它可以一页一页的查看文件内容，适合大文档的查看]([Linux more命令:显示文本文件内容 - Linux命令 - C语言网](https://www.dotcpp.com/course/680))

### 4. less

[和 more 类似，但是多了一个向前翻页的功能，用于分页显示文件或其他输出]([Linux less命令:分页显示文件内容 - Linux命令 - C语言网](https://www.dotcpp.com/course/692))

### 5. head

[查看文件前几行]([Linux head命令:查看文件前几行 - Linux命令 - C语言网](https://www.dotcpp.com/course/693))
>>>>>>> 8ecdf2e35d8794f235f84b4a00f3623796ae7c01

```cs
## head [-n number] filename
-n : 后面接数字，代表显示几行
```

### 6. tail

<<<<<<< HEAD
[head 的反向操作，查看文件后几行](https://www.dotcpp.com/course/694)
=======
[head 的反向操作，查看文件后几行]([Linux tail命令:查看文件后几行 - Linux命令 - C语言网](https://www.dotcpp.com/course/694))
>>>>>>> 8ecdf2e35d8794f235f84b4a00f3623796ae7c01

### 7. od

以字符或者十六进制的形式显示二进制文件
<<<<<<< HEAD

- `-n` ：后接数字，代表显示几行；

- `-f` ：表示持续侦测后面所接档名，要等到按下 [ctrl]-c 才结束 tail 侦测。
=======
>>>>>>> 8ecdf2e35d8794f235f84b4a00f3623796ae7c01
