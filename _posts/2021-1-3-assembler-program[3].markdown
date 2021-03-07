---
layout:     post
title:      "Assembler program[3]"
subtitle:   " \"asm program|栈\""
date:       2021-1-3 16:50:00
author:     "Minghong"
header-img: "img/in-post/post-bg-asm.jpg"
catalog: true
tags:
    - 嵌入式
    - ASM汇编
    - 栈
---

> “Great thing is always simple, but not too simple. ”


今天使用汇编实现实现栈的Push和Pop。


### 技术提要
1).栈：栈底为高地址，栈顶为低地址，栈是倒着向上生长的。  


### 1.编写代码
Example: asm实现数据的压栈和出栈，观察数据进出的顺序。
需要初始化一个栈区 STACK SEGMENT,16个字节。

```
DATA SEGMENT ;建立DATA数据段
  X DB 1AH ;
  Y DB 2BH ;
  NEWLINE DB 13,10,'$'
  STRING  DB "POP function executed!$"
DATA ENDS  ;DATA 数据段结束

STACK SEGMENT;建立stack栈
byte 16 dup(18H)
STACK ENDS

CODE SEGMENT  ;建立代码段
ASSUME CS:CODE,DS:DATA,SS:STACK;说明与有关段寄存器相关的段标识
START:
MOV AX,DATA ;
MOV DS,AX ;加载data segment地址 到DS register
XOR AX,AX ;clear AX
XOR AX,AX ;clear AX
XOR BX,BX ;clear BX

MOV AL,X
MOV BL,Y

PUSH AX ;
PUSH BX ;

POP AX
POP BX

MOV DX,OFFSET STRING ;调用打印数据
MOV AH,09
INT 21H

MOV AH,4CH
INT 21H ;返回调用

CODE ENDS ;代码段结束
  END START  ;程序结束
```

### 2.编译，链接
编译masm和链接link生成目标文件*.obj，可执行文件 *.exe, (ml stack.asm)
> masm stack.asm  
 link stack.obj

### 3.Debug 调试
>debug stack.exe

#### 1) push 入栈/压栈
![*push*](http://leeminghong.github.io/img/in-post/asm3/push.png)
AX,BX 初始化之后内容分别为0x001A, 0x002B, 依次使用Push命令入栈。栈基地址：SS=0769, 栈指针向上移动SP：0 -> 0xfffc，指向0x2B。 -d 769:FFFC可以查看栈内容。注：X86是小端，低字节在低地址。

#### 2) pop 出栈
![*pop_a*](http://leeminghong.github.io/img/in-post/asm3/pop_a.png)
由于栈指针指向0x2B, 所以使用pop BX, 会将0x2B,0x00放到AX中， 栈指针向下移动， SP:0XFFFC -> 0XFFFE

![*pop_b*](http://leeminghong.github.io/img/in-post/asm3/pop_b.png)
pop BX, 会将栈内容0x1A,0x00放到BX 中， 栈指针向下移动， SP:0XFFFE -> 0

### 4.执行结果
![*done*](http://leeminghong.github.io/img/in-post/asm3/done.png)
执行完成，打印状态.
-g 命令是全速运行。

<p id = "build"></p>
