---
layout: post
title: "更多bash shell命令《Linux命令行与Shell脚本编程大全》" 
date:   2022-8-2 11:29:08 +0800
tags: 《Linux命令行与Shell脚本编程大全》 Linux  
---

# 更多命令

Linux的系统管理命令

## 检测程序

跟踪运行在系统中的程序

### 探测进程

程序运行在系统之上的时候被称为进程

```bash
ps
    PID TTY          TIME CMD
   2753 pts/0    00:00:00 bash
   4496 pts/0    00:00:00 ps
```

> 输出系统上所有程序的许多信息, 默认情况下没有那么多的信息, 控制台下属于当前用户的进程
>
> 显示了程序进程的ID, 运行的终端: TTY, 已经用的CPU时间

Linux中的GUN ps支持三种不同类型的命令分行参数

+ Unix风格 加单破折线
+ BSD风格 不加破折线
+ GUN风格 双破折线

#### Unix风格

原有的Unix系统上继承而来的

![QQ图片20220802163459](https://xusenfeng.github.io/myimages/QQ图片20220802163459.png)

![QQ图片20220802163548](https://xusenfeng.github.io/myimages/QQ图片20220802163548.png)

**常用:**

> -ef: 显示所有的进程
>
> ```bash
> UID          PID    PPID  C STIME TTY          TIME CMD
> root           1       0  0 12:22 ?        00:00:01 /sbin/init splash
> ```
>
> + UID：启动这些进程的用户。
> + PID：进程的进程ID。
> + PPID：父进程的进程号（如果该进程是由另一个进程启动的）。
> + C：进程生命周期中的CPU利用率。
> + STIME：进程启动时的系统时间。
> + TTY：进程启动时的终端设备。
> + TIME：运行进程需要的累计CPU时间。
> + CMD：启动的程序名称

> -l: 获得更多
>
> ```bash
> F S   UID     PID    PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
> 0 S  1000    2753    2727  0  80   0 -  3713 do_wai pts/0    00:00:00 bash
> ```
>
> + F：内核分配给进程的系统标记。
> + S：进程的状态（O代表正在运行；S代表在休眠；R代表可运行，正等待运行；Z代表僵化，进程已结束但父进程已不存在；T代表停止）。
> + PRI：进程的优先级（越大的数字代表越低的优先级）。
> + NI：谦让度值用来参与决定优先级。
> + ADDR：进程的内存地址。
> + SZ：假如进程被换出，所需交换空间的大致大小。
> + WCHAN：进程休眠的内核函数的地址。

#### BSD风格

![QQ图片20220802164612](https://xusenfeng.github.io/myimages/QQ图片20220802164612.png)

![QQ图片20220802164651](https://xusenfeng.github.io/myimages/QQ图片20220802164651.png)

大部分的和Unix的是重合的, 但是在使用的时候会使用不同的格式输出

```bash
$ ps l
F   UID     PID    PPID PRI  NI    VSZ   RSS WCHAN  STAT TTY        TIME COMMAND
4  1000    1088     988  20   0 165480  6104 do_pol Ssl+ tty2       0:00 /usr/libexec/gdm-wayland-sessi
```

+ VSZ：进程在内存中的大小，以千字节（KB）为单位。
+ RSS：进程在未换出时占用的物理内存。
+ STAT：代表当前进程状态的双字符状态码。可以比Unix的单字节码更加仔细的显示进程的状态,



>  第一字节和Unix的一样
>
> + O代表正在运行；
> + S代表在休眠；
> + R代表可运行，正等待运行；
> + Z代表僵化，进程已结束但父进程已不存在；
> + T代表停止

> 第二字节进一步说明进程的状态
>
> + <：该进程运行在高优先级上。
> + N：该进程运行在低优先级上。
> + L：该进程有页面锁定在内存中。
> + s：该进程是控制进程。
> + l：该进程是多线程的。
> + +：该进程运行在前台。

#### GUN长参数

![QQ图片20220802165734](https://xusenfeng.github.io/myimages/QQ图片20220802165734.png)

![QQ图片20220802165756](https://xusenfeng.github.io/myimages/QQ图片20220802165756.png)

```bash
ps --forest
1981 ?        00:00:00 sshd 
3078 ?        00:00:00  \_ sshd 
3080 ?        00:00:00      \_ sshd 
3081 pts/0    00:00:00          \_ bash 
16676 pts/0    00:00:00              \_ ps 
```

显示进程之间的层级关系

### 检测实时进程

ps检测的进程是瞬时的

```bash
$ top
top - 17:21:22 up  4:58,  1 user,  load average: 0.00, 0.00, 0.00
任务: 295 total,   1 running, 294 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.3 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   3889.2 total,    733.1 free,   1354.1 used,   1802.0 buff/cache
MiB Swap:    923.2 total,    923.2 free,      0.0 used.   2253.2 avail Mem 

 进程号 USER      PR  NI    VIRT    RES    SHR    %CPU  %MEM     TIME+ COMMAND                                                      
   1186 jiao      20   0 4194532 308732 131592 S   0.7   7.8   0:40.73 gnome-shell                                                  
   1490 jiao      20   0  290508  38508  29908 S   0.3   1.0   0:16.65 vmtoolsd                                                     
   2727 jiao      20   0  650708  70520  54540 S   0.3   1.8   0:07.45 gnome-terminal-                                              
      1 root      20   0  166048  12312   7668 S   0.0   0.3   0:01.80 systemd                                                      
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.02 kthreadd  
```

> 输出的第一部分显示的是系统的概况：第一行显示了当前时间、系统的运行时间、登录的用户数以及系统的平均负载, 平均负载有三个值, 一分钟, 五分钟, 十五分钟

> 第二行显示了进程概要信息——top命令的输出中将进程叫作任务（task）：有多少进程处在运行、休眠、停止或是僵化状态（僵化状态是指进程完成了，但父进程没有响应）。

> 下一行显示了CPU的概要信息。top根据进程的属主（用户还是系统）和进程的状态（运行、空闲还是等待）将CPU利用率分成几类输出。

> 紧跟其后的两行说明了系统内存的状态。第一行说的是系统的物理内存：总共有多少内存，当前用了多少，还有多少空闲。后一行说的是同样的信息，不过是针对系统交换空间（如果分配了的话）的状态而言的。

> 最后一部分显示了当前运行中的进程的详细列表，有些列跟ps命令的输出类似。
>
> + PID：进程的ID。
> + USER：进程属主的名字。
> + PR：进程的优先级。
> + NI：进程的谦让度值。
> + VIRT：进程占用的虚拟内存总量。
> + RES：进程占用的物理内存总量。
> + SHR：进程和其他进程共享的内存总量。
> + S：进程的状态（D代表可中断的休眠状态，R代表在运行状态，S代表休眠状态，T代表跟踪状态或停止状态，Z代表僵化状态）。
> + %CPU：进程使用的CPU时间比例。
> + %MEM：进程使用的内存占可用内存的比例
> + TIME+：自进程启动到目前为止的CPU时间总量。
> + COMMAND：进程所对应的命令行名称，也就是启动的程序名。

### 结束进程

在Linux之中进程之间通过信号来通讯, 信号就是事先预定好的一个消息, 进程来进行处理, 大多数完善的程序都可以处理标准的Unix信号

| 信号 | 名称 |            描述            |
| :--: | :--: | :------------------------: |
|  1   | HUP  |            挂起            |
|  2   | INT  |            中断            |
|  3   | QUIT |          结束运行          |
|  9   | KILL |         无条件终止         |
|  11  | SEGV |           段错误           |
|  15  | TERM |         尽可能终止         |
|  17  | STOP | 无条件停止运行, 但是不终止 |
|  18  | TSTP |  停止或暂停, 但在后台运行  |
|  19  | CONT |  在STOP和TSTP之后恢复运行  |

#### kill命令

可以通过进程ID给进程发送信号, 默认为TERM

要发送你必须是进程的属主或者root用户

> -s: 支持其他信号

#### killall命令

 支持通过进程名来结束, 也支持通配符

> **注:**root用户使用通配符的时候要小心

## 检测磁盘空间

### 挂载存储媒体

Linux文件系统将所有的磁盘都并入一个虚拟目录下。在使用新的存储媒体之前，需要把它放到虚拟目录下。这项工作称为**挂载**

大多数的多支持自动挂载特定类型的可移动存储媒体

#### mount命令

默认情况下会输出当前挂载的设备

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/3$ mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
```

提示信息:

+ 媒体的设备文件名
+ 媒体挂载到虚拟目录的挂载点
+ 文件系统类型
+ 已挂载媒体的访问状态

挂载命令

```bash
mount -t type device directory
```

> 管理员权限
>
> type: 格式化的文件系统类型
>
> + vfat：Windows长文件系统, 大部分的U盘和软盘
> + ntfs：Windows NT、XP、Vista以及Windows 7中广泛使用的高级文件系统。
> + iso9660：标准CD-ROM文件系统。

`mount -t vfat /dev/sdb1 /media/disk`: 把U盘`/dev/sdb1`挂载到`/media/disk`

常用命令



|    参数     |                          描述                          |
| :---------: | :----------------------------------------------------: |
|     -a      |         挂载/etc/fstab文件中指定的所有文件系统         |
|     -f      |        使mount命令模拟挂载设备，但并不真的挂载         |
|     -F      |       和-a参数一起使用时，会同时挂载所有文件系统       |
|     -v      |           详细模式，将会说明挂载设备的每一步           |
| -I(大写的i) |  不启用任何/sbin/mount.filesystem下的文件系统帮助文件  |
| -l(小写的L) |     给ext2、ext3或XFS文件系统自动添加文件系统标签      |
|     -n      |     挂载设备，但不注册到/etc/mtab已挂载设备文件中      |
|   -p num    |     进行加密挂载时，从文件描述符num中获得密码短语      |
|     -s      |             忽略该文件系统不支持的挂载选项             |
|     -r      |                   将设备挂载为只读的                   |
|     -w      |            将设备挂载为可读写的（默认参数）            |
|  -L label   |                将设备按指定的label挂载                 |
|   -U uuid   |                 将设备按指定的uuid挂载                 |
|     -O      | 和-a参数一起使用，限制命令只作用到特定的一组文件系统上 |
|     -o      |                给文件系统添加特定的选项                |

-o参数允许在挂载文件系统时添加一些以逗号分隔的额外选项。以下为常用的选项。

+ ro：以只读形式挂载。
+ rw：以读写形式挂载。
+ user：允许普通用户挂载文件系统。
+ check=none：挂载文件系统时不进行完整性校验。
+ loop：挂载一个文件。

#### umount

从Linux上删除一个设备, 不能直接从系统上移除, 应该先卸载

```bash
umount [directory | device ]
```

> umount命令支持通过设备文件或者是挂载点来指定要卸载的设备。如果有任何程序正在使用设备上的文件，系统就不会允许你卸载它
>
> 可以使用lsof命令查看谁在使用: `lsof /path/to/device/node` `lsof /path/to/mount/point`
>
> 

### df命令

检查设备还有多少磁盘空间

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/3$ df
文件系统          1K-块     已用    可用 已用% 挂载点
tmpfs            398260     2036  396224    1% /run
/dev/sda3      19946096 16341272 2566284   87% /
tmpfs           1991288        0 1991288    0% /dev/shm
tmpfs              5120        4    5116    1% /run/lock

```

+ 设备的设备文件位置；
+ 能容纳多少个1024字节大小的块；
+ 已用了多少个1024字节大小的块；
+ 还有多少个1024字节大小的块可用；
+ 已用空间所占的比例；
+ 设备挂载到了哪个挂载点上。

常用的参数

> -h: 使用用户便于理解的方式显示

### du命令

发现存储的空间快要使用完了, 可以使用du命令来查看某一个特定的目录, 默认情况下是当前目录

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell$ du
4	./3/abb
20	./3
8	./1
32	.
```

左侧的是文件或者目录占用的目录的磁盘块数, 这个列表是从目录底层开始, 然后按文件, 子目录, 目录逐级向上

> -c: 显示已经列出的文件的总大小

> -h: 用户易读的格式输出

> -s: 只显示每个输出参数总计

## 处理数据文件

### 排序数据

处理大量数据的时候, 使用sort, 按照默认的语言排序规则对文本文件进行排序

一般情况会把数字当做字符进行排序

> -n: 把数字识别为数字而不是字符串

> -M: 按照月份排序

| 单破折号 |              双破折号               |                             描述                             |
| :------: | :---------------------------------: | :----------------------------------------------------------: |
|    -b    |       --ignore-leading-blanks       |                     排序时忽略起始的空白                     |
|    -C    |            --check=quiet            |                不排序，如果数据无序也不要报告                |
|    -c    |               --check               |     不排序，但检查输入数据是不是已排序；未排序的话，报告     |
|    -d    |         --dictionary-order          |               仅考虑空白和字母，不考虑特殊字符               |
|    -f    |            --ignore-case            |    默认情况下，会将大写字母排在前面；这个参数会忽略大小写    |
|    -g    |        --general-number-sort        | 按通用数值来排序（跟-n不同，把值当浮点数来排序，支持科学计数法表示的值） |
|    -i    |        --ignore-nonprinting         |                   在排序时忽略不可打印字符                   |
|    -k    |          --key=POS1[,POS2]          |    排序从POS1位置开始；如果指定了POS2的话，到POS2位置结束    |
|    -M    |            --month-sort             |                   用三字符月份名按月份排序                   |
|    -m    |               --merge               |                   将两个已排序数据文件合并                   |
|    -n    |           --numeric-sort            |            按字符串数值来排序（并不转换为浮点数）            |
|    -o    |            --output=file            |                 将排序结果写出到指定的文件中                 |
|    -R    | --random-sort 或-random-source=FILE |                 按随机生成的散列表的键值排序                 |
|    -r    |              --reverse              |                   反序排序（升序变成降序）                   |
|    -S    |         --buffer-size=SIZE          |                      指定使用的内存大小                      |
|    -s    |              --stable               |                      禁用最后重排序比较                      |
|    -T    |      --temporary-directory=DIR      |                指定一个位置来存储临时工作文件                |
|    -t    |        --field-separator=SEP        |                 指定一个用来区分键位置的字符                 |
|    -u    |              --unique               | 和-c参数一起使用时，检查严格排序；不和-c参数一起用时，仅输出第一例相似的两行 |
|    -z    |          --zero-terminated          |              用NULL字符作为行尾，而不是用换行符              |

> -t 和 -k: 对字段进行分割然后排序
>
> `sort -t ':' -k 3 -n /etc/passwd`
>
> 按照:进行分割, 对第三项进行排列

> -r: 降序排列
>
> `du -sh * | sort -nr`

### 搜索数据

```bash
grep [options] pattern [file] 
```

> 在指定的文件中查找, 返回的是包含传入的字符串的行

> -v: 反向输入

> -n: 显示行号

> -c: 输出有多少各行包括有

> -e: 指定多个匹配` grep -e t -e f file1 `

> 可以使用正则表达式

+ egrep支持POSIX扩展的正则表达式
+ fgrep支持将匹配模式指定为用换行符分隔的一列固定长度的字符串。这样就可以把这列字符串放到一个文件中，然后在fgrep命令中用其在一个大型文件中搜索字符串了

### 压缩数据

Linux集成了多种压缩工具

|   工具   | 文件扩展名 |                       描述                        |
| :------: | :--------: | :-----------------------------------------------: |
|  bzip2   |    .bz2    | 采用Burrows-Wheeler块排序文本压缩算法和霍夫曼编码 |
| compress |     .Z     |      最初的Unix文件压缩工具，已经快没人用了       |
|   gzip   |    .gz     |           GNU压缩工具，用Lempel-Ziv编码           |
|   zip    |    .zip    |           Windows上PKZIP工具的Unix实现            |

> compress文件压缩工具已经很少在Linux系统上看到了。如果下载了带.Z扩展名的文件，通常可以用第9章中介绍的软件包安装方法来安装compress包(在很多Linux发行版上叫作ncompress), 再用uncompress命令来解压文件

gzip是Linux上最流行的压缩工具

+ gzip: 压缩文件
+ gzcat:查看压缩过的文本文件
+ gunzip: 解压

```bash
gzip 文件名
```

> 最文件进行压缩, 可以使用通配符批量操作, 但是只能处理单个文件

### 归档数据

最常用的归档工具是tar

```bash
tar function [options] object1 object2 ...
```

> function: 定义了tar命令应该做什么

| 功能 |    长名称     |                            描述                            |
| :--: | :-----------: | :--------------------------------------------------------: |
|  -A  | --concatenate |      将一个已有tar归档文件追加到另一个已有tar归档文件      |
|  -c  |   --create    |                  创建一个新的tar归档文件                   |
|  -d  |    --diff     |              检查归档文件和文件系统的不同之处              |
|  -r  |   --append    |                  从已有tar归档文件中删除                   |
|  -t  |    --list     |               追加文件到已有tar归档文件末尾                |
|  -u  |   --update    |                 列出已有tar归档文件的内容                  |
|  -x  |   --extract   | 将tar归档文件中已有的同名文件新的文件追加到该tar归档文件中 |
|      |   --delete    |                从已有tar归档文件中提取文件                 |

命令选项;

|  选项   |               描述                |
| :-----: | :-------------------------------: |
| -C dir  |          切换到指定目录           |
| -f file |     输出结果到文件或设备file      |
|   -j    | 将输出重定向给bzip2命令来压缩内容 |
|   -p    |         保留所有文件权限          |
|   -v    |       在处理文件时显示文件        |
|   -z    | 将输出重定向给gzip命令来压缩内容  |

> `tar -cvf test.tar test/ test2/`创建了名为test.tar的归档文件，含有test和test2目录内容

> `tar -tf test.tar`列出tar文件test.tar的内容

> `tar -xvf test.tar`通过这一命令从tar文件test.tar中提取内容

> > 下载了开源软件之后，你会经常看到文件名以.tgz结尾。这些是gzip压缩过的tar文件可以用命令tar -zxvf filename.tgz来解压