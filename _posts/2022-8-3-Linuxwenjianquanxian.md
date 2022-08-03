---
layout: post
title: "理解Linux的文件权限《Linux命令行与Shell脚本编程大全》" 
date:   2022-8-3 20:03:08 +0800
tags: 《Linux命令行与Shell脚本编程大全》 Linux  
---

# 理解Linux的文件权限

系统要有一套保护文件免受非授权用户浏览修改的机制, 沿用Unix的办法, 允许用户和组根据文件和目录的安全设置来访问

## Linux的安全性

Linux安全的核心是用户的账户, 每个进入Linux系统的用户都会被分配唯一的用户账户, 用户的权限取决于登陆的时候使用的账户

权限是通过创建用户时候分配的ID来跟踪的, 每个用户的UID是数字, 而在登陆的时候使用的是用户名, 同时关联一个密码

Linux系统使用特定的文件和工具来跟踪和管理系统上的用户账户

## /etc/passwd文件

专门的文件来将用户的登录名匹配到对应的UID值

```bash
  1 root:x:0:0:root:/root:/bin/bash                                                       
  2 daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
```

root==>管理员

Linux会为了各种各样的功能创建不同的账户用户, 叫做系统账户, 是系统上运行的各种服务进程访问资源用的特殊账户。所有运行在后台的服务都需要用一个系统用户账户登录到Linux系统上

> 作用: 防止因为某一个用户被攻克导致可以访问所有的文件

Linux为系统账户预留了500以下的UID值, 有些服务甚至要用特定的UID才能正常工作

文件包含的信息如下

+ 登录用户名
+ 用户密码
+ 用户账户的UID（数字形式）
+ 用户账户的组ID（GID）（数字形式）
+ 用户账户的文本描述（称为备注字段）
+ 用户HOME目录的位置
+ 用户的默认shell

所有的密码用x代替, 用户的密码被存放在/etc/shadow, 只有特定的程序才可以访问

### /etc/shadow文件

```bash
jiao:$6$apS9G8HD6duUAyqr$2e81915XsZW02XwXRuyWzo9GVElzwo3kr7X9JUDUlPMDdDCuiJT7mqbV1y6rc1C1EFcF1HeS6dt5dvFUv0Y0/1:19062:0:99999:7:::
```

+ 与/etc/passwd文件中的登录名字段对应的登录名
+ 加密后的密码
+ 自上次修改密码后过去的天数密码（自1970年1月1日开始计算）
+ 多少天后才能更改密码
+ 多少天后必须更改密码
+ 密码过期前提前多少天提醒用户更改密码
+ 密码过期后多少天禁用用户账户
+ 用户账户被禁用的日期（用自1970年1月1日到当天的天数表示）
+ 预留字段给将来使用

使用shadow密码系统后，Linux系统可以更好地控制用户密码。它可以控制用户多久更改一次密码，以及什么时候禁用该用户账户，如果密码未更新的话

### 添加新用户

添加新用户的主要工具是useradd。这个命令简单快捷，可以一次性创建新用户账户及设置用户HOME目录结构。

useradd命令使用系统的默认值以及命令行参数来设置用户账户。系统默认值被设置在/etc/default/useradd文件中

```bash
root@jiao-virtual-machine:~# /usr/sbin/useradd -D
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/sh
SKEL=/etc/skel
CREATE_MAIL_SPOOL=no
```

+ 新用户会被添加到GID为100的公共组；
+ 新用户的HOME目录将会位于/home/loginname；
+ 新用户账户密码在过期后不会被禁用；
+ 新用户账户未被设置过期日期；
+ 新用户账户将bash shell作为默认shell；
+ 系统会将/etc/skel目录下的内容复制到用户的HOME目录下；
+ 系统不会为该用户账户在mail目录下创建一个用于接收邮件的文件

/etc/skel文件夹

