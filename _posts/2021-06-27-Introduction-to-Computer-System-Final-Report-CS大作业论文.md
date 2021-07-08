---
layout: post
title:  "P2P的: From Program to Process - CS大作业论文"
categories: [ homework, projects, blog ]
image: assets/images/dazuoye/hello.png
author: mani
tags: [featured]
---

<br>

- 目录

- <a href="#topic1">第1章 概述</a>
- 1.1 HELLO简介
- 1.2 环境与工具
- 1.3 中间结果
- 1.4 本章小结

- <a href="#topic2">第2章 预处理</a>
- 2.1 预处理的概念与作用
- 2.2在UBUNTU下预处理的命令
- 2.3 HELLO的预处理结果解析
- 2.4 本章小结

- <a href="#topic3">第3章 编译</a>
- 3.1 编译的概念与作用
- 3.2 在UBUNTU下编译的命令
- 3.3 HELLO的编译结果解析
- 3.4 本章小结

- <a href="#topic4">第4章 汇编</a>
- 4.1 汇编的概念与作用
- 4.2 在UBUNTU下汇编的命令
- 4.3 可重定位目标ELF格式
- 4.4 HELLO.O的结果解析
- 4.5 本章小结

- <a href="#topic5">第5章 链接</a>
- 5.1 链接的概念与作用
- 5.2 在UBUNTU下链接的命令
- 5.3 可执行目标文件HELLO的格式
- 5.4 HELLO的虚拟地址空间
- 5.5 链接的重定位过程分析
- 5.6 HELLO的执行流程
- 5.7 HELLO的动态链接分析
- 5.8 本章小结

- <a href="#topic6">第6章 HELLO进程管理</a>
- 6.1 进程的概念与作用
- 6.2 简述壳SHELL-BASH的作用与处理流程
- 6.3 HELLO的FORK进程创建过程
- 6.4 HELLO的EXECVE过程
- 6.5 HELLO的进程执行
- 6.6 HELLO的异常与信号处理
- 6.7本章小结

- <a href="#topic7">第7章 HELLO的存储管理</a>
- 7.1 HELLO的存储器地址空间
- 7.2 INTEL逻辑地址到线性地址的变换-段式管理
- 7.3 HELLO的线性地址到物理地址的变换-页式管理
- 7.4 TLB与四级页表支持下的VA到PA的变换
- 7.5 三级CACHE支持下的物理内存访问
- 7.6 HELLO进程FORK时的内存映射
- 7.7 HELLO进程EXECVE时的内存映射
- 7.8 缺页故障与缺页中断处理
- 7.9动态存储分配管理
- 7.10本章小结

- <a href="#topic8">第8章 HELLO的IO管理</a>
- 8.1 LINUX的IO设备管理方法
- 8.2 简述UNIX IO接口及其函数
- 8.3 PRINTF的实现分析
- 8.4 GETCHAR的实现分析
- 8.5本章小结

- 结论
- 附件
- 参考文献

## 摘要
Hello 之旅从代码编辑器开始。 为在屏幕上打印`hello`而编写的简单代码。 将源代码编译为对象非常复杂。 输出最终的可执行文件有许多中间步骤。 `hello.c` 预处理、编译、组装并链接到输出 `hello.out` 可执行文件。操作系统和应用程序之间的接口，应用程序可以创建hello进程，等待`hello`进程停止或终止、运行新程序以及捕获来自其他进程的信号。`shell` 是操作系统进程管理，`fork`, `hello.out` 和 `execve` 执行程序。存储管理和 `MMU` 将逻辑地址转换为线性地址，将线性地址转换为虚拟地址，然后将虚拟地址转换为物理地址。使用上述过程可以让 `hello` 能够在键盘、主板、显卡和屏幕之间导航。

关键词：`预处理`; `编译`; `汇编`; `链接`; `异常与信号`; `存储管理`; `IO管理`; `fork`;

<div id="topic1">第1章 概述</div>

### 1.1 Hello简介
根据Hello的自白，利用计算机系统的术语，简述Hello的P2P，020的整个过程。

### 1.2 环境与工具
列出你为编写本论文，折腾Hello的整个过程中，使用的软硬件环境，以及开发与调试工具。

### 1.2.1 硬件环境
i5-8300H CPU；2.30GHz；8G RAM；128G SSD

### 1.2.2 软件环境
Windows10 64位；VMware 16；Ubuntu 18.04 LTS 64位

### 1.2.3 开发工具
Visual Studio 2019 64位；CodeBlocks 64位；vi/vim/gedit+gcc
GDB/OBJDUMP；EDB；KDD等

### 1.3 中间结果
列出你为编写本论文，生成的中间结果文件的名字，文件的作用等。

### 1.4 本章小结
要完成这项作业，需要满足系统要求。 系统应运行带有汇编器、编译器、链接器和调试器的 Linux 虚拟机。

<div id="topic2"><h2>第2章 预处理</h2></div>

### 2.1 预处理的概念与作用
C 预处理器是一个宏处理器，C 编译器自动使用它在实际编译之前转换程序。 预处理器 (cpp) 根据以 `#` 字符开头的指令修改原始 C 程序。 C 预处理器的输出看起来很像输入，只是所有预处理指令行都被替换为空行，所有注释都被替换为空格。

C 预处理器提供了四个独立的工具：

#### 1. 包含头文件

C 预处理器将头文件的内容插入到程序中。 任何 C 程序中最常见的头文件是 `#include <stdio.h>`。它用于 scanf 和 printf。 因此，`stdio.h` 包含 printf 和 scanf 的定义。

有两种类型的头文件。

a) 系统头文件

系统头文件声明了操作系统各部分的接口。它包含在 C 程序中，用于在程序需要调用系统调用和库时提供定义和声明。
例如。
```cpp
#include <stdio.h>
```

b) 用户头文件
用户头文件包含 C 程序源文件之间接口的声明。
例如。
```cpp
#include "myheader.h"
```

#### 2. 宏扩展
C 预处理器将在整个程序中用它们的定义替换宏。宏是一种缩写，在C程序中定义一次，以后使用。
例如 `#define PI 3.14`，C程序中的所有PI宏都替换为3.14


#### 3. 条件编译
条件是允许在编译期间在某些条件下忽略程序的一部分的指令。 #if 指令与#elif、#else 和#endif 指令一起控制源文件部分的编译。

```
#if 表达式
文本-如果-真
#else /* 不是表达式 */
文本如果假
#endif /* 不是表达式 */
```

### 4. 线路控制
C 预处理器通知编译器每个源代码行最初来自组合或重新排列源文件的中间文件中的何处。
例如：#line linenum

linenum 是一个非负十进制整数常量。它指定应为以下输入行报告的行号。后续行从 linenum 开始计数。

## 2.2 在Ubuntu下预处理的命令

命令: `cpp hello.c > hello.i`

![](/assets/images/dazuoye/2.1.png)
<div style="text-align:center; margin-top: -3rem;">图2.1 使用 cpp 预处理 hello.c</div>

## 2.3 Hello的预处理结果解析
这是hello程序的源代码。

```cpp
// 大作业的 hello.c 程序
// gcc -m64 -no-pie -fno-PIC hello.c -o hello
// 程序运行过程中可以按键盘，如不停乱按，包括回车，Ctrl-Z，Ctrl-C等。
// 可以 运行 ps  jobs  pstree fg 等命令

#include <stdio.h>
#include <unistd.h> 
#include <stdlib.h>

int sleepsecs=2.5;

int main(int argc,char *argv[])
{
    int i;

    if(argc!=3)
    {
        printf("Usage: Hello 学号 姓名！\n");
        exit(1);
    }
    for(i=0;i<10;i++)
    {
        printf("Hello %s %s\n",argv[1],argv[2]);
        sleep(sleepsecs);
    }
    getchar();
    return 0;
}
```

1 . 在预处理期间，第 1 行到第 3 行的注释被单个空格替换。

2 . hello.c 程序包含三个头文件。
```cpp
#include <stdio.h>      // 标准缓冲输入/输出
#include <unistd.h>     // 标准符号常量和类型
#include <stdlib.h>     // 标准库定义
```
因此，这些头文件的所有宏和函数定义都包含在预处理文件 hello.i 中。

3 . C 程序使用了 stdio.h 中的 printf() 和 getchar() 函数，stdlib.h 头文件中的 exit() 函数和 unistd.h 中的 sleep() 函数，所以 hello.i 包含这些函数定义。 C预处理器读取系统头文件stdio.h、unistd.h和stdlib.h的内容，直接插入到程序文本文件hello.i中。

hello.i中stdio.h头文件的内容
```cpp
extern int printf (const char *__restrict __format, ...);
extern int getchar (void);
```

hello.i中unistd.h头文件的内容
```cpp
extern unsigned int sleep (unsigned int __seconds);
```

hello.i中stdlib.h头文件的内容
```cpp
extern void exit (int __status) __attribute__ ((__nothrow__ , __leaf__)) __attribute__ ((__noreturn__));
```

