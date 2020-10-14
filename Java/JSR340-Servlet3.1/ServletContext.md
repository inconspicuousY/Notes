

# ServletContext

## 1. 简介

> `ServletContext`是一个接口，称为Servlet上下文。
>
> ServletContext定义了一组Servlet与其容器进行通信的方法，例如获取MIME文件类型、进行请求的调度、写入日志等。

## 2. 作用范围

> 每个Java虚拟机的每个Web应用程序都有一个上下文。
>
> 注意：
>
> 1. 该处的上下文有且仅有一个，我们可以通过该上下文实现全局数据共享，多个Servlet之间的消息传递。
> 2. 一个Java虚拟机中可以存在多个Web应用程序，也就是说会存在多个上下文，在这种情况下，上下文不能用作共享全局信息，应该采用外部资源存储数据信息，例如数据库。
>
> 比如如果启动一个Tomcat服务器， 该服务器下部署了多个Web应用程序，那么此时就是一个虚拟机中存在多个Web应用程序，Web应用程序之间上下文数据不共享。

## 3. 实例对象的产生

> `ServletContext` 对象包含在ServletConfig对象中，该对象在初始化Servlet时由Web服务器提供。

### 3.1 获取ServletContext对象。

因为ServletContext对象在ServletConfig对象中， 所以我们先获取ServletConfig对象然后获取ServletContext对象。

- 利用`Servlet`对象获取 `ServletConfig`对象。

```java
public ServletConfig getServletConfig();
```

- 利用`ServletConfig`对象获取到 `ServletContext`对象。

```java
public ServletContext getServletContext();
```

#### 3.1.1 代码演示

```java
package com.inconspicuousy.servlet;

import jakarta.servlet.ServletConfig;
import jakarta.servlet.ServletContext;
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
        // 获取到ServletContext对象必须先获取到ServletConfig对象
        ServletConfig servletConfig = this.getServletConfig();
        ServletContext servletContext = servletConfig.getServletContext();
        // 通过ServletContext我们可以通过方法获取到Servlet与容器之间交互的信息，一般都是请求的信息
        // 得到Session的超时时间
        int sessionTimeout = servletContext.getSessionTimeout();
        String text = servletContext.getMimeType(null);
        System.out.println("text = " + text);
        System.out.println("sessionTimeout = " + sessionTimeout);
        resp.getWriter().println("<><><><<><><><<><><><>>>>>><<<<>>>>>>>");
    }
}
```

> 注意，在编写Servlet时，一般都是继承于HttpServlet类，而HttpServlet中封装了一个方法 `getServletContext` 直接获取到ServletContext对象，但是实际底层还是先获取的ServletConfig对象，然后再获取的ServletContext对象。

```java
 @Override
    public ServletContext getServletContext() {
        ServletConfig sc = getServletConfig();
        if (sc == null) {
            throw new IllegalStateException(lStrings.getString("err.servlet_config_not_initialized"));
        }

        return sc.getServletContext();
    }
```

## 4. 初始化参数

> 初始化参数指的是Web服务器在初始化ServletContext上下文对象时给与上下文对象的一些初始化属性，本质上类似于面向用户的钩子（hook），在初始化初期用户可以自定义某些属性赋予上下文对象，然后可以在Servlet中获取该属性。

### 4.1 设置初始化参数

#### 4.1.1 web.xml中设置初始化参数

通过context-param标签设置初始化参数

```xml
 <!-- 设置多个初始化参数 -->
    <context-param>
        <param-name>key</param-name>
        <param-value>value</param-value>
    </context-param>

    <context-param>
        <param-name>key1</param-name>
        <param-value>value1</param-value>
    </context-param>
```

### 4.2 获取初始化参数

ServletContext对象中提供了一组方法进行获取初始化参数。

| 方法签名                             | 描述                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| String getInitParameter(String name) | 根据初始化参数的参数名获取到参数值，注意，当参数名传递为null时，会抛出NPE |
| Enumeration getInitParameterNames()  | 返回初始化参数的参数名列表枚举                               |

代码演示

```java
package com.inconspicuousy.servlet;

import jakarta.servlet.ServletConfig;
import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.util.Enumeration;

/**
 * @author peng.yi
 */
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // HttpServlet 提供的直接获取到ServletContext的方法(本质上还是通过ServletConfig获取到的)
        ServletContext servletContext = this.getServletContext();
        // 获取到所有的初始化参数的名称
        Enumeration<String> initParameterNames = servletContext.getInitParameterNames();
        while (initParameterNames.hasMoreElements()) {
            String next = initParameterNames.nextElement();
            // 根据参数名称获取到属性值
            String initParameter = servletContext.getInitParameter(next);
            System.out.println("initParameter = " + initParameter);
        }
        resp.getWriter().println("<<<<<<<<<<<<<<<<<<<<<>>>>>>>>>>>>>>>>>>>");
    }
}
```

