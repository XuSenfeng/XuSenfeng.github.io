---
layout: post
title: "理解shell《Linux命令行与Shell脚本编程大全》" 
date:   2022-8-2 11:29:08 +0800
tags: 《Linux命令行与Shell脚本编程大全》 Linux
---

# 理解shell

shell不只是一种简单的CLI, 他是一个时刻在运行的复杂的交互式程序

## shell的类型

启动什么样的shell取决于个人用户ID的设置, 在`/etc/passwd`文件中

```bash
6jiao:x:1000:1000:jiao,,,:/home/jiao:/bin/bash
-rwxr-xr-x 1 root root 1392424 10月  7  2021 /bin/bash*
```

是一个可执行文件, 一般还包含有其他的shell, 但是由于bash shell广为流传, 所以很少使用别的shell作为默认的shell

默认的shell会在用户登录的时候启动, 不过还有一个默认的shell是/bin/sh, 作为默认的系统shell, 用于那些需要启动时候使用的系统shell脚本

有的发行版会把默认的系统shell使用软连接设置为bash shell,

在有的发行版上默认的系统shell和交互shell并不相同

````bash
lrwxrwxrwx 1 root root 4  3月 11 21:38 /bin/sh -> dash   //Ubuntu使用dash
````

> 对于shell脚本来说, 由于shell的不同, 会导致问题

可以更改默认的交互shell, 直接输入文件名就可以了, 使用exit退出

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/3$ /bin/dash
$ ls
3  abb	acc  baa  num.txt.gz  test.tar.gz  tset
$ cd ..
$ ls
1  3  test.tar
$ exit
```

## shell的父子关系

用于登录某个虚拟控制器终端或在GUI中运行终端仿真器时所启动的默认的交互shell，是一个父shell。父shell提供CLI提示符，然后等待命令输入。

在CLI提示符后输入/bin/bash命令或其他等效的bash命令时，会创建一个新的shell程序。这个shell程序被称为子shell（child shell）。子shell也拥有CLI提示符，同样会等待命令输入

```bash
$ ps --forest
    PID TTY          TIME CMD
   2753 pts/0    00:00:00 bash
   6135 pts/0    00:00:00  \_ dash
   6139 pts/0    00:00:00      \_ ps
```

bash shell程序可以使用命令行参数修改shell的启动方式

|   参数    |                    描述                     |
| :-------: | :-----------------------------------------: |
| -c string |        从string中读取命令并进行处理         |
|    -i     |     启动一个能够接收用户输入的交互shell     |
| -l(小写L) |            以登录shell的形式启动            |
|    -r     | 启动一个受限shell，用户会被限制在默认目录中 |
|    -s     |            从标准输入中读取命令             |

### 进程列表

你可以在一行中指定要依次运行的一系列命令。这可以通过命令列表来实现，只需要在命令之间加入分号（;）即可

```bash
pwd ; ls ; cd /etc ; pwd ; cd ; pwd ; ls 
```

> 这不是一个进程列表

```bash
(pwd ; ls ; cd /etc ; pwd ; cd ; pwd ; ls )
```

> 这是一个进程列表, 会产生一个子shell来运行