### 2.4 本章小结
C 预处理器 (cpp) 是一个宏处理器，C 编译器自动使用它在编译前转换 C 程序。 它被称为宏处理器，因为它允许定义宏，宏是较长结构的简短缩写。 C 预处理器旨在仅用于 C、C++ 和 Objective-C 源代码。

<div id="topic3"><h2>第3章 编译</h2></div>

### 3.1 编译的概念与作用
在 C 预处理器剥离注释的源代码并扩展预处理器指令之后。 编译器 (cc1) 将文本文件 hello.i 翻译成文本文件 hello.s ，其中包含一个汇编语言程序。 它是一种机器级代码，包含直接操作内存和处理器的指令。
编译器生成指令序列来实现程序构造，例如算术表达式求值、循环或过程调用和返回。     

1 . 词法分析将源代码文本分解为一系列称为词法标记的小片段。

这个阶段可以分为两个阶段：

a) 扫描

它将输入文本分割成称为词素的句法单元，并为它们分配一个类别

b) 评估

它将词素转换为处理过的值。令牌是由令牌名称和可选令牌值组成的对。

2 . 语法分析（也称为解析）涉及解析令牌序列以识别程序的句法结构。

3 . 语义分析在解析树中加入语义信息，建立符号表。
此阶段执行语义检查，例如

a) 类型检查：对于类型错误
b) 对象绑定：通过将变量和函数引用与其定义相关联
c) 明确赋值：要求所有局部变量在使用前初始化
d) 拒绝不正确的程序或发出警告。

4 . 优化——中间语言表示被转换成功能等效但速度更快（或更小）的形式。流行的优化是内联扩展、死代码消除、常量传播、循环转换甚至自动并行化。

### 3.2 在Ubuntu下编译的命令
命令：`gcc -S hello.i -o hello.s`
使用 -S 时，编译器会生成汇编代码。

![](/assets/images/dazuoye/3.1.png)
<div style="text-align:center; margin-top: -3rem;">图3.1。将 hello.i 编译为 hello.s</div>

### 3.3 Hello的编译结果解析
以 '.' 开头的所有行汇编代码。 这些是指导汇编器和链接器的指令。这些汇编代码采用 ATT 格式（以 AT&T 命名）。

a) 对于标量数据类型，汇编代码不区分有符号或无符号整数

b) 数据移动指令有四种变体：movb（移动字节）、movw（移动字）、movl（移动双字）和 movq（移动四字）。

c)  hello.s 中的mov只包含“q”和“l”，即四字和双字。

![](/assets/images/dazuoye/3.3.c.png)

d) 最常用的指令是那些将数据从一个位置复制到另一个位置的指令。
例如 hello.s 中的汇编代码循环

![](/assets/images/dazuoye/3.3.d.png)


#### 3.3.1 数据
e) 常量

常量值直接放入汇编代码中。 在图像中 1 是常数值。 

![](/assets/images/dazuoye/3.3.1.e1.png)

控制台上使用的字符串打印消息存储在 .rodata 部分。

![](/assets/images/dazuoye/3.3.1.e2.png)

f) 全局变量

在 hello.s 汇编代码中有一个全局变量 sleepsecs。该全局变量在当前程序和不同程序中都被引用。 所以，即全局变量存储独立于函数的存储。 它存储在函数之外的数据部分。 使用全局偏移表（GOT）引用的全局变量。 .data 部分存储初始化的全局和静态变量，因此编译器首先在 .text 代码段中声明 sleepsecs，然后将对齐设置为 4 字节，大小为 4 字节，类型为 long。 长转换是为了防止数据丢失。 因为 int 可能会导致数据丢失。

![](/assets/images/dazuoye/3.3.1.f.png)

g) 局部变量

局部变量存储在堆栈或寄存器中。 Main 函数中的局部变量 I 存储在 -4(%rbp) 中。

![](/assets/images/dazuoye/3.3.1.g1.png)

i 的值增加一

![](/assets/images/dazuoye/3.3.1.g2.png)


#### 3.3.2 赋值
h) 在hello.c中sleepsecs在汇编代码中赋值为2.5

![](/assets/images/dazuoye/3.3.2.h.png)

i) 在hello.c中我在汇编代码中赋值为0

![](/assets/images/dazuoye/3.3.2.i.png)

j) 在hello.c中i++等价于汇编代码中的i = i + 1。

![](/assets/images/dazuoye/3.3.2.j.png)

#### 3.3.3 类型转换(隐式或显式)
在 hello.c 程序中 sleepsec 变量是 int 但分配的值是 2.5。 所以，这里完成了隐式类型转换，将 float 转换为 long。 3.3.1 b) 分析了长转换防止数据丢失。

#### 3.3.4 算术操作
Hello.c C 代码 `for(i=0;i<10;i++)` 中的 `i++` 改为汇编代码。

![](/assets/images/dazuoye/3.3.4.png)

#### 3.3.5 关系操作
hello.c C 代码的 `if(argc!=3)` 中的 `!=` 更改为汇编代码。

![](/assets/images/dazuoye/3.3.5.png)

hello.c C 代码中 `for(i=0;i<10;i++)` 中的 `i<10` 改为汇编代码。

![](/assets/images/dazuoye/3.3.5.2.png)

#### 3.3.6 数组/指针/结构操作
hello.c 程序中唯一的数组是主函数的第二个参数，即 *argv[]。 argv[0] 包含程序的名称，argv[1] 和 argv[2] 包含程序执行期间提供的命令行参数。

![](/assets/images/dazuoye/3.3.6.png) 

#### 3.3.7 控制转移
a) 跳转指令

跳转指令可以导致执行切换到程序中的一个全新位置。 这些跳转目的地通常在汇编代码中由标签指示。
在 hello.s 中，jmp、je 和 jle 用于跳转到程序的不同部分。
1 . 这里 3 是与相对于基指针的值进行比较。 如果相等，则跳转到 .L2，即循环的起点。

![](/assets/images/dazuoye/3.3.7.1.png)

2 . 这里 0 被移动到相对于基指针的地址 -4(%rbp) 是 for 循环的 i。 然后 jmp 使程序跳转到 .L3 标签。 然后从那里开始程序执行。

![](/assets/images/dazuoye/3.3.7.2.png)
 
3 . 这里的值相对于基指针，即我与值 9 进行比较。如果它小于或等于 9，则跳转到 .L4。

![](/assets/images/dazuoye/3.3.7.3.png)

b) 循环
条件测试和跳转的组合用于实现循环的效果。
这是for循环的汇编代码。

![](/assets/images/dazuoye/3.3.7.b.png)

这里 -4(%rbp) 作为局部变量 i，0 移动到地址。 addl $1, -4(%rbp) 将 i 加一。 在 .L3 中 -4(%rbp) 比较，如果它小于 9，则重复 .L4 中的指令。


#### 3.3.8 函数操作
使用 x86-64，最多可以通过寄存器传递六个整数（即整数和指针）参数。 寄存器以指定的顺序使用，用于寄存器的名称取决于传递的数据类型的大小。

![](/assets/images/dazuoye/3.3.8.png)

<div style="text-align:center; margin-top: -3rem;">图3.2。 用于传递函数参数的寄存器。</div>

当一个函数有六个以上的整型参数时，其他的将在堆栈上传递。 程序为参数 7 到 n 分配一个具有足够存储空间的堆栈帧。


1 . main 函数
这是程序的起点。

![](/assets/images/dazuoye/3.3.8.1.png)

它存储在汇编代码的 .text 部分中。

a) 参数传递(地址/值)

命令行参数 argc 和 argv[] 是主函数的参数，因此 %rdi 和 %rsi 用于存储值的地址。

![](/assets/images/dazuoye/3.3.8.1.a.png)

b) 函数调用()

在汇编代码中，“call”指令用于调用函数。
例如功能

![](/assets/images/dazuoye/3.3.8.1.b.png)

c) 函数返回 return

当 hello 退出时，它将 0 移到 eax 寄存器然后返回。 eax 存储返回值。

![](/assets/images/dazuoye/3.3.8.1.c.png)


2 . puts 函数

在 hello.c 中， printf 更改为 puts 在 hello.s 中。这种优化由编译器完成以节省内存。它比 printf 更有效。

![](/assets/images/dazuoye/3.3.8.2.1.png)

以rip 相对方式指向.LC0，这比默认的绝对寻址要短。.LC0 包含用于程序使用的字符串。然后将带有地址的 puts 调用到 .LCO 字符串中的 rdi 调用并打印该字符串。

![](/assets/images/dazuoye/3.3.8.2.2.png)

3 . printf 函数

printf 没有更改为 puts，因为这里已经完成了格式化。 printf 函数采用三个参数来格式化字符串，argv[1] 和 argv[2]。使用 movq 指令移动到 rdx、rsi 和 rdi 然后调用 printf 函数。

![](/assets/images/dazuoye/3.3.8.3.png)
 
4 . exit 函数

当参数计数不等于 3 时，调用退出函数。退出函数接受一个参数，所以 1 被移动到 rdi 然后退出调用。该函数关闭了程序。

