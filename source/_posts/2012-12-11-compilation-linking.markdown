---
layout: post
title: "编译和链接"
date: 2011-10-11 15:21
comments: true
categories: [C/C++, Linux]
---

日常工作中习惯了使用集成开发环境(IDE)，对于程序的构建过程没有清晰的认识和深刻的理解，这导致我对相对底层的一直基本原理不够清楚，因此有必要对此进行一次透彻的学习。近期刚好在阅读[《程序员的自我修养》][1]这本书，就算是对本书内容的学习总结吧！
<!-- more -->

被隐藏了的过程
----------------

以c语言的“Hello, World!”程序为例:  
{% include_code hello.c lang:c c/hello.c %}
在linux下，我们使用gcc编译器进行编译，如下：
{% codeblock %}
$gcc hello.c
$./a.out
Hello, World!
{% endcodeblock %}
事实上，上述过程包含了多个步骤，分别是**预处理(Preprocessing)、编译(Compilation)、汇编(Assembly)**和**链接(Linking)**，如下图所示：

![gcc-compile-process]

### 1. 预编译
预编译的过程主要处理源代码中包含的预编译指令，比如“#include”、“#define”等等，基本的处理内容有以下几个方面：

   * 处理类似“#define”的预编译指令，将其删除，并展开所有的宏定义。 
   * 处理"#include"指令，将其包含的头文件插入到相应的位置，这里需要注意的是，插入的过程是递归的，包含的头文件中可能还包含其他文件。 
   * 删除所有的注释，即"//"或"/\*....\*/"。 
   * 添加行号和文件名标识，便于调试时显示相应的信息。 

Linux下，预编译可以使用如下命令进行：
```
$gcc -E hello.c -o hello.i
```
或者：
```
$cpp hello.c > hello.i
```
### 2. 编译
编译器把预编译后的文件进行词法分析、语法分析、语义分析以及优化后，生成相应的汇编代码文件，这个过程便是编译的过程。编译过程是程序构建中的核心部分，也是最复杂的部分之一。  
Linux下，编译过程可以使用如下命令进行：
```
$gcc -S hello.i -o hello.s
```
浏览下生成的hello.s汇编文件，其内容大致如下：（里面就是一些汇编代码，我只看懂那么几个指令:)）
{% include_code hello.s c/hello.s %}

现代的gcc版本将预编译和编译两个过程合二为一，使用一个叫做ccl的程序来完成此过程，该程序在/usr/lib/gcc/x86_64-linux-gnu/4.6.3/目录下(这是在我机器上的目录，版本不同，目录可能有所不同)，我们可以直接使用它:   
```
$/usr/lib/gcc/x86_64-linux-gnu/4.6.3/cc1 hello.c
```
当然，方便起见，可以直接使用以下命令:
```
$gcc -S hello.c -o hello.s
```
如此，都可以得到hello.s这个汇编代码文件。

### 3. 汇编
接下来，汇编器会将生成的汇编代码转变为计算机可以执行的指令，每一句汇编代码几乎对应一条机器指令，因此，这个过程只是一个简单的翻译过程，相对其他的过程来说是非常简单的。  
汇编过程通过调用汇编器as来完成：
```
$as hello.s -o hello.o
```
或者使用gcc的-c参数：
```
$gcc -c hello.s -o hello.o
```
当然了，可以一步搞定，直接从源代码开始经过预编译、编译和汇编输出**目标文件**：
```
$gcc -c hello.c -o hello.o
```
### 4. 链接
**未完待叙...**


[1]: http://book.360buy.com/10067200.html
[gcc-compile-process]: /images/blog/cmpsys/gcc-compile-process.png (GCC 编译过程)
