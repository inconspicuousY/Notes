# c Primer Plus之控制语句：循环

## 1. while循环

### 1.1 代码示例

```c
#include <stdio.h>

/**
 * while循环: 根据用户键入的整数求和
 * @return
 */
int main(void) {

    int status, num, sum;

    printf("请输入一个整数\n");
    printf("如果按q退出程序\n");
    // 用status检测用户输入的值是否有效
    // 如果输入的是非整数,那么就会读取失败,status = 0
    sum = 0;
    status = scanf("%d", &num);
    while (status > 0) {
        sum += num;
        printf("如果按q退出程序\n");
        status = scanf("%d", &num);
    }
    printf("sum = %d", sum);

    return 0;
}
```

- 输出结果

```
请输入一个整数
如果按q退出程序
12
如果按q退出程序
13
如果按q退出程序
14
如果按q退出程序
15
如果按q退出程序
q
sum = 54
```

该程序的作用是监听用户的每次输入，将用户每次输入的整数进行合并。

> 注意：
>
> 一个分号也是一个语句，所以当while循环后面直接跟分号而不是花括号时，表示while的循环体为空循环。

## 2. for循环

### 2.1 代码示例

```c
#include <stdio.h>

/**
 * for循环
 * @return
 */
int main(void) {

    const int NUMBER = 10;
    int count;
    for (count = 1; count < NUMBER; count++) {
        printf("Be my Valentine!\n");
    }

    return 0;
}
```

- 输出结果

```
Be my Valentine!
Be my Valentine!
Be my Valentine!
Be my Valentine!
Be my Valentine!
Be my Valentine!
Be my Valentine!
Be my Valentine!
Be my Valentine!
```

## 3. do-while循环

### 3.1 代码示例

```c
#include <stdio.h>

/**
 * do_while循环
 * @return
 */
int main(void) {

    const int secret_code = 13;
    int code_entered = 0;
    do {

        printf("当前code_entered的值为: %d \n", code_entered);
        ++code_entered;

    } while (code_entered <= secret_code);

    return 0;
}
```

- 输出结果

```
当前code_entered的值为: 0 
当前code_entered的值为: 1 
当前code_entered的值为: 2 
当前code_entered的值为: 3 
当前code_entered的值为: 4 
当前code_entered的值为: 5 
当前code_entered的值为: 6 
当前code_entered的值为: 7 
当前code_entered的值为: 8 
当前code_entered的值为: 9 
当前code_entered的值为: 10 
当前code_entered的值为: 11 
当前code_entered的值为: 12 
当前code_entered的值为: 13 
```