![](/assets/images/dazuoye/3.3.8.4.png) 

5 . sleep 函数

sleepsecs 的相对 rip 的地址移至 eax，然后 eax 的地址移至 edi。 sleep 函数接受一个参数，edi 具有 sleepsecs 的地址。

![](/assets/images/dazuoye/3.3.8.5.png) 

6 . getchar 函数

getchar 函数不接受任何参数，所以 getchar 被简单地调用。

![](/assets/images/dazuoye/3.3.8.6.png)

#### 3.4 本章小结
编译器完成整个编译序列中的大部分工作，将用 C 提供的相对抽象的执行模型表示的程序转换为处理器执行的非常基本的指令。 汇编代码表示非常接近机器代码。 与机器码的二进制格式相比，它的主要特点是它采用更易读的文本格式。

<div id="topic4"><h2>第4章 汇编</h2></div>

### 4.1 汇编的概念与作用
GNU 汇编器，通常称为 gas 或简称为它的可执行名称，是用于将 hello.s 组装成 hello.o 的汇编器。它将 hello.s 转换为机器语言指令，将它们打包成一种称为 可重定位目标程序，并将结果存储在目标文件 hello.o 中。 如果用文本编辑器打开 hello.o 文件，它看起来会很乱。

### 4.2 在Ubuntu下汇编的命令
命令: `as hello.s -o hello.o`

![](/assets/images/dazuoye/4.2.png)
<div style="text-align:center; margin-top: -3rem;">图4.1。hello.s 到 hello.o 的汇编</div>

### 4.3 可重定位目标elf格式
1 . 运行readelf -a hello.o，查看hello.o的精灵信息。
 
![](/assets/images/dazuoye/4.3.1.png)
<div style="text-align:center; margin-top: -3rem;">图4.2。hello.o的elf信息</div>


2 . elf文件的典型结构如下

![](/assets/images/dazuoye/4.3.2.png)
<div style="text-align:center; margin-top: -3rem;">图4.3。典型的 ELF 可重定位目标文件</div>

3 . `e_ident` 字段包含 ELF 文件的格式和版本信息。 幻数的前 4 个字节应该是 `\x7f、E、L、F`。 e_ident 字段的剩余字节将告诉文件是 32 位还是 64 位，文件是小端格式还是大端格式。
 
![](/assets/images/dazuoye/4.3.3.png)
<div style="text-align:center; margin-top: -3rem;">图4.4。hello.o的elf头</div>

4 . 节头定义了文件中的所有节。

<table  class="table">

<tr>
<td>.init</td>
<td>本节包含有助于流程初始化代码的可执行指令。 当程序开始运行时，系统会在调用主程序入口点之前执行本节中的代码。</td>
</tr>

<tr>
<td>.plt</td>
<td>本节包含过程链接表。</td>
</tr>



<tr>
<td>.text</td>

<td>本节包含程序的“文本”或可执行指令。</td>
</tr>


<tr>
<td>.rodata</td>

<td>本节包含通常在过程映像中构成不可写段的只读数据。</td>
</tr>


<tr>
<td>.got</td>

<td>本节包含全局偏移表。</td>
</tr>


<tr>
<td>.data</td>

<td>本节包含有助于程序存储映像的初始化数据。</td>
</tr>


<tr>
<td>.bss</td>

<td>本节包含有助于程序存储映像的未初始化数据。 根据定义，当程序开始运行时，系统将数据初始化为零。</td>
</tr>


<tr>
<td>.symtab</td>

<td>本节包含一个符号表。 一个符号表，用于存储有关程序中定义和引用的函数和全局变量的信息。</td>
</tr>


<tr>
<td>.debug</td>

<td>本节包含有关符号调试的信息。 内容未指定。</td>
</tr>


<tr>
<td>.line</td>

<td>本节包含用于符号调试的行号信息，该信息描述了程序源和机器代码之间的对应关系。</td>
</tr>


<tr>
<td>.strtab</td>

<td>本节包含字符串，最常见的是代表与符号表条目关联的名称的字符串。</td>
</tr>


<tr>
<td>.shstrtab</td>

<td>本节包含节名称。</td>
</tr>

</table>

<div style="text-align:center; margin-top: -3rem;">图4.5。ELF格式的可执行目标文件的各类信息</div>

5 . ELF 格式定义了两种标准的重定位格式，“rel”和“rela”。 第一种形式较短，从被重定位的单词的原始值中获取重定位的加数部分。 第二种形式为全角加数提供显式字段。.rela 重定位部分。 本节内容为：偏移量、信息、类型、符号值、符号名称和加数。 有8条搬迁信息。

![](/assets/images/dazuoye/4.3.5.png)
<div style="text-align:center; margin-top: -3rem;">图4.6。hello.o的.rela.text</div>

6 . 目标文件的符号表包含定位和重新定位程序的符号定义和引用所需的信息。 符号表索引是该数组的下标。 索引 0 既指定表中的第一个条目，又用作未定义符号索引。

![](/assets/images/dazuoye/4.3.6.png)
<div style="text-align:center; margin-top: -3rem;">图4.7。hello.o的 .symtab</div>

### 4.4 Hello.o的结果解析
1 . 运行 objdump -d -r hello.o， hello.o的目标代码转储 文件的代码。

![](/assets/images/dazuoye/4.4.1.png)
<div style="text-align:center; margin-top: -3rem;">图4.8。hello.o的目标代码转储</div>

2 . 跳转中hello.s中的label .L1，.L2，.L3，.L4变成hello.o中的地址

![](/assets/images/dazuoye/4.4.2.png)
<div style="text-align:center; margin-top: -3rem;">图4.9。hello.s 和 hello_o_dump.txt的跳转区别</div>

3 . hello.s 中的函数调用是通过函数名调用的，而 hello.o 中的调用是由 hello.o 中的下一条指令完成的

![](/assets/images/dazuoye/4.4.3.png)
<div style="text-align:center; margin-top: -3rem;">图4.10。hello.s 和 hello_o_dump.txt函数调用区别</div>

4 . 全局变量在hello.s: 段地址+%rip完成的
hello.o 中的全局变量: 0+%rip

![](/assets/images/dazuoye/4.4.4.png)
<div style="text-align:center; margin-top: -3rem;">图4.11。hello.s 和 hello_o_dump.txt 的全局变量区别</div>

由于 .rodata 部分中的数据是在运行时确定的，因此也需要重新定位。

### 4.5 本章小结
本章解释了使用 GNU 汇编器进行编译的概念。 描述了 ELF 文件格式的不同部分。 hello.s 被编译为 hello.o，然后分析 hello.o 的目标代码。 hello.s 与 hello.o 的目标代码进行比较。

<div id="topic5"><h2>第5章 链接</h2></div>

### 5.1 链接的概念与作用
链接是将各种代码和数据收集并组合成一个文件的过程，该文件可以加载（复制）到内存中并执行。 链接可以在编译时执行，当源代码被翻译成机器代码时； 在加载时，当程序被加载到内存并由加载器执行时； 甚至在运行时，通过应用程序。
printf 函数驻留在一个名为 printf.o 的单独预编译目标文件中，它必须
以某种方式与我们的 hello.o 程序合并。 链接器 ( ld ) 处理这种合并。 结果是 hello 文件，它是一个可执行的目标文件（或简称为可执行文件），准备好加载到内存中并由系统执行。

### 5.2 在Ubuntu下链接的命令
命令:  `ld  -o  hello  -dynamic-linker  /lib64/ld-linux-x86-64.so.2 /usr/lib/x86_64-linux-gnu/crt1.o /usr/lib/x86_64-linux-gnu/crti.o hello.o /usr/lib/x86_64-linux-gnu/libc.so /usr/lib/x86_64-linux-gnu/crtn.o`

![](/assets/images/dazuoye/5.2.png)
<div style="text-align:center; margin-top: -3rem;">图5.1。使用 ld 将 hello.o 链接到共享库函数</div>

### 5.3 可执行目标文件hello的格式
节头表（名称、大小、类型、整体大小、地址、标志、偏移量、对齐方式等信息）

![](/assets/images/dazuoye/5.3.png)
<div style="text-align:center; margin-top: -3rem;">图5.2。可执行目标文件 hello 的节头</div>


### 5.4 hello的虚拟地址空间
1 . 在 edb 中运行 hello

![](/assets/images/dazuoye/5.4.1.png)
<div style="text-align:center; margin-top: -3rem;">图5.3。edb 中的 hello 程序调试</div>

2 . 数据转储从 0x400000 开始到 0x400ff0

![](/assets/images/dazuoye/5.4.2.png)
<div style="text-align:center; margin-top: -3rem;">图5.4。edb 中的数据转储 hello 可执行文件</div>

3 . edb 中加载的符号

![](/assets/images/dazuoye/5.4.3.png)
<div style="text-align:center; margin-top: -3rem;">图5.5。hello 可执行文件中的所有符号</div>


4 . hello程序的.interp符号

