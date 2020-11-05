# C primer Plus之C语言概述

## 1. 第一个C语言程序

```c
/**
 * 创建第一个简单的C语言程序
 * Created by inconspicuousy on 2020/11/5.
 */

#include "stdio.h"

int main(void) {

    int num;

    num = 1;
    printf("我是一个简单的");
    printf("电脑。\n");
    printf("这是我编写的第%d个C语言程序。", num);
    return 0;
}
```

- 执行结果

```shell
我是一个简单的电脑。
这是我编写的第1个C语言程序。
```

## 2. 预处理指令和头文件

```c
#include "stdio.h"
```

一般来说，C语言程序的顶部都是由预处理指令和头部组成。

- 预处理指令

\#include 这行代码是一条C预处理器指令（preprocessor directive），通常，C编译器在编译前会对源代码做一些准备工作，即预处理 （preprocessing）。

- 头部

C程序顶部的信息集合被称为头文件（header）。 在大多数情况下，头文件包含了编译器创建最终可执行程序要用到的信息。简而言之，头文件帮助编译器把你的程序正确地组合在一起。

所有的C编译器软件包都提供stdio.h文件。该文件中包含了供编译器使用的输入和输出函数（如， printf()）信息。该文件名的含义是标准输入/输出头文件。

> `#include "stdio.h"` 的作用相当于把stdio.h文件中的所有内容都输入该行所在的位置，可以理解为这是一种拷贝-粘贴的操作。
>
> 这种方式也就是C语言模块化的核心，通过#include预处理指令，可以实现多个模块之间共享许多程序共有的信息。