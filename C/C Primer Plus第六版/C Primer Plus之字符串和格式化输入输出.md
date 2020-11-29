# C primer Plus之字符串和格式化输入输出

## 1. 前导程序

```c
#include <stdio.h>
// 引入String字符串的头文件
// 提供strlen()函数的原型
#include <string.h>
// #define指令 也叫做预处理器指令
// 当前预处理指令提前定义常量DENSITY定义为62.4, 方便代码中直接引入
#define DENSITY 62.4

int main(void) {

    char name[40];
    int age, size, letters;
    printf("%f", DENSITY);


    // 处理姓名
    printf("你叫什么名字? \n");
    scanf("%s", name);
    // 处理年龄
    printf("你有多少岁? \n");
    scanf("%d", &age);

    // 获取数组的大小
    size = sizeof(name);
    // 获取数组中存储字符的实际长度, 也就是字符串的长度
    letters = strlen(name);

    printf("你的姓名由%d个字符组成, %s \n", letters, name);
    printf("当前有多少%d个字节存储它", size);

    return 0;
}
```

### 1.1 程序解读

- 引入String字符串头文件，提供操作字符串的相关函数

```c
#include <string.h>
```

- 预处理指令

```c
#define DENSITY 62.4
```

通过 `#define`关键字定义预处理指定，预处理指令表示在程序之前的准备工作。

`#define DENSITY 62.4`表示在程序运行之前，提前定义好 `DENSITY`常量，并将其赋值。值得注意的是，通过预处理指令定义的常量是不能修改的，只能在程序中引用。`printf("%f", DENSITY);`就是程序中对预处理指令中定义的常量的引用。

- 数组的声明

```c
char name[40];
```

声明一个可以存储40个字符的字符型数组，当前使用字符数组存储字符串。

数组由连续的存储单元组成，字符串中的字符被存储在相邻的存储单元中，每个单元存储一个字符。

- 字符串使用scanf函数赋值，与字符串的格式化输出

```c
scanf("%s", name);
printf("你的姓名由%d个字符组成, %s \n", letters, name);
```

字符串使用 `%s` 来赋值和打印。值得注意的是，使用scanf为字符串赋值时，与对基本数据类型的变量赋值不同的是没有借助 `&`符号，比如 `scanf("%d", &age);`语句中，对age进行赋值时，就用到了 `&`。

- 获取数组的大小

```c
 size = sizeof(name);
```

借助内置运算符sizeof，可以获取到数组的大小。

- 获取字符串的长度

```c
 letters = strlen(name);
```

借助strlen函数获取到字符串的长度。

## 2. 字符串

### 2.1 字符串的特点

- 字符串是一个或多个字符的序列。
- 字符串用双引号包裹。
- C语言中没有专门用于存储字符串的变量类型，字符串都被存储在char类型的数组中。