![](/assets/images/dazuoye/5.4.4.png)
<div style="text-align:center; margin-top: -3rem;">图5.6。.interp 在 edb 中显示的 hello 程序中</div>

5 . hello程序中的动态符号

![](/assets/images/dazuoye/5.4.5.png)
<div style="text-align:center; margin-top: -3rem;">图5.7。.dynsym 在 edb 中显示的 hello 程序中</div>

6 . hello 程序中的 .rodata

![](/assets/images/dazuoye/5.4.6.png)
<div style="text-align:center; margin-top: -3rem;">图5.8。.rodata 在 edb 中显示的 hello 程序中</div>

### 5.5 链接的重定位过程分析
1 . 运行`objdump -d -r hello > hello_dump.txt` 得到汇编代码hello程序

2 . 运行`objdump -d -r hello.o > hello_o_dump.txt` 得到汇编代码 hello.o

3 . hello_dump.txt 的文件部分比 hello_o_dump.txt 多
hello_dump.txt 有 `.init` 和 `.plt` 但 hello_o_dump.txt 只有 `.text` 部分
 
![](/assets/images/dazuoye/5.5.3.png)
<div style="text-align:center; margin-top: -3rem;">图5.9。hello_o_dump.txt 和 hello_dump.txt 的目标代码转储差异</div>


4 . hello_dump.txt 和 hello_o_dump.txt 之间的虚拟地址空间差异

![](/assets/images/dazuoye/5.5.4.png)
<div style="text-align:center; margin-top: -3rem;">图5.10。hello 和 hello.o 的目标代码转储差异</div>

5 . 使用过程链接表 (.plt) 从外部添加到 hello_dump.txt 的共享库函数

![](/assets/images/dazuoye/5.5.5.png)
<div style="text-align:center; margin-top: -3rem;">图5.11。使用 .plt 链接共享库函数</div>

6 . 跳转和函数调用是hello_dump.txt中的虚拟内存地址

![](/assets/images/dazuoye/5.5.6.png) 
<div style="text-align:center; margin-top: -3rem;">图5.12。hello 和 hello.o 中跳转和函数调用的区别</div>

### 5.6 hello的执行流程
1 . 在 edb 中打开 hello 不带参数

![](/assets/images/dazuoye/5.6.1.1.png)
![](/assets/images/dazuoye/5.6.1.2.png)
<div style="text-align:center; margin-top: -3rem;">图5.13。使用不带参数的 edb 运行 hello 并输出显示 hello 使用说明</div>

2 . 使用参数在 edb 中打开 hello

![](/assets/images/dazuoye/5.6.2.1.png)
![](/assets/images/dazuoye/5.6.2.2.png)
<div style="text-align:center; margin-top: -3rem;">图5.14。使用带有参数学校编号和名称的 edb 运行 hello 并输出打印消息十次</div>

3 . 从头到尾执行带有参数的 hello 程序

<table class="table">
  <thead>
    <tr>
      <td scope="col">子程序名</td>
      <td scope="col">地址（16 进制）</td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>ld-2.27.so!_dl_start</td>
      <td>0x00007f13:c67a9093</td>
    </tr>
    <tr>
      <td>ld-2.27.so!_dl_init</td>
      <td>0x00007f13:c67a90c5</td>
    </tr>
    <tr>
      <td>hello!_start</td>
      <td>0x400500</td>
    </tr>
    <tr>
      <td>libc-2.27.so!__libc_start_main</td>
      <td>0x00007f13:c63d8b10</td>
    </tr>
    <tr>
      <td>hello!printf@plt（调用 10 次）</td>
      <td>0x4004c0</td>
    </tr>
    <tr>
      <td>hello!sleep@plt（调用 10 次）</td>
      <td>0x4004f0</td>
    </tr>
    <tr>
      <td>hello!getchar@plt</td>
      <td>0x4004d0</td>
    </tr>
    <tr>
      <td>libc-2.31.so!exit</td>
      <td>0x4004e0</td>
    </tr>
  </tbody>
</table>

<div style="text-align:center; margin-top: -3rem;">图5.15。hello 带参数运行时的函数调用</div>


<table class="table">
  <thead>
    <tr>
      <td scope="col">子程序名</td>
      <td scope="col">地址（16 进制）</td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>ld-2.27.so!_dl_start</td>
      <td>0x00007f13:c67a9093</td>
    </tr>
    <tr>
      <td>ld-2.27.so!_dl_init</td>
      <td>0x00007f13:c67a90c5</td>
    </tr>
    <tr>
      <td>hello!_start</td>
      <td>0x400500</td>
    </tr>
    <tr>
      <td>libc-2.27.so!__libc_start_main</td>
      <td>0x00007f13:c63d8b10</td>
    </tr>
    <tr>
    <td>hello!puts@plt</td>
    <td>0x4004b0</td>
    </tr>
    <tr>
      <td>libc-2.31.so!exit</td>
      <td>0x4004e0</td>
    </tr>
  </tbody>
</table>

<div style="text-align:center; margin-top: -3rem;">图5.16。hello 不带参数运行时的函数调用</div>

### 5.7 Hello的动态链接分析
1 . 动态链接器使用全局偏移表GOT 和过程链接表PLT 来实现函数的动态链接。 函数地址存储在 GOT 中。 而PLT使用GOT中的地址跳转到函数。

2 . 默认情况下，edb 中不显示 GOT，因此，转到 edb 中的 GOT 地址

![](/assets/images/dazuoye/5.7.2.png)
<div style="text-align:center; margin-top: -3rem;">图5.17。使用地址前往 GOT</div>

3 . 调用 dl_init 前的 GOT 信息

![](/assets/images/dazuoye/5.7.3.png)
<div style="text-align:center; margin-top: -3rem;">图5.18。在 dl_init 调用之前，edb 中的hello。</div>

4 . 调用 dl_init 后的 GOT 信息

![](/assets/images/dazuoye/5.7.4.png) 
<div style="text-align:center; margin-top: -3rem;">图5.19。在 dl_init 调用以后，edb 中的hello。</div>

5 . 当编译器生成对全局变量的 PIC 引用时，数据段和代码段之间的距离总是相同的。因此，代码段中的任何指令与数据段中的任何变量之间的距离是一个运行时常数，与代码和数据段的绝对内存位置无关。
编译器将通过在数据部分的开头创建一个称为全局偏移表 (GOT) 的表来生成对全局变量的 PIC 引用。 GOT 包含对象模块引用的每个全局数据对象（过程或全局变量）的 8 字节条目。编译器还为 GOT 中的每个条目生成一个重定位记录。在加载时，动态链接器重新定位每个 GOT 条目，以便它包含对象的绝对地址。
惰性绑定是通过两种数据结构之间紧凑但有些复杂的交互实现的：GOT 和 PLT。如果目标模块调用共享库中定义的任何函数，它就有自己的 GOT 和 PLT。 GOT 是数据段的一部分。 PLT 是代码片段的一部分。

### 5.8 本章小结
在本章中，使用 hello.o 解释了链接到 hello 可执行文件的概念和功能。 也比较了hello.o和hello可执行文件的汇编代码，以及它们在地址、跳转和函数调用上的区别。 然后详细解释全局偏移表和过程链接表。


<div id="topic6"><h2>第6章 hello进程管理</h2></div>

### 6.1 进程的概念与作用
进程的经典定义是正在执行的程序的一个实例。 系统中的每个程序都在某个进程的上下文中运行。 上下文由程序需要正确运行的状态组成。 此状态包括存储在内存中的程序代码和数据、其堆栈、其通用寄存器的内容、其程序计数器、环境变量和一组打开的文件描述符。
每次用户通过向 shell 键入可执行对象文件的名称来运行程序时，shell 都会创建一个新进程，然后在这个新进程的上下文中运行可执行对象文件。
应用程序还可以创建新进程并在新进程的上下文中运行它们自己的代码或其他应用程序。

进程为应用程序提供的关键抽象：

a) 一个独立的逻辑控制流，提供我们的程序独占使用处理器的错觉。

b) 一个私有地址空间，它提供了我们的程序独占使用内存系统的错觉。

### 6.2 简述壳Shell-bash的作用与处理流程
Shell 是一种交互式应用程序级程序，它代表用户运行其他程序。 最初的 shell 是 sh 程序，随后是 csh、tcsh、ksh 和 bash 等变体。 Shell 执行一系列读取/评估步骤，然后终止。 读取步骤从用户读取命令行。 评估步骤解析命令行并代表用户运行程序。

shell的功能

shell 是一个交互式界面，允许用户在 Linux 和其他基于 UNIX 的操作系统中执行其他命令和实用程序。 登录操作系统时，会显示标准shell，可以进行复制文件或重启系统等常用操作。

shell的处理流程
1. shell运行时，等待接收命令
2. 命令完成后，shell 将获取该行并将其保存在包含空 `\0` 字符的字符数组中
3. 使用标记化来分隔字符串
4. 然后shell验证receive命令匹配函数调用
5. shell 会定位函数调用。 它将在 PATH 环境中找到。
6. shell 执行匹配的程序进行系统调用（syscall）来创建一个新进程。

