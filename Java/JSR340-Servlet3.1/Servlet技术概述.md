# Servlet技术概述

## 1. 什么是 Servlet

> Servlet是基于Java技术实现的Web组件，是在Web服务器上运行的小型的Java程序，Servlet通常通过HTTP接收和响应来自Web客户端的请求，可以被基于Java技术实现的Web服务器动态加载并运行。
>

### 1.1 基于 Java 实现的 Web 服务器列表

- Tomcat
- Jetty

### 1.2 Servlet Jar包

- 普通的Java EE项目

一般我们项目中如果添加了 Tomcat Web 服务器配置的话， 会自动引入 Servlet 的 Jar 包（servlet-api.jar）。

- Maven工程

maven工程的话需要引入如下的依赖

```xml
<!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>
```

因为用到Servlet的Maven项目都是JavaEE项目， 最后项目都会被加载到Web服务器中运行，一般Web服务器会自动提供相应的Jar包，所以依赖的生命周期为provided。

> 注意：如果使用Tomcat10的话，由于Tomcat10将Servlet相关的类迁移至jakarta包下，所以引入的Jar文件不再是javax.servlet-api，而是jakarta.servlet-api，相关的maven依赖如下：
>
> ```xml
> <!-- https://mvnrepository.com/artifact/jakarta.servlet/jakarta.servlet-api -->
> <dependency>
>     <groupId>jakarta.servlet</groupId>
>     <artifactId>jakarta.servlet-api</artifactId>
>     <version>5.0.0</version>
>     <scope>provided</scope>
> </dependency>
> ```

## 2. 什么是Servlet容器

> Servlet容器也叫Servlet引擎，是Web服务器为支持Servlet功能扩展的部分。
>
> Servlet容器可以嵌入到宿主的Web服务器中，或者通过Web服务器的本地扩展API单独作为附加组件安装。

### 2.1 Servlet容器的作用

- 管理Servlet的生命周期。
- 提供基于请求、响应发送模型的网络服务

> 注：因为Servlet容器负责将HTTP请求转化成Request请求对象信息转交给对应的Servlet进行处理，然后将Servlet响应的Response响应对象转化成HTTP响应信息，所以Servlet容器必须基于HTTP协议的请求、响应模型，但是 基于HTTPS（HTTP over SSL）协议的请求、响应模型可以选择性的支持。

## 3. 图解Servlet处理请求

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master/1602319254(1).jpg)

## 4. Servlet3.0规范与低版本规范的兼容性

- 监听器Listener的顺序

Servlet3.0规范之前的版本，监听器以随机的顺序被调用。Servlet3.0之后，监听器可以指定被调用的顺序。

- 注解处理

从Servlet3.0规范之后，支持注解配置Servlet、Listener、Filter。

## 5. Servlet与其他技术的比较

简单来说，因为Servlet是基于Java实现的，具有Java的特性以及可以访问Java平台提供的大量的API。