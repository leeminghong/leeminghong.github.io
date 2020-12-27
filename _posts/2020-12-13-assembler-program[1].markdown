---
layout:     post
title:      "Assembler program[1]"
subtitle:   " \"asm program|汇编-环境搭建, 编译链接, Debug调试, ADD示例\""
date:       2020-12-12 18:23:00
author:     "Minghong"
header-img: "img/in-post/post-bg-asm.jpg"
catalog: true
tags:
    - 嵌入式
    - ASM汇编
---

> “Great thing is always simple, but not too simple. ”


嵌入式调试过程中会经常和计算机底层硬件打交道，会涉及到计算机内存，CPU寄存器，指令指针等相关概念。C语言调试无法直接窥视底层硬件状态，有时候就很难找到Bug,看二进制机器码很难理解程序，而汇编是介于高级语言C语言和机器码中间的编程语言，是最接近机器码的翻译语言，可以直观地查看和分析计算要执行的操作，有利于我们快速解决嵌入式开发遇到的各种问题。

这篇文章是关于学习和巩固asm汇编的相关知识的笔记。

#### 1.搭建汇编调试环境
搭建汇编编译和调试环境, 参考[汇编masm软件和相关软件的下载和使用](https://blog.csdn.net/weixin_44604887/article/details/106860445) ，这里不再赘述。

#### 2.编写代码
Example: asm实现 SUM=X+Y
文件：addtion.asm, 最后放置和masm.exe link.exe 同一个目录下，否则找不到文件.

```
DATA SEGMENT ;建立DATA数据段  
     X DB 11H  
	   Y DB 23H  
	   SUM DW 2H  
DATA ENDS  ;DATA 数据段结束  
CODE SEGMENT  ;建立代码段  
ASSUME CS:CODE,DS:DATA ;说明与有关段寄存器相关的段标识  
START:  
MOV AX,DATA  
MOV DS,AX  
XOR AH,AH ;clear AH  
MOV AL,X  
ADD AL,Y ;X+Y  
ADC AH,0 ;进位存到高字节  
MOV SUM,AX ;存到SUM中  
MOV AH,4CH  
INT 21H ;返回调用  
CODE ENDS ;代码段结束  
       END START  ;程序结束  
```
#### 3.编译
编译生成目标文件*.obj
> masm addition.asm

编译如果不成功，可以根据编译的代码的行号去修正代码。
![*报错*](http://leeminghong.github.io/img/in-post/asm/asm-masm-error.png)

#### 4.链接
链接生成可执行文件*.exe
>link addition.obj

这里会提示输入可执行的文件名等。

#### 5.Debug调试
>debug addition.exe

进入调试界面，可以使用以下的命令来查看寄存器的值或者单步调试等等。
常用命令
>-t  //单步运行一次  
-r   //查看寄存器的值  
-d   //查看内存  
-u   //查看编译后所有的汇编指令  

初始化的DS数据段的基地址： 76AH,初始化值 X,Y,SUM分别是 11，23，02 [均为16进制]  
![*初始值*](http://leeminghong.github.io/img/in-post/asm/asm-masm-addition-init.png)

其中的调试过程
![*单步调试*](http://leeminghong.github.io/img/in-post/asm/asm-masm-addition.png)

在这个加法的example中，首先将X的值move到AX中，然后将Y的值直接和AX相加并存到AX寄存器中，最后再存到SUM，在DS中的偏移地址为2，结果为34.
![*结果*](http://leeminghong.github.io/img/in-post/asm/asm-masm-addition-result.png)
最后的数据X,Y,SUM 分别为11，23，34.
这样就实现了简单的汇编加法。

<p id = "build"></p>