![](/assets/images/dazuoye/6.1.png)
<div style="text-align:center; margin-top: -3rem;">图6.1。bash 组件架构</div>

### 6.3 Hello的fork进程创建过程
fork() 通过复制调用进程来创建一个新进程。 新进程称为子进程。 调用进程称为父进程。 fork 函数被调用一次，但它返回两次：一次在调用进程（父进程）中，一次在新创建的子进程中。 在父级中，fork 返回子级的 PID。 在子进程中，fork 返回值 0。子进程和父进程在不同的内存空间中运行。 在 fork() 时，两个内存空间具有相同的内容。

1. shell正在运行，等待命令输入
2. 使用./hello 学号用户名运行hello程序时，终端判断是否内置命令。
3. hello 不是内置命令，shell 会在当前目录
4. shell执行fork创建子进程，hello程序在这个进程的上下文中执行
5. hello 子进程拥有与父进程相同的地址空间、数据、堆、栈副本, 等。
6. 父子进程之间不共享存储空间。

![](/assets/images/dazuoye/6.3.6.png)
<div style="text-align:center; margin-top: -3rem;">图6.2。hello程序执行在shell</div>

### 6.4 Hello的execve过程
execve 函数在当前进程的上下文中加载并运行一个新程序。 execve 函数加载并运行带有参数列表 argv 和环境变量列表 envp 的可执行目标文件 filename。 Execve 仅在出现错误时才返回调用程序。 这会导致调用进程的当前运行程序被具有新初始化堆栈、堆和（初始化和未初始化）数据段的新程序替换。

1. execve 从 argv[0] 加载文件名，即 hello
2. 它调用启动代码
3. 启动代码设置栈，将控制权交给hello程序的main函数
4. 当 main 开始执行时，用户堆栈具有以下组织。

![](/assets/images/dazuoye/6.4.4.png)
<div style="text-align:center; margin-top: -3rem;">图6.3。hello启动时用户堆栈的典型组织</div>

5 . 存储在寄存器中的主函数 argc 和 argv 的参数指向 argv[] 数组的第一个条目。


### 6.5 Hello的进程执行
#### 6.5.1 进程上下文
内核为每个进程维护一个上下文。 上下文是内核需要重新启动被抢占进程的状态。 它由对象的值组成，例如通用寄存器、浮点寄存器、程序计数器、用户堆栈、状态寄存器、内核堆栈和各种内核数据结构，例如表征地址空间的页表，以及表征地址空间的进程表。 包含有关当前进程的信息，以及包含有关进程已打开文件的信息的文件表。

上下文切换机制
- a) 保存当前进程的上下文
- b) 恢复一些先前被抢占的进程的保存上下文
- c) 将控制权交给这个新恢复的进程

#### 6.5.2 进程时间片
在抢占式调度下，最高优先级的任务将一直执行，直到它进入等待或死状态或出现更高优先级的任务。 在时间片下，任务在预先定义的时间片内执行，然后重新进入就绪任务池。

#### 6.5.3 过程调度
在进程执行过程中的某些时刻，内核可以决定抢占当前进程并重新启动先前被抢占的进程。 这个决定称为调度，由内核中的代码处理，称为调度器。 当内核选择一个新进程运行时，我们说内核已经调度了该进程。 内核安排新进程运行后，它会抢占当前进程并使用一种称为上下文切换的机制将控制权转移给新进程。

#### 6.5.4 用户态与核心态转换
上下文切换也可能因中断而发生。 内核可以判断当前进程已经运行了足够长的时间并切换到一个新进程。 如果 hello 程序需要一些来执行当前指令，则内核切换到其他进程执行。 当中断发生以执行完整的 hello 指令时，内核决定从其他进程切换到 hello 进程。
![](/assets/images/dazuoye/6.5.4.png)
<div style="text-align:center; margin-top: -3rem;">图6.4。进程上下文切换剖析</div>

### 6.6 hello的异常与信号处理
信号是一条小消息，它通知进程系统中发生了某种类型的事件。

![](/assets/images/dazuoye/6.6.png)
<div style="text-align:center; margin-top: -3rem;">图6.5。信号处理
信号的接收触发到信号处理程序的控制转移。 处理完成后，处理程序将控制权返回给被中断的程序。</div>

#### 6.6.1 不停乱按
![](/assets/images/dazuoye/6.6.1.png)
<div style="text-align:center; margin-top: -3rem;">图6.6。运行 hello 并从输入而不停止</div>



#### 6.6.2 回车
![](/assets/images/dazuoye/6.6.2.1.png)
![](/assets/images/dazuoye/6.6.2.2.png)
<div style="text-align:center; margin-top: -3rem;">图6.7。输入回车等键盘输入不停</div>

### 6.6.3 Ctrl-Z
键入 Ctrl+Z 会导致内核向前台进程组中的每个进程发送 SIGTSTP 信号。 在默认情况下，结果是停止（挂起）前台作业。

![](/assets/images/dazuoye/6.6.3.png)
<div style="text-align:center; margin-top: -3rem;">图6.8。ctrl+z 的情况</div>

#### 6.6.4 Ctrl-C
在键盘上输入 Ctrl+C 会导致内核向前台进程组中的每个进程发送一个 SIGINT 信号。在默认情况下，结果是终止前台作业。

![](/assets/images/dazuoye/6.6.4.png)
<div style="text-align:center; margin-top: -3rem;">图6.9。ctrl+c 的情况</div>

#### 6.6.5 Ctrl-z后可以运行ps，jobs
![](/assets/images/dazuoye/6.6.5.png)
<div style="text-align:center; margin-top: -3rem;">图6.10。运行 hello 后的 ps 命令</div>

#### 6.6.6 Ctrl-z后可以运行pstree  
![](/assets/images/dazuoye/6.6.6.png)
<div style="text-align:center; margin-top: -3rem;">图6.11。运行 hello 后的 pstree 命令</div>

#### 6.6.7 Ctrl-z后可以运行fg  
在键盘上输入 fg 会导致内核向前台进程组中的每个进程发送一个 SIGCONT 信号。在默认情况下，结果是继续进程。
![](/assets/images/dazuoye/6.6.7.png)
<div style="text-align:center; margin-top: -3rem;">图6.12。运行 hello 后的 fg 命令</div>

#### 6.6.8 Ctrl-z后可以运行kill
/bin/kill 程序向另一个进程发送任意信号。
kill -9 19547，向进程组 15213 中的每个进程发送信号 9 (SIGKILL)。

![](/assets/images/dazuoye/6.6.8.png)
<div style="text-align:center; margin-top: -3rem;">图6.13。ctrl+z 后杀死命令</div>

### 6.7 本章小结
在本章中，将解释进程和信号。 解释了 bash shell 从接受输入到执行程序的工作。 Unix shell 和 Web 服务器等程序大量使用 fork 和 execve 函数。 在shell中使用fork和execve执行hello程序。 内核切换到中断的其他进程也会调度该进程执行。 hello 程序运行在各种条件下，例如不停和键盘输入、CTRL+Z 和CTRL+C 期间的暂停和终止，以及其他linux 命令如ps、fg, kill 和pstree。

<div id="topic7"><h2>第7章 hello的存储管理</h2></div>

### 7.1 hello的存储器地址空间
#### 7.1.1 逻辑地址 (Logical Address)
逻辑地址是指与程序生成的段相关的偏移地址部分。 应用程序员只需要处理逻辑地址，分段和分页机制对他们完全透明，只由系统程序员参与。 比如hello中main函数的起始地址就是一个逻辑地址。

#### 7.1.2 线性地址 (Linear Address)
线性地址是通过段转换从虚拟地址计算出来的。 选择器引用的段的基址被添加到虚拟偏移量，给出一个 32 位线性地址。

#### 7.1.3 虚拟地址 (Virtual Address)
虚拟地址由应用程序使用。 它们由一个 16 位选择器和一个 32 位偏移量组成。 在平面内存模型中，选择器被预加载到段寄存器 CS、DS、SS 和 ES 中，它们都指向相同的线性地址。 应用程序不需要考虑它们。 地址只是 32 位近指针。

#### 7.1.4 物理地址 (Physical Address)
物理地址是通过分页从线性地址计算出来的。 线性地址用作 CPU 定位相应物理地址的页表的索引。 如果未启用分页，则线性地址始终等于物理地址。

## 7.2 Intel逻辑地址到线性地址的变换-段式管理
#### 7.2.1 为了执行这种转换，处理器使用以下数据结构：
**a) 描述符 (Descriptors)**

段描述符为处理器提供将逻辑地址映射到线性地址所需的数据。 描述符由编译器、链接器、加载器或操作系统创建，而不是由应用程序程序员创建。

![](/assets/images/dazuoye/7.1.png)
<div style="text-align:center; margin-top: -3rem;">图7.1。通用段描述符格式</div>

**b) 描述符表 (Descriptor tables)**

