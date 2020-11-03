# RequestDispatcher

## 1. 简介

> 见名自其义，RequestDispatcher实现的是请求转发。
>
> RequestDispatcher对象由Servlet容器创建， 该对象用于接收客户端的请求并将请求发送到服务器上的任意资源（例如：Servlet，HTML文件或JSP文件）。因为对于RequestDispatcher对象来说，WEB资源都是可见的，可直接将请求转发到任意资源。
>
> RequestDispatcher对象可用于将请求转发到资源或将资源包括在响应中。 
>
> RequestDispatcher对象本质是对某个资源的包装， 当指定forward方法时，会将请求转发到包装的资源路径上。

## 2. 获取RequestDispatcher的三种方式

### 2.1  ServletContext#getRequestDispatcher

```java
public RequestDispatcher getRequestDispatcher(String path)
```

该方法返回一个RequestDispatcher对象，该对象充当位于给定路径的资源的包装，通过参数path指定资源的路径名。

> 注意：
>
> 1、路径名必须以"/"开头，"/"会被解析为相对于当前上下文的根路径。
>
> 2、如果ServletContext无法返回RequestDispatcher对象的话，则此方法返回null。
>
> 3、path指定的资源路径如果是文件资源（JSP，html）传递资源的绝对路径，如果是Servlet的话，那么传递的就是Servlet对应的urlPatterns解析的访问路径。

- Servlet代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * 请求转发
 *
 * @author peng.yi
 */
@WebServlet(value = "/dispatcher")
public class DispatcherServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取到 ServletContext上下文对象
        ServletContext servletContext = getServletContext();
        // 通过RequestDisPatcher对象对dispatcher.html进行包装
        RequestDispatcher requestDispatcher = servletContext.getRequestDispatcher("/dispatcher.html");
        // 利用forward方法实现请求的转发到指定的路径，此时请求会被转发到dispatcher.html上
        requestDispatcher.forward(req, resp);
    }
}
```

当前 `dispatcher.html` 文件位于webapp目录下，相当于在根路径下，当GET请求DispatcherServlet时，会被直接转发到dispatcher.html资源上。

### 2.2  ServletContext#getNamedDispatcher

```java
public RequestDispatcher getNamedDispatcher(String name)
```

该方法返回一个RequestDispatcher对象，该对象实现对指定名称对应的Servlet的包装。

> 注意：
>
> 1、如果ServletContext由于任何原因无法返回RequestDispatcher对象，则此方法返回null。
>
> 2、Servlet名称可以通过web.xml中指定，也可通过WebServlet注解的name属性指定，WebServlet注解注册的Servlet默认的Servlet名称是全类名（包名+类名）。
>
> 3、JSP页面是一种特殊形式的Servlet，所以该方法也可以是某个JSP页面的包装。

- Servlet代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Optional;

/**
 * @author peng.yi
 */
@WebServlet(value = "/named-dispatcher")
public class NamedDispatcherServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取到SevletContext上下文对象
        ServletContext servletContext = getServletContext();
        // 获取到Servlet名称(默认Servlet的名称为全类名)
        String servletName = "com.inconspicuousy.servlet.DispatcherServlet";
        // 根据名称获取到Servlet包装的RequestDispatcher对象
        RequestDispatcher namedDispatcher = servletContext.getNamedDispatcher(servletName);
        // 我们将请求转发到DispatcherServlet
        namedDispatcher.forward(req, resp);
    }
}
```

当前GET请求到NamedDispatcherServlet时会被转发到DispatcherServlet时，然后再次转发到dispatcher.html资源路径上。

### 2.3 ServletRequest#getRequestDispatcher

```java
public RequestDispatcher getRequestDispatcher(String path)
```

该方法与 `ServletContext#getRequestDispatcher` 类似，不同在于该处的资源路径可以是相对路径，不一定是以 "/"开头。

- Servlet代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * 利用Servlet对象获取到RequestDispatcher对象
 * @author peng.yi
 */
@WebServlet(value = "/servlet-dispatcher")
public class ServletDispatcherServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // dispatcher 会被解析成 /dispatcher, 因为当前Servlet的路径就是 /servlet-dispatcher, 相对路径的话就是 dispatcher
        // /dispatcher对应的Servlet就是DispatcherServlet
        RequestDispatcher requestDispatcher = req.getRequestDispatcher("dispatcher");
        if (requestDispatcher != null) {
            // 当前请求就会被转发到 DispatcherServlet
            requestDispatcher.forward(req, resp);
        }
    }
}
```

当GET请求访问 `/servlet-dispatcher` 会被转发到 `/dispatcher` ，然后再次会被转发到 ` /dispatcher.html`资源路径。