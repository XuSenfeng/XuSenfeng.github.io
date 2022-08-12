---
layout: post
title: "gawk进阶《Linux命令行与Shell脚本编程大全》" 
date:   2022-8-10 23:39:08 +0800
tags: 《Linux命令行与Shell脚本编程大全》 Linux
---

# gawk进阶

是一门丰富的编程语言

## 使用变量

+   内建变量
+   自定义变量

### 内建变量

#### 字段和记录分隔符变量

数据字段变量, \$和数字引用记录对应的字段, 第一个\$1, 第二个\$2

命令行下使用命令行参数-F或者在gawk程序中使用特殊的内建变量FS来更改字段分隔符

|    变量     |                       描述                       |
| :---------: | :----------------------------------------------: |
| FIELDWIDTHS | 由空格分隔的一列数字，定义了每个数据字段确切宽度 |
|     FS      |                  输入字段分隔符                  |
|     RS      |                  输入记录分隔符                  |
|     OFS     |                  输出字段分隔符                  |
|     ORS     |                  输出记录分隔符                  |

OFS和FS的功能相似, 但是作用于输出, 默认情况下为空格

```bash
print $1,$2,$3 
# 会看到如下输出：
field1 field2 field3
```

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk 'BEGIN{FS=","} {print $1,$2,$4}' data1.txt 
data11 data12 data14
data21 data22 data24
data31 data32 data34
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk 'BEGIN{FS=","; OFS="-"} {print $1,$2,$4}' data1.txt 
data11-data12-data14
data21-data22-data24
data31-data32-data34
```

FIELDWIDTHS变量允许你不依靠字段分隔符来读取记录。

一旦设置了FIELDWIDTH变量，gawk就会忽略FS变量，并根据提供的字段宽度来计算字段

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ cat data2.txt 
1005.3247596.37 
115-2.349194.00
05810.1298100.1
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk 'BEGIN{FIELDWIDTHS="3 5 2 5"}{print $1,$2,$3, $4}' data2.txt 
100 5.324 75 96.37
115 -2.34 91 94.00
058 10.12 98 100.1
```

变量RS和ORS定义了gawk程序如何处理数据流中的字段。默认情况下，gawk将RS和ORS设为换行符。

每行新文本就是一条新纪录

可以把RS变量设置成空字符串，然后在数据记录间留一个空白行。gawk会把每个空白行当作一个记录分隔符

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ cat data3.txt 
Riley Mullen 
123 Main Street
Chicago, IL  60601 
(312)555-1234

Frank Williams
456 Oak Street 
Indianapolis, IN  46201 
(317)555-9876

Haley Snell 
4231 Elm Street
Detroit, MI 48201 
(313)555-4938 

jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk 'BEGIN{FS="\n"; RS=""} {print $1,$4}' data3.txt 
Riley Mullen  (312)555-1234
Frank Williams (317)555-9876
Haley Snell  (313)555-4938 
```

#### 数据变量

|    变量    |                         描述                         |
| :--------: | :--------------------------------------------------: |
|    ARGC    |                  当前命令行参数个数                  |
|   ARGIND   |                当前文件在ARGV中的位置                |
|    ARGV    |                 包含命令行参数的数组                 |
|  CONVFMT   |   数字的转换格式（参见printf语句），默认值为%.6 g    |
|  ENVIRON   |        当前shell环境变量及其值组成的关联数组         |
|   ERRNO    |      当读取或关闭输入文件发生错误时的系统错误号      |
|  FILENAME  |          用作gawk输入数据的数据文件的文件名          |
|    FNR     |               当前数据文件中的数据行数               |
| IGNORECASE | 设成非零值时，忽略gawk命令中出现的字符串的字符大小写 |
|     NF     |                 数据文件中的字段总数                 |
|     NR     |                  已处理的输入记录数                  |
|    OFMT    |            数字的输出格式，默认值为%.6 g             |
|  RLENGTH   |          由match函数所匹配的子字符串的长度           |
|   RSTART   |        由match函数所匹配的子字符串的起始位置         |

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk "BEGIN{print ARGC,ARGV[1],ARGV[0]}" data1.txt 
2 data1.txt gawk
```

>   gawk并不会将程序脚本当成命令行参数的一部分。

>   在脚本中引用gawk变量时，变量名前不加美元符

ENVIRON变量看起来可能有点陌生。它使用关联数组来提取shell环境变量。关联数组用文本作为数组的索引值，而不是数值

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk '
BEGIN{
print ENVIRON["HOME"]
print ENVIRON["PATH"]
}'
/home/jiao
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/snap/bin:.:/home/jiao/桌面
```

NF变量可以让你在不知道具体位置的情况下指定记录中的最后一个数据字段

NF变量含有数据文件中最后一个数据字段的数字值。可以在它前面加个美元符将其用作字段变量。

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk 'BEGIN{FS=":"; OFS=":"}{print $1,$NF}' /etc/passwd
root:/bin/bash
daemon:/usr/sbin/nologin
...
```

FNR变量含有当前数据文件中已处理过的记录数，NR变量则含有已处理过的记录总数。

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk 'BEGIN{FS=","}{print $1,"FNR="FNR}
' data1.txt data2.txt 
data11 FNR=1
data21 FNR=2
data31 FNR=3
1005.3247596.37  FNR=1
115-2.349194.00 FNR=2
05810.1298100.1 FNR=3
```

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk 'BEGIN{FS=","}{print $1,"NR="NR}
' data1.txt data2.txt 
data11 NR=1
data21 NR=2
data31 NR=3
1005.3247596.37  NR=4
115-2.349194.00 NR=5
05810.1298100.1 NR=6
```

### 自定义变量

变量名可以是任意数目的字母、数字和下划线，但不能以数字开头

#### 在脚本中给变量赋值

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk 'BEGIN{test="This is a test"; print test; test=1; print test}'
This is a test
1
```

gawk编程语言包含了用来处理数字值的标准算数操作符。其中包括求余符号（%）和幂运算符号（^或**）

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk 'BEGIN{test=5 ; test=test*3+2; print test}'
17
```

#### 在命令行上给变量赋值

允许你在正常的代码之外更改变量

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ cat test1.sh 
BEGIN{FS=","}
{print $n}
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk -f test1.sh n=1 data1.txt 
data11
data21
data31
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk -f test1.sh n=2 data1.txt 
data12
data22
data32
```

在你设置了变量后，这个值在代码的BEGIN部分不可用。

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ cat test2.sh 
BEGIN{print "n=",n; FS=","}
{print $n}
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk -f test2.sh n=2 data1.txt 
n= 
data12
data22
data32
```

可以用-v命令行参数来解决这个问题。它允许你在BEGIN代码之前设定变量。在命令行上，-v命令行参数必须放在脚本代码之前

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk -v  n=2 -f test2.sh data1.txt 
n= 2
data12
data22
data32
```

### 定义数组变量

```bash
var[index]=element
```

>   其中var是变量名，index是关联数组的索引值，element是数据元素值

```bash
jiao@jiao-virtual-machine:~/桌面/linux-shell/22$ gawk 'BEGIN{capital["Illinois"]="Spr"
> print capital["Illinois"]
> }'
Spr
```

```bash
$ gawk 'BEGIN{ 
> var[1] = 34 
> var[2] = 3 
> total = var[1] + var[2] 
> print total 
> }'
37 
$
```

### 遍历数组变量

```bash
for (var in array)
{
	statements
}
```

>   记住这个变量中存储的是索引值而不是数组元素值。可以将这个变量用作数组的索引，轻松地取出数据元素值