段描述符存储在两种描述符表中的任一种中：
- a) 全局描述符表 (GDT)
- b) 本地描述符表 (LDT)

描述符表只是一个包含描述符的 8 字节条目的内存数组。 描述符表的长度可变，最多可包含 8192 (2^(13)) 个描述符。 但是，处理器不使用 GDT 的第一个条目 (INDEX=0)。
处理器通过 GDTR 和 LDTR 寄存器在内存中定位 GDT 和当前 LDT。 这些寄存器将表的基地址存储在线性地址空间中并存储段限制。 LGDT 和 SGDT 指令允许访问 GDTR； LLDT 和 SLDT 指令允许访问 LDTR。

![](/assets/images/dazuoye/7.2.png)
<div style="text-align:center; margin-top: -3rem;">图7.2。描述符表</div>

**c) 选择器 (Selectors)**

逻辑地址的选择器部分通过指定一个描述符表并在该表中索引一个描述符来标识一个描述符。 选择器可能作为指针变量中的一个字段对应用程序可见，但选择器的值通常由链接器或链接加载器分配（固定）。

![](/assets/images/dazuoye/7.3.png) 
<div style="text-align:center; margin-top: -3rem;">图7.3。选择器的格式</div>

由于处理器不使用 GDT 的第一个条目，因此索引为零且表指示符为零的选择器（即指向 GDT 的第一个条目的选择器）可以用作空值 选择器。 当段寄存器（除了 CS 或 SS）加载了空选择器时，处理器不会导致异常。 但是，当使用段寄存器访问内存时，它会导致异常。 此功能对于初始化未使用的段寄存器以捕获意外引用很有用。

**d) 段寄存器 (Segment Registers)**

80386 将来自描述符的信息存储在段寄存器中，从而避免每次访问内存时都需要查阅描述符表。 使用这些指令，程序加载带有 16 位选择器的段寄存器的可见部分。 处理器自动从描述符表中获取基地址、限制、类型和其他信息，并将它们加载到段寄存器的不可见部分。因为大多数指令引用的段中的数据的选择器已经加载到段寄存器中，所以 处理器可以将指令提供的段相对偏移量添加到段基地址，而无需额外开销。

![](/assets/images/dazuoye/7.4.png)
<div style="text-align:center; margin-top: -3rem;">图7.4。段寄存器</div>

#### 7.2.2 段翻译(Segment Translation)
1. TI=0，选择全局描述符表（GDT），TI=1，选择局部描述符表（LDT）
RPL=00，是第0级，位于最高核心状态，RPL=11，是第3级，
在用户态度的最低级别，级别 0 高于级别 3。
高13-bit-8K索引用于确定当前使用的段描述符在描述符表中的位置
2. 机器语言指令中出现的内存地址都是逻辑地址，需要先转换成线性地址，再由MMU（CPU中的内存管理单元）转换成物理地址，才能访问。
 
![](/assets/images/dazuoye/7.5.png)
<div style="text-align:center; margin-top: -3rem;">图7.5。逻辑地址到线性地址的变换</div>

### 7.3 Hello的线性地址到物理地址的变换-页式管理
#### 7.3.1 页目录项和页表项

![](/assets/images/dazuoye/7.6.png)
<div style="text-align:center; margin-top: -3rem;">图7.6。页目录项和页表项</div>

