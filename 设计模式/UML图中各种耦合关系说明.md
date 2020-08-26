## UML图中各种耦合关系说明

### 开头声明

> 以下UML类图都是依赖idea中Diagrams功能, 与实际的类图会有些许的差别, 比如组合和聚合, idea中都是用实心菱形表示, 但是实际的类图中, 聚合采用的是空心菱形, 组合采用的是实心菱形.

### 依赖关系

> 只要类中用到了对方, 那么他们之间就存在依赖关系(Dependence).

#### 类图

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master//%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/20200826173204.png)

#### 代码演示

```java
package com.inconspicuousy.uml.dependency;

class A {}
class B extends A {}
class C {}
class D {}

class E {
    // 成员变量
    private A a;

    // 方法参数
    public E(A a) {
        this.a = a;
    }

    // 方法参数 + 返回值 + 局部变量
    public B method(C c) {
        D d = new D();
        return new B();
    }

}

/**
 * UML图中依赖关系
 * @author peng.yi
 */
public class DependencyExample {
}
```

### 泛型关系

> 泛型关系(Generalization)也叫继承关系

#### 类图

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master//%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/20200826174001.png)

#### 代码演示

```java
package com.inconspicuousy.uml.generalization;

class A {}
class B extends A{}

/**
 * 泛型关系-继承关系
 * @author peng.yi
 */
public class GeneralizationExample {
}
```

### 实现关系

> 实现关系(implementation)就是类A实现B接口

#### 类图

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master//%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/20200826174407.png)

#### 代码演示

```java
package com.inconspicuousy.uml.implementation;

interface A { }
class B implements A {}

/**
 * 实现关系
 * @author peng.yi
 */
public class ImplementationExample {
}
```

### 关联关系

> 关联关系(Association)实际上类与类之间的联系

#### 关联关系的特征

1. `导航性`- 即关系是双向关系还是单项关系

2. `多重性`- 表示对象之间`逻辑数量`关系, 比如Car与Tire存在关联关系, 一个Car有四个Tire那么就对应UML关联关系就是4, 注意这里是逻辑数量与实际类与类之间定义的关联关系个数无关.
   - "0..."表示0个或者多个
   - "m..."表示至少m个
   - "1"表示有且仅有一个
   - "0,1"表示0个或者一个个

#### 类图

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master/设计模式/20200826181415.png)

#### 代码演示

```java
package com.inconspicuousy.uml.association;

// 单向关系
class A { B b;}
class B {}

// 双向关系
class C {D d;}
class D {C c;}


/**
 * 关联关系
 * @author peng.yi
 */
public class AssociationExample {
}
```

### 聚合关系

> 聚合关系(Aggregation)表示的是整体和部分的关系, 整体和部分可以分开. 聚合关系是关联关系的特例, 所以他具有`导航性与多重性`

整体和部分的关系: 一台电脑就是整体, 电脑包括的键盘还有鼠标就是部分,

所以电脑与键盘还有鼠标的关系就是整体和部分的关系, 并且鼠标能够从电脑上分离出来, 就表示整体和部分可以分开.

#### 类图

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master//%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/20200826185730.png)

代码演示

```java
package com.inconspicuousy.uml.aggregation;

class Computer {
    private Mouse mouse;
    private KeyBoard keyBoard;

    public void setMouse(Mouse mouse) {
        this.mouse = mouse;
    }

    public void setKeyBoard(KeyBoard keyBoard) {
        this.keyBoard = keyBoard;
    }
}
class Mouse {}
class KeyBoard {}

/**
 * 聚合关系
 * @author peng.yi
 */
public class AggregationExample {
}
```

### 组合关系

> 组合关系(Composition)表示的也是整体和部分的关系, 但是整体和部分不可以分开.

比如说, 人和人的头部就是典型的组合关系.

#### 类图

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master//%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/20200826192602.png)

#### 代码演示

```java
package com.inconspicuousy.uml.composition;

class Person {
    private Head head = new Head();
    private IdCard idCard;
}
class Head {}
class IdCard {}

/**
 * 组合关系示例
 * @author peng.yi
 */
public class CompositionExample {
}

```

