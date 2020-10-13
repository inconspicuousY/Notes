# Tomcat10找不到HttpServlet实例的问题

> 情景描述：
>
> 无论是普通的JavaEE项目还是Maven项目，在进行Servlet开发过程中， 我们都要对servlet-api相关包进行引入。
>
> 普通的JavaEE项目，常采用的方式就是以外部Jar的形式引入到项目中，而Maven项目的话常采用就是引入provided生命周期的 `javax.servlet-api` 依赖.
>
> 在编写Servlet类时，我们直接将类继承 `javax.servlet.http.HttpServlet` 就可以对Servlet进行开发了，原理也很简单， 在将项目部署到Tomcat服务器时，Tomcat安装目录下的lib目录下就自带了servlet相关的Jar包，实际在项目运行时，HttpServlet就是调用的Tomcat自带的HttpServlet类。
>
> 但是我们将代码部署到Tomcat10时居然会报错，是什么原因导致的呢？

## 1. 错误分析

### 1.1 图例

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master/20201012174426.png)

### 1.2 分析

这里具体的错误原因就是找不到 `javax.servlet.http.HttpServlet`类，但是我们自定义的Servlet继承于该类，我们导致出错，Servlet实例化失败。

其实在Tomcat10以前的版本中， HttpServlet确实是在 `javax.servlet.http`包下的， 但是在Tomcat10中，将HttpServlet相关的实现类都移到了 `jakarta.servlet.http`包下了，导致找不到 `javax.servlet.http.HttpServlet`类，所以就导致出错了。

## 2. 解决问题

因为类找不到而引发的问题， 解决方式也就很简单了， 直接将正确的包引入到项目中即可。这里提供两种方式解决问题。

### 2.1 方式一：更换低版本的Tomcat

因为有时候涉及到代码无法更改的情况，只能对项目环境进行处理， 所以只能更换到低版本的Tomcat。

### 2.2 方式二：直接继承jakarta包下的HttpServlet

#### 2.2.1 引入相关的依赖包

- 非maven普通项目

直接将tomcat10添加到项目依赖中即可。

- maven项目引入如下依赖

```xml
         <!-- https://mvnrepository.com/artifact/jakarta.servlet/jakarta.servlet-api -->
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>5.0.0</version>
            <scope>provided</scope>
        </dependency>
```

这里引入的就是Jakarta包下面的servlet相关的jar包，而不再是javax包下面的。

#### 2.2.2 Servlet示例

```java
package com.inconspicuousy.servlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

/**
 * @author peng.yi
 */
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().println("<><><><<><><><<><><><>>>>>><<<<>>>>>>>");
    }
}
```

这种方式适合项目初期可以更改代码，改动量不大的情况采用。

> 注意：采用该方式的话，我们直接引入jakarta相关的Jar包即可， 不需要再像以前一样引入javax.servlet-api相关的Jar包或者依赖了。

### 