1. P: 1表示页表或页在主存中, 0 表示页表或页不在主存中，即缺少页。此时
需要将缺页线性地址保存到CR2。
2. R/W: 0 表示页表或页是只读的, 1 表示可以读写
3. U/S：0表示用户进程不能访问, 1 表示允许访问。
4. PWT：页表或页的缓存写策略是全写还是回写（Write Back）。
5. PCD：可以在缓存中缓存页表或页。
6. A: 1 表示指定的页表或页已被访问，OS 会在初始化时将其清零。通过使用这个标志，操作系统可以清楚地识别哪些页表或页面正在使用，一般选择长期未使用的页面或最近最少使用的页面来调用主存。该位由 MMU 在地址转换期间设置。
7. D：修改位（脏位）。在页目录项中没有意义，只在页表项中有意义。 OS在初始化时将其清0，当MMU执行写操作的地址转换时，该位设置为1。
8. PS: 页大小为 4 KB 或 4 MB ( 只对第一层 PTE 定义
9. G: 表示全局页面 Global
10. 高20位是主存中页表或页首地址对应的页框号，即首地址的高20位。每个页表的起始位置对齐4KB。

![](/assets/images/dazuoye/7.7.png)
<div style="text-align:center; margin-top: -3rem;">图7.7。地址转换概述</div>

![](/assets/images/dazuoye/7.8.png)
<div style="text-align:center; margin-top: -3rem;">图7.8。线性地址到物理地址的变换</div>

### 7.4 TLB与四级页表支持下的VA到PA的变换
处理器封装（芯片）包括四个内核、一个所有内核共享的大型 L3 缓存和一个 DDR3 内存控制器。 每个内核都包含一个 TLB 层次结构、一个数据和指令缓存层次结构以及一组基于 QuickPath 技术的快速点对点链接，用于直接与其他内核和外部 I/O 桥进行通信。 TLB 是虚拟寻址的，并且是 4 路组关联的。 L1、L2 和 L3 高速缓存是物理寻址的，块大小为 64 字节。 L1 和 L2 是 8 路集合关联，L3 是 16 路集合关联。 页面大小可以在启动时配置为 4 KB 或 4MB。 Linux 使用 4 KB 页面。
 
![](/assets/images/dazuoye/7.9.png)
<div style="text-align:center; margin-top: -3rem;">图7.9。Intel Core i7 内存系统</div>

Core i7 MMU 使用四级页表将虚拟地址转换为物理地址。 36 位 VPN 被划分为四个 9 位块，每个块用作页表中的偏移量。 CR3 寄存器包含 L1 页表的物理地址。 VPN 1 为 L1 PTE 提供了一个偏移量，其中包含 L2 页表的基地址。 VPN 2 提供对 L2 PTE 的偏移量，依此类推。

```
PT: page table 
PTE: page table entry
VPN: virtual page number
VPO: virtual page offset
PPN: physical page number
PPO: physical page offset
```

![](/assets/images/dazuoye/7.10.png) 
<div style="text-align:center; margin-top: -3rem;">图7.10。针对core i7页表翻译; 四级页表翻译</div>

### 7.5 三级Cache支持下的物理内存访问
Core i7 使用四级页表层次结构。 每个进程都有自己的私有页表层次结构。 当 Linux 进程运行时，与分配的页面相关联的页表都驻留在内存中，尽管 Core i7 架构允许这些页表交换进出。 CR3 控制寄存器包含一级 (L1) 页表开头的物理地址。 CR3 的值是每个进程上下文的一部分，并在每次上下文切换期间恢复。

![](/assets/images/dazuoye/7.11.png)
<div style="text-align:center; margin-top: -3rem;">图7.11。Core i7地址转换总结</div>

### 7.6 hello进程fork时的内存映射
1. 当前进程调用fork函数时，内核为hello进程创建各种数据结构，并为其分配一个唯一的PID。 要为 hello 进程创建虚拟内存，它会创建当前进程的 mm_struct 、区域结构和页表的精确副本。 它将两个进程中的每个页面标记为只读，并将两个进程中的每个区域结构标记为私有写时复制。

2. 当 fork 在 hello 进程中返回时，hello 进程现在拥有虚拟内存的精确副本，因为它在调用 fork 时存在。 当任一进程执行任何后续写入时，写时复制机制会创建新页面，从而为每个进程保留私有地址空间的抽象。

### 7.7 hello进程execve时的内存映射
execve 函数在当前进程中加载并运行包含在可执行目标文件 hello 中的程序，有效地将当前程序替换为 hello 程序。
加载和运行 hello 需要以下步骤：
1. **删除现有用户区域：**删除当前进程虚拟地址的用户部分中的现有区域结构。
2. **映射私人区域：**为代码、数据、bss 和堆栈区的 hello。所有这些新区域都是私有的写时复制。代码和数据区域映射到 hello 文件的 .text 和 .data 部分。 bss 区域是零需求，映射到一个匿名文件，其大小包含在 hello 中。堆栈和堆区域也要求为零，最初长度为零。
3. **映射共享区域：**如果 hello 程序与共享对象链接，例如标准 C 库 libc.so ，那么这些对象将动态链接到程序中，然后映射到用户虚拟地址空间的共享区域。
4. **设置程序计数器（PC）：** execve 所做的最后一件事是将当前进程上下文中的程序计数器设置为指向代码区中的入口点。

下次安排此进程时，它将从入口点开始执行。 Linux 将根据需要交换代码和数据页。

### 7.8 缺页故障与缺页中断处理
1. 在虚拟内存术语中，DRAM 缓存未命中称为页面错误。
2. CPU引用了VP 3中的一个字，没有缓存在DRAM中。 地址转换硬件从内存中读取 PTE 3，从有效位推断 VP 3 未缓存，并触发缺页异常。
3. 缺页异常调用内核中的缺页异常处理程序，它选择一个牺牲页——在这种情况下，VP 4 存储在 PP 3 中。如果 VP 4 已被修改，则内核将其复制回磁盘。 无论哪种情况，内核都会修改 VP 4 的页表条目，以反映 VP 4 不再缓存在主内存中的事实。
4. 接下来，内核将VP 3 从磁盘复制到内存中的PP 3，更新PTE 3，然后返回。 当处理程序返回时，它会重新启动出错指令，该指令将出错的虚拟地址重新发送到地址转换硬件。 但是现在，VP 3 缓存在主内存中，页面命中由地址转换硬件正常处理。

缺页故障（以前）：对 VP 3 中的字的引用是未命中并触发缺页错误。

![](/assets/images/dazuoye/7.12.png)
<div style="text-align:center; margin-top: -3rem;">图7.12。缺页故障（以前）</div>

缺页故障（以后）：页错误处理程序选择 VP 4 作为牺牲品，并用磁盘中的 VP 3 副本替换它。 缺页处理程序重新启动出错指令后，将正常从内存中读取该字，不会产生异常。

![](/assets/images/dazuoye/7.13.png)
<div style="text-align:center; margin-top: -3rem;">图7.13。缺页故障（以后）</div>

## 7.9 动态存储分配管理
#### 7.9.1 动态内存分配器 
动态内存分配器维护进程的虚拟内存区域，称为堆。堆是一个零需求内存区域，它在未初始化的数据区域之后立即开始并向上增长（向更高的地址）。对于每个进程，内核维护一个指向堆顶部的变量 brk。

有两种类型的分配器。
1. 显式分配器要求应用程序显式释放任何已分配的块。例如，C 标准库提供了一个称为 malloc 包的显式分配器。 C 程序通过调用 malloc 函数分配块，并通过调用 free 函数释放块。 C++ 中的 new 和 delete 调用具有可比性。
2. 隐式分配器要求分配器检测已分配的块何时不再被程序使用，然后释放该块。隐式分配器也称为垃圾收集器，自动释放未使用的分配块的过程称为垃圾收集。例如，Lisp、ML 和 Java 等高级语言依靠垃圾收集来释放分配的块。


#### 7.9.2 带边界标签的隐式空闲链表分配器原理
一个块由一个单字头、有效载荷和可能的一些额外填充组成。 标头对块大小（包括标头和任何填充）以及块是已分配还是空闲进行编码。
![](/assets/images/dazuoye/7.14.png)
<div style="text-align:center; margin-top: -3rem;">图7.14。简单堆块的格式</div>

![](/assets/images/dazuoye/7.15.png)
<div style="text-align:center; margin-top: -3rem;">图7.15。使用隐式空闲列表组织堆</div>

例如，假设我们有一个块大小为 24 ( 0x18 ) 字节的已分配块。 那么它的标题将是
0x00000018 | 0x1 = 0x00000019

类似地，块大小为 40 ( 0x28 ) 字节的空闲块的标头为
0x00000028 | 0x0 = 0x00000028

标头后面是应用程序在调用 malloc 时请求的有效负载。 有效负载后面是一大块未使用的填充，可以是任意大小。 填充的原因有很多。
边界标签的概念是一个简单而优雅的概念，它可以推广到许多不同类型的分配器和空闲列表组织。 然而，有一个潜在的缺点。 如果应用程序操作许多小块，要求每个块都包含页眉和页脚可能会引入大量内存开销。

#### 7.9.3 显式空间链表的基本原理
更好的方法是将空闲块组织成某种形式的显式数据结构。 由于根据定义，程序不需要空闲块的主体，因此实现数据结构的指针可以存储在空闲块的主体中。 例如，通过在每个空闲块中包含一个 pred（前导）和 succ（后继）指针，可以将堆组织为一个双向链接的空闲列表。

![](/assets/images/dazuoye/7.16.png)
<div style="text-align:center; margin-top: -3rem;">图7.16。使用双向链表的堆块的格式</div>

使用双向链表而不是隐式空闲列表将首次适合的分配时间从块总数的线性减少到空闲块数的线性。 然而，释放块的时间可以是线性的，也可以是常数，这取决于我们为空闲列表中的块排序所选择的策略。 一种方法是通过在列表的开头插入新释放的块来以后进先出 (LIFO) 顺序维护列表。

## 7.10 本章小结
在转移到主存之前，处理器生成一个虚拟地址，然后将其转换为物理地址。 从虚拟地址空间到物理地址空间的地址转换需要硬件和软件之间的强大协作。 虚拟地址由专用硬件使用页表进行转换，页表的内容由操作系统提供。
当引用磁盘页面时，会发生页面错误，并将控制权传递给操作系统的错误处理程序。 故障处理程序将页面从磁盘移动到主内存缓存，并在必要时重写被驱逐的页面。
系统中任何硬件缓存的操作都必须与地址转换过程相结合。 L1 缓存包含大部分页表数据，尽管称为 TLB 的页表条目的片上缓存降低了从 L1 访问页表信息的成本。


<div id="topic8"><h2>第8章 hello的IO管理</h2></div>

### 8.1 Linux的IO设备管理方法
**设备的模型化：** 文件

所有的 I/O 设备，包括网络、磁盘和终端，都被视为文件，所有的输入和输出都是通过读写相关文件来完成的。

**设备管理：**unix io接口

Linux 基于 Unix I/O 架构，提供了一组有限的系统级功能，允许程序打开、关闭、读写文件、检索文件信息和执行 I/O 重定向。

![](/assets/images/dazuoye/8.1.png)
<div style="text-align:center; margin-top: -3rem;">图8.1。Unix I/ O 、 C Standard I/ O 、 RIO</div>

### 8.2 简述Unix IO接口及其函数
#### 8.2.1 打开一个文件
应用程序通过要求内核打开相应的文件来宣布其访问 I/O 设备的意图。 内核返回一个小的非负整数，称为描述符，它在文件的所有后续操作中标识该文件。 内核会跟踪有关打开文件的所有信息。 应用程序只跟踪描述符。

函数

a) `int open(char *filename, int flags, mode_t mode);`

open 函数将文件名转换为文件描述符并
返回描述符编号。 返回的描述符始终是进程中当前未打开的最小描述符。

flags 参数指示进程打算如何访问文件：
- O_WRONLY：只写
- O_RDONLY：只读
- O_RDWR：读写

flags 参数也可以与一个或多个位掩码进行 OR 运算，以获得额外的写入指令：
- O_CREAT：如果该文件不存在，则创建它的截断（空）版本。
- O_TRUNC：如果文件已经存在，则截断它。
- O_APPEND：在每次写操作之前，将文件位置设置为文件末尾。
返回值: 若成功则为新文件描述符，否则返回-1

#### 8.2.2 更改当前文件位置
内核为每个打开的文件维护一个文件位置 k，最初为 0。 文件位置是从文件开头的字节偏移量。 应用程序可以通过执行查找操作显式设置当前文件位置 k。

#### 8.2.3 读取和写入文件
读取操作将 n > 0 个字节从文件复制到内存，从当前文件位置 k 开始，然后将 k 递增 n。 给定一个大小为 m 字节的文件，当 k ≥ m 触发称为结束文件 (EOF) 的条件时执行读取操作，该条件可以被应用程序检测到。 文件末尾没有明确的“EOF 字符”。 类似地，写操作从内存复制 n > 0 个字节到一个文件，从当前文件位置 k 开始，然后更新 k。
函数
应用程序通过调用读写函数来执行输入和输出。

a) `ssize_t read(int fd, void *buf, size_t n);`
read 函数最多将 n 个字节从描述符 fd 的当前文件位置复制到内存位置 buf。
返回值： 如果正常则读取的字节数，EOF 为 0，错误为 -1

b) `ssize_t write(int fd, const void *buf, size_t n);`
write 函数最多将 n 个字节从内存位置 buf 复制到描述符 fd 的当前文件位置。
返回值：如果正常则写入的字节数，错误时为 -1

#### 8.2.4 关闭文件 
当应用程序完成访问文件时，它通过要求内核关闭文件来通知内核。 内核通过释放它在打开文件时创建的数据结构并将描述符恢复到可用描述符池来响应。 当进程因任何原因终止时，内核会关闭所有打开的文件并释放它们的内存资源。
函数
`int close(int fd);`
关闭文件描述符，使其不再引用任何文件并且可以重用。
 返回值: 成功返回0，否则为-1

## 8.3 printf的实现分析
#### 8.3.1 printf() 实现
函数将格式化的字符串发送到标准输出（显示器）

```cpp
int printf(const char *fmt, ...)
{
    int i;
    char buf[256];
    va_list arg = (va_list)((char *)(&fmt) + 4);
    i = vsprintf(buf, fmt, arg);
    write(buf, i);
    return i;
}
```

**参数：** `const char *fmt, ...`

- a) fmt是一个指针，这个指针指向第一个const参数（const char *fmt)中的第一个元素。

