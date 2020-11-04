# Filter

## 1. 简介

> Filter 被称为过滤器，其主要作用是对 Servlet 容器调用 Servlet 的过程进行过滤，从而在 Servlet 进行响应处理的前后实现一些特殊功能。

### 1.1 Filter工作图解

![image-20201104142718953](https://raw.githubusercontent.com/inconspicuousy-start/image/master/image-20201104142718953.png)

当用户通过浏览器访问服务器中的目标资源时，首先会交给对当前请求进行过滤的Filter，在 Filter 中进行过滤预处理操作，然后再将请求转发给目标资源。当服务器接收到这个请求后会对其进行响应，在服务器处理响应的过程中，也需要将响应结果经过滤器处理后，才发送给客户端。

> 注意：
>
> 不是说所有的请求经过Filter处理后都会再请求到Web资源，可能过滤器就会对当前请求进行处理后就直接返回给客户端了。

## 2. 相关方法

| 方法名称 | 方法签名                                                     | 描述                                    | 注意                                                         |
| -------- | ------------------------------------------------------------ | --------------------------------------- | ------------------------------------------------------------ |
| init     | default public void init(FilterConfig filterConfig) throws ServletException {} | 用于初始化过滤器                        | 开发人员可以在 init() 方法中完成与构造方法类似的初始化功能，如果初始化代码中要使用到 FillerConfig 对象，那么，这些初始化代码就只能在 Filler 的 init() 方法中编写，而不能在构造方法中编写。 |
| doFilter | public void doFilter(ServletRequest request, ServletResponse response,                      FilterChain chain)         throws IOException, ServletException; | 处理请求                                | 1、doFilter() 方法有多个参数，其中，参数 request 和 response 为 Web 服务器或 Filter 链中的上一个 Filter 传递过来的请求和响应对象。<br/>2、参数 chain 代表当前 Filter 链的对象，Filter链对象可以理解为多个Filter的集合。只有在当前 Filter 对象中的 doFilter() 方法内部需要调用 FilterChain 对象的 doFilter() 方法，才能把请求交付给 Filter 链中的下一个 Filter 或者目标程序处理。<br/>3、在chain调用doFilter之前的处理就是对请求进行前置过滤处理；在chain调用doFilter之后的处理就是对请求进行后置过滤处理。 |
| destroy  | default public void destroy() {}                             | 在 Web 服务器卸载 Filter 对象之前被调用 | 该方法用于释放被 Filter 对象打开的资源，例如关闭数据库和 I/O 流。 |

> 注意：
>
> 1、三个方法都是可以表现 Filter 生命周期的方法。
>
> 2、 init() 方法在 Web 应用程序加载时会被调用，只会被调用一次。
>
> 3、destroy() 方法在 Web 应用程序卸载（或关闭）时被调用，只会被调用一次。
>
> 4、 doFilter() 方法会被调用多次（只要客户端有请求时就会被调用），Filter 所有的工作集中在 doFilter() 方法中。

## 3. 注册Filter

常用的注册Filter到引用程序中有两个方法。

- 一个是在部署描述文件web.xml中注册；
- 一个是通过@WebFilter注解注册；

### 3.1 web.xml注册Filter

- Filter代码

```java
package com.inconspicuousy.filter;

import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * @author peng.yi
 */
public class FirstFilter extends HttpFilter {

    public FirstFilter() {
        System.out.println("第一个Filter的构造器");
    }

    @Override
    protected void doFilter(HttpServletRequest req, HttpServletResponse res, FilterChain chain) throws IOException, ServletException {
         System.out.println("第一个Filter的前置过滤处理请求的方法");
        // 该方法就是对将请求交给下一个过滤器处理或者是交给Servlet处理
        super.doFilter(req, res, chain);
        // 可以在调用完doFilter之后，对Servlet的响应进行处理
        System.out.println("第一个Filter后置过滤请求的方法");
    }

    @Override
    public void init(FilterConfig config) throws ServletException {
        super.init(config);
        System.out.println("第一个Filter的初始化方法");
    }

    @Override
    public void destroy() {
        System.out.println("第一个Filter的销毁方法");
    }
}
```

- 处理请求的Servlet代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * @author peng.yi
 */
@WebServlet(value = "/filter")
public class FilterServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("FilterServlet 接收到请求");
    }
}
```

- web.xml对Filter的配置

```xml
<filter>
        <!-- 定义Filter的名称 -->
        <filter-name>firstFilter</filter-name>
        <!-- 定义Filter的全路径名-->
        <filter-class>com.inconspicuousy.filter.FirstFilter</filter-class>
    </filter>
    <filter-mapping>
        <!-- 定义当前过滤器映射对应的Filter的名称 -->
        <filter-name>firstFilter</filter-name>
        <!-- 定义当前Filter过滤的路径 -->
        <url-pattern>/filter</url-pattern>
    </filter-mapping>