```bash
root@jiao-virtual-machine:~# ls /etc/skel -al
总用量 28
drwxr-xr-x   2 root root  4096 10月 13  2021 .
drwxr-xr-x 132 root root 12288  8月  3 20:33 ..
-rw-r--r--   1 root root   220 10月  7  2021 .bash_logout
-rw-r--r--   1 root root  3771 10月  7  2021 .bashrc
-rw-r--r--   1 root root   807 10月  7  2021 .profile
```



```bash
root@jiao-virtual-machine:~# useradd -m test
root@jiao-virtual-machine:~# ls -al /home/test/
总用量 20
drwxr-x--- 2 test test 4096  8月  3 20:47 .
drwxr-xr-x 4 root root 4096  8月  3 20:47 ..
-rw-r--r-- 1 test test  220 10月  7  2021 .bash_logout
-rw-r--r-- 1 test test 3771 10月  7  2021 .bashrc
-rw-r--r-- 1 test test  807 10月  7  2021 .profile
```

> -m会创建一个HOME目录

其他命令

|       参数       |                             描述                             |
| :--------------: | :----------------------------------------------------------: |
|    -c comment    |                       给新用户添加备注                       |
|   -d home_dir    |   为主目录指定一个名字（如果不想用登录名作为主目录名的话）   |
|  -e expire_data  |            用YYYY-MM-DD格式指定一个账户过期的日期            |
| -f inactive_days | 指定这个账户密码过期后多少天这个账户被禁用；0表示密码一过期就立即禁用，1表示禁用这个功能 |
| -g initial_group |                  指定用户登录组的GID或组名                   |
|   -G group...    |          指定用户除登录组之外所属的一个或多个附加组          |
|        -k        | 必须和-m一起使用，将/etc/skel目录的内容复制到用户的HOME目录  |
|        -m        |                      创建用户的HOME目录                      |
|        -M        | 不创建用户的HOME目录（当默认设置里要求创建时才使用这个选项） |
|        -n        |                创建一个与用户登录名同名的新组                |
|        -r        |                         创建系统账户                         |
|    -p passwd     |                    为用户账户指定默认密码                    |
|     -s shell     |                     指定默认的登录shell                      |
|      -u uid      |                     为账户指定唯一的UID                      |

对于默认值的修改, 在`useradd -D`之后加上参数

|        参数        |                     描述                     |
| :----------------: | :------------------------------------------: |
|  -b default_home   |       更改默认的创建用户HOME目录的位置       |
| -e expiration_date |          更改默认的新账户的过期日期          |
|    -f inactive     | 更改默认的新用户从密码过期到账户被禁用的天数 |
|      -g group      |            更改默认的组名称或GID             |
|      -s shell      |             更改默认的登录shell              |

```bash
jiao@jiao-virtual-machine:~$ sudo useradd -D -s /bin/dash
jiao@jiao-virtual-machine:~$ sudo useradd -D 
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/dash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=no
```

### 删除用户

```bash
userdel 用户名
```

> 默认情况下只会删除passwd文件中的记录, 不会删除文件

> -r: 删除HONE目录以及邮件目录

### 修改用户

工具

|   命令   |                           描述                           |
| :------: | :------------------------------------------------------: |
| usermod  | 修改用户账户的字段，还可以指定主要组以及附加组的所属关系 |
|  passwd  |                    修改已有用户的密码                    |
| chpasswd |           从文件中读取登录名密码对，并更新密码           |
|  chage   |                    修改密码的过期日期                    |
|   chfn   |                  修改用户账户的备注信息                  |
|   chsh   |               修改用户账户的默认登录shell                |

#### usermod

可以修改passwd文件中的大部分

参数大部分和useradd一样, 如: -c修改备注字段，-e修改过期日期，-g修改默认的登录组

+ -l修改用户账户的登录名。
+ -L锁定账户，使用户无法登录。
+ -p修改账户的密码。
+ -U解除锁定，使用户能够登录

#### passwd和chpasswd

只有root可以修改其他人的密码

> -e: 强制用户在下一次登陆的时候改密码

chpasswd对大量用户更改密码 