- b) ...当参数数量不确定时使用。

#### 8.3.2 分析
1 . `va_list arg = (va_list)((char *)(&fmt) + 4);`

- a) va_list的定义： typedef char *va_list这说明它是一个字符指针

- b) (char *)(&fmt) + 4) 表示的是...中的第一个参数

2 . `i = vsprintf(buf, fmt, arg);`
vsprintf的作用就是格式化。它接受确定输出格式的格式字符串fmt。用格式字符串对个数变化的参数进行格式化，产生格式化输出。

实现
```cpp
int vsprintf(char *buf, const char *fmt, va_list args)
{
    char *p;
    char tmp[256];
    va_list p_next_arg = args;

    for (p = buf; *fmt; fmt++)
    {
        if (*fmt != '%')
        {
            *p++ = *fmt;
            continue;
        }

        fmt++;

        switch (*fmt)
        {
        case 'x':
            itoa(tmp, *((int *)p_next_arg));
            strcpy(p, tmp);
            p_next_arg += 4;
            p += strlen(tmp);
            break;
        case 's':
            break;
        default:
            break;
        }
    }

    return (p - buf);
}
```

**参数：**
`char *buf, const char *fmt, va_list args`

a) buf - 指向存储结果 C 字符串的缓冲区的指针。

b) fmt - 包含格式字符串的 C 字符串，该格式字符串遵循与 printf 中的格式相同的规范

c) args - 标识用 va_start 初始化的变量参数列表的值。

3 . `write(buf, i);`
写操作，把buf中的i个元素的值写到终端

```asm
write:
     mov eax, _NR_write
     mov ebx, [esp + 4]
     mov ecx, [esp + 8]
int INT_VECTOR_SYS_CALL
```
4 . `int INT_VECTOR_SYS_CALL`
这个表示要通过系统来调用sys_call函数

实现
```asm
sys_call:
     call save
     push dword [p_proc_ready]
     sti
     push ecx
     push ebx
     call [sys_call_table + eax * 4]
     add esp, 4 * 3
     mov [esi + EAXREG - P_STACKBASE], eax
     cli
     ret
```

5 . 字符显示驱动子程序：从ASCII到字模库到显示vram（存储每一个点的RGB颜色信息）。
6 . 显示芯片按照刷新频率逐行读取vram，并通过信号线向液晶显示器传输每一个点（RGB分量）。

### 8.4 getchar的实现分析
#### 8.4.1 getchar的实现
getchar等调用read系统函数，通过系统调用读取按键ascii码，直到接受到回车键才返回。

```cpp
int getchar(void)
{
    static char buf[BUFSIZ];
    static char *bb = buf;
    static int n = 0;
    if (n == 0)
    {
        n = read(0, buf, BUFSIZ);
        bb = buf;
    }
    return (--n >= 0) ? (unsigned char)*bb++ : EOF;
}
```

1）运行到getchar函数时，程序将控制权交给os。 当键入时，内容会缓慢进入一英寸并在屏幕上回。 按 enter 通知 os 输入完成，然后将控制权返回给程序。

2) 异步异常-键盘中断处理：键盘中断处理子程序。 接受按键扫描码转换为ascii码并保存到系统的键盘缓冲区。

3）getchar调用read系统函数，通过系统调用读取key的ascii码，直到收到回车键才返回。

### 8.5本章小结
Linux 基于 Unix I/O 架构，提供了一组有限的系统级功能，允许程序打开、关闭、读写文件、检索文件信息和执行 I/O 重定向。 所有的 I/O 设备，包括网络、磁盘和终端，都被表示为文件，所有的输入和输出都是通过读写相关文件来完成的。

## 结论
1. hello.c源代码使用cpp预处理，输出文件为hello.i
2. hello.i 文件使用 gcc -S 编译，输出文件为 hello.s
3. 使用as汇编的hello.s文件，输出文件为hello.o
4. 使用readelf解析elf头并输出到hello_o_elf.txt
5. hello.o的目标代码与hello.s的分析对比
6. hello.o 使用 ld 链接，输出文件是 hello 可执行文件
7. edb中分析hello程序的执行
8. hello.o和hello的目标代码dump对比分析
9. hello 使用 fork 和 execve 分析以及 shell 如何执行程序
10. hello 程序在 shell 中执行时经过各种测试

a) 计算机系统由硬件和系统软件组成，它们共同运行hello程序。计算机内部的信息表示为一组位，根据上下文以不同的方式解释。 hello 由其他程序翻译成不同的形式，从 ASCII 源代码文本开始，然后由编译器和链接器翻译成 hello.s 和 hello.o 成一个 hello 二进制可执行文件。

b) 机器级程序和用汇编代码表示的程序在很多方面都与 C 程序不同。不同数据类型之间的差异非常小。一个程序被表示为一系列指令，每个指令执行一个操作。

c) 异常是由处理器中的事件触发的控制流的突然变化。控制流被传递给一个软件处理程序，它执行一些处理，然后将控制权返回给被中断的控制流。

d) 内存系统是计算机系统中对应用程序员最明显的部分之一。 有从逻辑地址到物理地址的地址转换。

e) 在操作系统和应用程序之间的接口上，应用程序可以创建一个hello子进程，等待其子进程停止或终止，运行一个新的程序，并捕获来自其他进程的信号。

## 附件
<table  class="table">
<tr>
<td>文件名</td>
<td>说明</td>
<td>与章节有关</td>
</tr>

<tr>
<td>hello.c</td>
<td>源代码</td>
<td>第2章</td>
</tr>

<tr>
<td>hello.i</td>
<td>预处理文件</td>
<td>第2章</td>
</tr>

<tr>
<td>hello.s</td>
<td>汇编语言文件</td>
<td>第3章</td>
</tr>

<tr>
<td>hello.o</td>
<td>编译文件</td>
<td>第3章</td>
</tr>

<tr>
<td>hello_o_elf.txt</td>
<td>hello.o的elf文件</td>
<td>第3章</td>
</tr>


<tr>
<td>hello_o_dump.txt</td>
<td>hello.o 的目标代码转储</td>
<td>第4章，第5章</td>
</tr>


<tr>
<td>hello_elf.txt</td>
<td>hello的elf文件</td>
<td>第5章</td>
</tr>


<tr>
<td>hello_dump.txt</td>
<td>hello 的目标代码转储</td>
<td>第5章</td>
</tr>


<tr>
<td>hello</td>
<td>最终编译的可执行文件</td>
<td>第5章，第6章</td>
</tr>

</table>

## 参考文献
[1] Brian W. Kernighan and Dennis M. Ritchie. 1988. The C Programming Language (2nd. ed.). Prentice Hall Professional Technical Reference.

[2] Randal E. Bryant and David R. O'Hallaron. 2015. Computer Systems: A Programmer's Perspective (3rd. ed.). Pearson.

[3] GCC online documentation - GNU Project - Free Software Foundation (FSF)
[https://gcc.gnu.org/onlinedocs/](https://gcc.gnu.org/onlinedocs/)

[4] Linux man pages online
[https://man7.org/linux/man-pages/index.html](https://man7.org/linux/man-pages/index.html)

[5]  edb-debugger: cross-platform AArch32/x86/x86-64 debugger
[https://github.com/eteran/edb-debugger](https://github.com/eteran/edb-debugger)

[6] Compiler – Wikipedia
[https://en.wikipedia.org/wiki/Compiler](https://en.wikipedia.org/wiki/Compiler)

[7] ELF-64 Object File Format. Version 1.5 Draft 2. 1998.
[https://www.uclibc.org/docs/elf-64-gen.pdf](https://www.uclibc.org/docs/elf-64-gen.pdf)

[8] The Bourne-Again Shell
[https://www.aosabook.org/en/bash.html](https://www.aosabook.org/en/bash.html)

[9] Virtual, Linear, and Physical Addresses
[http://www.on-time.com/rtos-32-docs/rttarget-32/programming-manual/x86-cpu/protected-mode/virtual-linear-and-physical-addresses.htm](http://www.on-time.com/rtos-32-docs/rttarget-32/programming-manual/x86-cpu/protected-mode/virtual-linear-and-physical-addresses.htm)

[10] 逻辑地址 线性地址 虚拟地址 物理地址关系-CSDN博客
[https://blog.csdn.net/icandoit_2014/article/details/87897495](https://blog.csdn.net/icandoit_2014/article/details/87897495)

[11] 80386 Programmer's Reference Manual -- Section 5.1 (mit.edu)
[https://pdos.csail.mit.edu/6.828/2005/readings/i386/s05_01.htm](https://pdos.csail.mit.edu/6.828/2005/readings/i386/s05_01.htm)

[12]  Everything is a file - Wikipedia
[https://en.wikipedia.org/wiki/Everything_is_a_file](https://en.wikipedia.org/wiki/Everything_is_a_file)

[13]  [转]printf 函数实现的深入剖析-博客园
[https://www.cnblogs.com/pianist/p/3315801.html](https://www.cnblogs.com/pianist/p/3315801.html)