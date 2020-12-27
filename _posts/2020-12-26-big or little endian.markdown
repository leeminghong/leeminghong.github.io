---
layout:     post
title:      "Big/Little Endian"
subtitle:   " \"SW Arch| Endian 大小端\""
date:       2020-12-26 11:34:00
author:     "Minghong"
header-img: "img/in-post/endian/post-bg-endian.jpg"
catalog: true
tags:
    - 嵌入式
    - 软件架构
    - 大小端
---


大端小端是定义计算机中数据存放的顺序。多于1个字节的数据类型，如int32，需要固定字节顺序模式，以方便处理器处理。小端是数据的字符尾端放在低地址内存中（"低尾端"），大端是数据的字符尾端放在高地址内存中（“高尾端”）。
 ![*example*](http://leeminghong.github.io/img/in-post/endian/endian.png)


### 技术提要
1).**小端**-低尾端; **大端**-高尾端, 尾部字节放在高地址，和我们的阅读习惯相同。  
2).intel,arm处理器通常是小端架构，PowerPC 是大端架构。

### 1.编写代码
Example: x86/x64架构的intel芯片中，编写程序测试芯片架构是大端还是小端。
IDE：Visual Stdio.

```cpp
#include<stdio.h>

int main()

{

int val = 0x12345678;

printf("test value:%x\n", val);

unsigned char* m = &val;

if (*m == 0x78)

  printf("x86 system is Little-Endian!\n");

else if (*m == 0x12)

  printf("x86 system is Big-Endian!\n");

else

  printf("Error, please check the code!");

for (int j = 0; j < sizeof(int); j++, m++)

  printf("Address:%p, data:0x%x\n", m, *m);

return;

}
```

### 2.输出结果
打印输出结果如下:
![*print*](http://leeminghong.github.io/img/in-post/endian/endian-print.png)
结果显示：
原数据为0x12345678，尾端字符“0x78”放在较低的地址内存单元中，证明Intel是Little-Endian,小端模式;



<p id = "build"></p>
