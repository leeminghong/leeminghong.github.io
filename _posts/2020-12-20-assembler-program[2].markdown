---
layout:     post
title:      "Assembler program[2]"
subtitle:   " \"asm program|汇编-四则，循环\""
date:       2020-12-20 21:58:00
author:     "Minghong"
header-img: "img/in-post/post-bg-asm.jpg"
catalog: true
tags:
    - 嵌入式
    - ASM汇编
    - 编译
    - 循环，四则
---

> “Great thing is always simple, but not too simple. ”


今天使用汇编实现四则运算，循环和字符打印。


### 技术提要
1).计算机的减法是加法的拓展，除法和乘法又是移位加法和移位减法的延伸。  
2).编译是将高级语言代码翻译成机器指令，生成obj目标文件,链接是将目标文件和依赖库文件，相互的文件引用链接生成可以直接运行的exe文件。

### 1.编写代码
Example: asm实现加减乘除四则运算: X（+，-，*，/，&）Y， 保存结果. 实现6次循环loop和打印字符。

```
DATA SEGMENT ;建立DATA数据段
  X DB 0B4H ;byte
  Y DB 81H ;
  RES_0 DW 1a1bH
  RES_1 DW 2a2bH
  RES_2 DW 3a3bH
  RES_3 DW 4a4bH;word,double bytes
  NEWLINE DB 13,10,'$'
  STRING  DB "Execution Done!$"

DATA ENDS  ;DATA 数据段结束
CODE SEGMENT  ;建立代码段
ASSUME CS:CODE,DS:DATA ;说明与有关段寄存器相关的段标识
START:
MOV AX,DATA ;
MOV DS,AX ;加载data segment地址 到DS register
XOR AX,AX ;clear AX
XOR BX,BX ;clear BX

MOV AL,X
MOV BL,Y
ADC AX,BX ;带进位的加法：X+Y, AX+BX+CF -> AX
MOV RES_0,AX ;store

XOR AX,AX ;clear AX
MOV AL,X
SUB AL,BL ;减法：X-Y, AL-BL -> AL
MOV RES_1,AX ;store

XOR AX,AX ;clear AX
MOV AL,X
MOV AH,0
MUL BX;乘法：X*Y, AX*BX -> AX
MOV RES_2,AX ;store

XOR AX,AX ;clear AX
MOV AL,X
MOV AH,0
DIV BX ;除法：X/Y, AX/BX -> AX
MOV RES_3,AX ;store

MOV AL,X
MOV AH,0
AND AX,BX ;与：X&Y, AX&BX -> AX
MOV RES_3+1,AX ;store

MOV AH, 02H ;PRINT FUNC
MOV CX, 6 ;循环loop 6 times
MOV DL, 61H ;'a'

S:
INT 21H ;中断，打印
INC DL
LOOP S ;loop till CX -> 0

MOV DX,OFFSET NEWLINE ;先打印回车和换行
MOV AH,9 ;打印字符串
INT 21H

MOV DX,OFFSET STRING
MOV AH,09
INT 21H

MOV AH,4CH
INT 21H ;返回调用

CODE ENDS ;代码段结束
   END START  ;程序结束
```

### 2.编译，链接
编译masm和链接link生成目标文件*.obj，可执行文件 *.exe, (ml arithm.asm)
> masm arithm.asm  
 link arithm.obj

exe文件和汇编代码的对应关系
![*code_data*](http://leeminghong.github.io/img/in-post/asm2/arithmetic-code&data.png)

*Tips:*
> Mul,bx, 和 div,bx都是默认ax作为目标寄存器  
ADC AX,BX 带进位标志的加法，进位直接存到AX中

### 3.Debug 调试
>debug arithm.exe

以下的运算和调试也非常简单,代码注释比较详细，这里不展开，只展示调试结果。
#### 1) addition 加法
![*add*](http://leeminghong.github.io/img/in-post/asm2/arithmetic-adc.png)

#### 2) subtraction 减法
![*sub*](http://leeminghong.github.io/img/in-post/asm2/arithmetic-sub.png)

#### 3) multiplication 乘法
![*mul*](http://leeminghong.github.io/img/in-post/asm2/arithmetic-mul.png)

#### 4) division 除法
![*div*](http://leeminghong.github.io/img/in-post/asm2/arithmetic-div.png)

#### 5) and 与
![*and*](http://leeminghong.github.io/img/in-post/asm2/arithmetic-and.png)

#### 6) loop 循环
语法结构
MOV CX, 10 ;counter register
```
B:
   code...
LOOP B
```

![*loop*](http://leeminghong.github.io/img/in-post/asm2/arithmetic-loop.png)

### 4.执行结果
![*printdone*](http://leeminghong.github.io/img/in-post/asm2/arithmetic-printdone.png)
字符串尾部需要加$作为结束符.
![*result*](http://leeminghong.github.io/img/in-post/asm2/arithmetic-result.png)
结果可以看到“与”的结果放在了除法结果的后一个字节，因为这里直接使用"MOV RES_3+1,AX"。

<p id = "build"></p>