当访问到helloServlet时，打印信息如下：

```
key1 = value1
key = value
```

## 5. 读取Web应用下的资源文件

> 在实际开发过程中，我们经常会需要读取Web应用中的一些资源文件，比如配置文件、图片等。为此，在ServletContext接口中定义了一些读取Web资源的方法。
>
> 这些方法是依靠Servlet容器实现的，Servlet容器根据资源文件相对应Web应用的路径，返回关联资源文件的I/O流或者资源文件在系统的绝对路径等。

### 5.1 方法列表

| 方法签名                                        | 方法描述                                                     |
| ----------------------------------------------- | ------------------------------------------------------------ |
| String getContextPath()                         | 返回Web应用的上下文路径，也就是浏览器访问到项目的相对路径    |
| Set&lt;String&gt; getResourcePaths(String path) | 返回一个Set集合，集合中包含指定资源目录中子目录和文件的路径名称，不包含子目录的下级文件 |
| String getRealPath(String path)                 | 返回指定的资源文件在服务器文件系统上的真实路径（绝对路径）   |
| URL getResource(String path)                    | 返回某个资源文件对应的URL对象                                |
| InputStream getResourceAsStream(String path)    | 返回某个资源文件对应的 InputStream 输入流（该路径应该指向某个具体的资源文件，这样才能获取到输入流， 否则返回null） |

### 5.2 代码演示

```java
package com.inconspicuousy.servlet;

import com.sun.scenario.effect.impl.sw.sse.SSEBlend_SRC_OUTPeer;
import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.*;
import java.net.URL;
import java.util.Set;

/**
 * @author peng.yi
 */
public class ReourceServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取到上下文对象
        ServletContext servletContext = this.getServletContext();
        // 返回Web应用的上下文路径，也就是浏览器访问到项目的相对路径
        String contextPath = servletContext.getContextPath();
        System.out.println("contextPath = " + contextPath);

        // 返回一个Set集合，集合中包含指定资源目录中子目录和文件的路径名称，不包含子目录的下级文件
        // 参数必须以/开始，表示根路径
        System.out.println("servletContext.getResourcePaths(\"/\")");
        Set<String> resourcePaths = servletContext.getResourcePaths("/");
        resourcePaths.forEach(System.out::println);

        System.out.println(" servletContext.getResourcePaths(\"/META-INF\")");
        Set<String> resourcePaths1 = servletContext.getResourcePaths("/META-INF");
        resourcePaths1.forEach(System.out::println);

        // 返回指定的资源文件在服务器文件系统上的真实路径（绝对路径）
        String realPath = servletContext.getRealPath("/");
        System.out.println("realPath = " + realPath);

        // 返回某个资源文件对应的URL对象
        URL resource = servletContext.getResource("/");
        System.out.println("resource = " + resource);

        // 返回某个资源文件对应的 InputStream 输入流
        // 该路径应该指向某个具体的资源文件，这样才能获取到输入流， 否则返回null
        InputStream resourceAsStream = servletContext.getResourceAsStream("/");
        System.out.println("resourceAsStream = " + resourceAsStream);

        InputStream resourceAsStream1 = servletContext.getResourceAsStream("/index.jsp");
        System.out.println(resourceAsStream1);

        // 打印index.jsp
        BufferedReader reader = new BufferedReader(new InputStreamReader(resourceAsStream1));
        String temp;
        while ((temp = reader.readLine()) != null) {
            System.out.println("temp = " + temp);
        }
    }
}
```

当我们访问到ResourceServlet时，打印结果如下：

```
contextPath = /servlet_xml_war_exploded
servletContext.getResourcePaths("/")
/META-INF/
/index.jsp
/WEB-INF/
 servletContext.getResourcePaths("/META-INF")
/META-INF/MANIFEST.MF
realPath = D:\Projects\Server\JSR340-Servlet\servlet-xml\target\servlet-xml-1.0-SNAPSHOT\
resource = file:/D:/Projects/Server/JSR340-Servlet/servlet-xml/target/servlet-xml-1.0-SNAPSHOT/
resourceAsStream = null
java.io.ByteArrayInputStream@429efab9
temp = <%--
temp =   Created by IntelliJ IDEA.
temp =   User: admin
temp =   Date: 2020/10/12
temp =   Time: 17:15
temp =   To change this template use File | Settings | File Templates.
temp = --%>
temp = <%@ page contentType="text/html;charset=UTF-8" language="java" %>
temp = <html>
temp = <head>
temp =     <title>Hello</title>
temp = </head>
temp = <body>
temp =     Hello Servlet-xml
temp = </body>
temp = </html>
```