chpasswd命令能从标准输入自动读取登录名和密码对（由冒号分割）列表，给密码加密，然后为用户账户设置

可以用重定向命令来将含有userid:passwd对的文件重定向给该命令

```bash
# chpasswd < users.txt
```

#### chsh, chfn, chage

专门用来修改特定的用户信息

> chsh命令用来快速修改默认的用户登录shell。使用时必须用shell的全路径名作为参数
>
> ```bash
> chsh -s /bin/csh test
> ```

> chfn命令提供了在/etc/passwd文件的备注字段中存储信息的标准方法, 用于Unix的finger命令的信息存进备注字段
>
> ```bash
> jiao@jiao-virtual-machine:~$ sudo chfn test
> 正在改变 test 的用户信息
> 请输入新值，或直接敲回车键以使用默认值
> 	全名 []: 较 焦浩洋
> 	房间号码 []: 01
> 	工作电话 []: 18790676335
> 	家庭电话 []: 110
> 	其它 []: 天才
> chfn：名称中有非 ASCII 字符：“焦浩洋”
> chfn：“天才”包含非 ASCII 字符
> $ vim /etc/passwd
> 49 test:x:1001:1001:焦浩洋,01,18790676335,110,天才:/home/test:/bin/bash 
> ```

最后，chage命令用来帮助管理用户账户的有效期

|    参数     |                描述                |
| :---------: | :--------------------------------: |
|     -d      |    设置上次修改密码到现在的天数    |
|     -E      |         设置密码过期的日期         |
| -I(大写的i) |    设置密码过期到锁定账户的天数    |
|     -m      |    设置修改密码之间最少要多少天    |
|     -W      | 设置密码过期前多久开始出现提醒信息 |

> chage命令的日期值可以用下面两种方式中的任意一种：
>
> YYYY-MM-DD格式的日期代表从1970年1月1日起到该日期天数的数值

> 过期的账户跟锁定的账户很相似：账户仍然存在，但用户无法用它登录。

## 使用Linux组

为了处理在共享文件的安全性

组允许多个用户对系统中的队对象, 如文件, 目录或者设备, 共享一组权限 

有些Linux发行版会创建一个组，把所有用户都当作这个组的成员。遇到这种情况要特别小心，因为文件很有可能对其他用户也是可读的。有些发行版会为每个用户创建单独的一个组

每个组都有唯一的UID以及组名

### /etc/group文件

用来保存每一个组的信息

```bash
  1 root:x:0:                                                                             
  2 daemon:x:1:
  3 bin:x:2:
  4 sys:x:3:
```

+ 组名
+ 组密码
+ GID 
+ 属于该组的用户列表

千万不能通过直接修改/etc/group文件来添加用户到一个组，要用usermod命令（在7.1节中介绍过）。在添加用户到不同的组之前，首先得创建组

> 有些组并没有列出用户。这并不是说这些组没有成员。当一个用户在/etc/passwd文件中指定某个组作为默认组时，用户账户不会作为该组成员再出现在/etc/group文件中

> ```bash
>  jiao@jiao-virtual-machine:~$ sudo usermod -G test jiao
>  76 test:x:1001:jiao   
>  
>  sudo gpasswd -d jiao test        //删除
> ```

### 创建新组

groupadd

```bash
jiao@jiao-virtual-machine:~$ sudo groupadd share
jiao@jiao-virtual-machine:~$ tail /etc/group
sambashare:x:134:
systemd-coredump:x:999:
test:x:1001:
share:x:1002:
```

默认没有用户被分配到该组。groupadd命令没有提供将用户添加到组中的选项，但可以用usermod命令来弥补这一点

```bash
jiao@jiao-virtual-machine:~$ sudo usermod -G share jiao
jiao@jiao-virtual-machine:~$ sudo usermod -G share test 
jiao@jiao-virtual-machine:~$ tail /etc/group
systemd-coredump:x:999:
test:x:1001:
share:x:1002:jiao,test
```

















