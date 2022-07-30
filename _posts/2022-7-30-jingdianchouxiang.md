---
layout: post
title:  "经典抽象数据类型《C和指针》"
date:   2022-7-30 10:11:08 +0800
tags: C和指针 C语言
---

# 经典抽象数据类型

有些抽象数据类型(ADT)是不可或缺的工具,链表,堆栈, 队列, 树等

## 内存分配

所有的ADT都必须确定一件事情,如何获取内存来存储值

+ 静态数组: 固定的长度,编译的时候确定
+ 动态分配数组: 运行时候决定
+ 动态分配链式结构: 最灵活

## 堆栈

### 接口

传统的堆栈有三种接口, push, top, pop

> top: 查看顶层
>
> push: 入栈
>
> pop: 移出但是不返回

还需要一个判断栈是不是为空的函数, 以及一个判断是不是满了的函数

### 实现

+ `stack.h`

```C
  1 #define STACK_TYPE int                                                                
  2 #include <stddef.h>
  3 void push(STACK_TYPE value);
  4 STACK_TYPE pop(void);
  5 STACK_TYPE top(void);
  6 int is_empty(void);
  7 int is_full(void);
  8 void create_stack(size_t size);
  9 void destory_stack(void);

```



+ `stack.c`

```C
  1 #include "stack.h"                                                                    
  2 #include <stdlib.h>
  3 #include <stdio.h>
  4 #include <assert.h>
  5 
  6 static size_t stack_size;
  7 static STACK_TYPE *stack;
  8 static int top_element = -1;
  9 
 10 void create_stack(size_t size)
 11 {
 12     assert(stack_size == 0);
 13     stack_size = size;
 14     stack = malloc(stack_size * sizeof(STACK_TYPE));
 15     assert(stack != NULL);
 16 }
 17 
 18 void destory_stack(void)
 19 {
 20     assert(stack_size > 0);
 21     stack_size = 0;
 22     free(stack);
 23     stack = NULL;
 24 }
 25 
 26 void push(STACK_TYPE value)
 27 {   
 28     assert(!is_full());
 29     top_element += 1;
 30     stack[top_element] = value;
 31 }
 32 
 33 STACK_TYPE pop(void)
 34 {
 35     STACK_TYPE temp;
 36     assert(!is_empty());
 37     temp = stack[top_element];
 38     top_element -= 1;
 39     return temp;
 40 }
 41 
 42 STACK_TYPE top(void)
 43 {
 44     assert(!is_empty());
 45     return stack[top_element];
 46 }
 47 int is_empty(void)
 48 {
 49     assert(stack_size > 0);
 50     return top_element == -1;
 51 }
 52 int is_full(void)
 53 {
 54     assert(stack_size > 0);
 55     return top_element == stack_size - 1;
 56 }

```





# 未完待续