```

- Tomcat部署好项目后启动，控制台打印信息

```
第一个Filter的构造器
第一个Filter的初始化方法
```

> 从这里我们可以发现，Filter和Servlet注册时的不同之处。Servlet在项目启动时，不会触发创建对象和初始化，只有等到Servlet容器解析请求指向当前Servlet时才会触发；Filter创建对象和初始化过程是在项目启动时就立即触发。

- GET请求地址

```http
http://localhost:8080/servlet_xml_war_exploded/filter
```

- 控制台输出信息

```
第一个Filter的前置过滤处理请求的方法
FilterServlet 接收到请求
第一个Filter后置过滤请求的方法
```

> 从这里我们可以发现，调用chain.doFilter前后的处理执行的时机。之前的处理就是请求前置过滤处理，之后的处理就是请求后置过滤处理。
>
> 再次请求地址时，打印的信息不变，可以发现，Filter的doFilter过程没经过一次请求都会被执行一次，而创建Filter对象和初始化过程只会执行一次。

- 关闭服务器，销毁容器，控制台输出信息

```
第一个Filter的销毁方法
```

### 3.2 注解的方式注册Filter

其他代码和web.xml注册一致，只需要删除web.xml中的配置信息，在FirstFilter类上添加@WebFilter注解即可。

```java
// 通过value、urlPatterns属性设置过滤器的虚拟映射路径
@WebFilter(value = "/filter")
public class FirstFilter extends HttpFilter {
```

## 4. Filter映射方式

这里的映射方式指的就是过滤器匹配到的进行过滤的请求。对于影响Filter过滤请求的配置有两种。

- 利用urlPatterns属性进行配置，对应web.xml中url-pattern元素标签。
- 利用dispatcherTypes属性进行配置，对应web.xml中的filter-mapper的子元素标签disparcher。

Filter一般只需要配置urlPatterns即可， dispatcherTypes默认是REQUEST，即请求直接访问资源时，才会触发过滤器。

### 4.1 urlPatterns属性配置映射方式

```java
// 通过value、urlPatterns属性设置过滤器的虚拟映射路径
@WebFilter(urlPatterns = "/*")
public class FirstFilter extends HttpFilter {
```

这里采用通配符的方式表示该过滤器会作用到所有的请求。

### 4.2 dispatcherTypes属性配置映射方式

```java
// 通过value、urlPatterns属性设置过滤器的虚拟映射路径
// 通过dispatcherTypes属性配置该过滤器作用什么请求分发类型的请求
@WebFilter(urlPatterns = "/*", dispatcherTypes = DispatcherType.REQUEST)
public class FirstFilter extends HttpFilter {
```

DispatcherType的值列表：

| 名称    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| FORWARD | 如果目标资源通过 RequestDispatcher 的 forward() 方法访问，那么该过滤器将会被调用。 |
| INCLUDE | 如果目标资源通过 RequestDispatcher 的 include() 方法访问，那么该过滤器将会被调用。 |
| REQUEST | 当用户直接访问页面时，Web 容器将会调用过滤器。               |
| ASYNC   | 当请求为异步处理请求时才会被触发。                           |
| ERROR   | 如果目标资源通过声明式异常处理机制调用，那么该过滤器将会被调用。 |

