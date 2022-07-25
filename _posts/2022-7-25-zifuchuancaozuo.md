---
layout: post
title: "字符串操作" 
---

# 字符串操作

### 字符串

C语言之中没有string，以NUL字节（全部为0 、\0）结尾

使用gets获取到的字符串丢弃换行符加上一个NUL

> scanf函数 ===> 实现按照指定的格式接收输入，返回值是成功传入的参数个数，形式和printf相似，但是接收使用指针，在读取数据时，只读取使用到的数据，剩余的数据保留

> getchar()===>获取一个字符，如果不再存在任何字符返回EOF
>
> **注**：EOF为长度大于一个字节的数字，应该使用整形接收防止误判

> gets()===>获取一个字符串存到传入的指针，但是没有溢出检测

> strcpy()===>字符串复制， **char \*strncpy(char \*dest, const char \*src, size_t n)** 把 **src** 所指向的字符串复制到 								**dest**，最多复制 **n** 个字符。当 src 的长度小于 n 时，dest 的剩余部分将用空字节填充。

> strcpy()===>和上面类似，但是没有限定字符的个数，进行全部复制

> strcat()===>把第二个字符串加到第一个的末尾

> strchr()===>查找一个字符，第一个参数是字符串，第二个参数是要查找的字符

> strstr()===>查找一个字符串