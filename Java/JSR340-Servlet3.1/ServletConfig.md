# ServletConfig

## 1. 概述

> ServletConfig是Servlet容器使用的Servlet配置对象，用于在初始化期间将信息传递给Servlet。

简单来说，就是负责保存Servlet的配置信息， 包括Servlet初始化参数。

我们在Servlet接口中可以看到初始化方法

```java
public void init(ServletConfig config) throws ServletException;
```

在Servlet调用init方法初始化时，会作为方法参数传递给Servlet。

## 2. 方法列表

| 方法名                | 方法签名                                                 | 方法描述                                                     |
| --------------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| getServletName        | public String getServletName()                           | 返回此Servlet实例的名称。Servlet一般在web.xml应用程序部署描述符中配置，对于未注册的Servlet实例（比如通过注解进行配置的Servlet），Servlet的名称就是他的类的全路径名称。 |
| getServletContext     | public ServletContext getServletContext()                | 返回Web应用程序的Servlet上下文对象。                         |
| getInitParameter      | public String getInitParameter(String name)              | 获取给定名称的Servlet初始化参数的值，如果初始化参数不存在，则返回null。这里需要与ServletContext的获取初始化参数作对比，ServletContext获取的是Servlet上下文初始化参数，并且当传递的参数为null时，会抛出空指针异常。 |
| getInitParameterNames | public Enumeration&lt;String&gt; getInitParameterNames() | 以String对象的Enumeration的形式返回Servlet初始化参数的名称。如果Servlet没有初始化参数，则返回一个空的Enumeration。 |

## 3. 代码演示

> 这里采用注解方式定义两个Servlet，在Servlet初始化时调用ServletConfig 的各个方法。

ServletConfigServlet1

```java
package com.inconspicuousy.servlet;

import javax.jws.WebService;
import javax.servlet.ServletConfig;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebInitParam;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Enumeration;

/**
 * ServletConfig 类方法演示
 *
 * @author inconspicuousy
 * @date 2020/10/27
 */
@WebServlet(value = "/servlet-config-one", initParams = @WebInitParam(name = "key", value = "value"))
public class ServletConfigServlet1 extends HttpServlet {

    @Override
    public void init(ServletConfig config) throws ServletException {
        // 打印config对象，判断不同的Servlet初始化是否对应不同的ServletConfig对象
        System.out.println("config = " + config);
        // getServletName获取到Servlet名称， 在未指定Servlet名称时，默认是类名
        String servletName = config.getServletName();
        System.out.println("servletName = " + servletName);
        // getServletContext 获取到ServletContext
        ServletContext servletContext = config.getServletContext();
        System.out.println("servletContext = " + servletContext);

        // 获取到初始化参数名称列表
        Enumeration<String> initParameterNames = config.getInitParameterNames();
        while (initParameterNames.hasMoreElements()) {
            String element = initParameterNames.nextElement();
            // getInitParameter 获取到初始化参数值
            System.out.println(element + " = " + config.getInitParameter(element));
        }

        // 获取到null的参数值
        String initParameter = config.getInitParameter(null);
        System.out.println("initParameter = " + initParameter);

        // 这个对比servletcontext获取null参数名对应的参数值的情况
        // 此时会抛出NPE异常
        try {
            String initParameter1 = servletContext.getInitParameter(null);
            System.out.println("initParameter1 = " + initParameter1);
        } catch (Exception e) {
            System.out.println("e = " + e.getMessage());
        }


    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }
}

```

ServletConfigServlet2

```java
package com.inconspicuousy.servlet;

import javax.servlet.ServletConfig;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebInitParam;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Enumeration;

/**
 * ServletConfig 类方法演示
 *
 * @author inconspicuousy
 * @date 2020/10/27
 */
@WebServlet(value = "/servlet-config-two", initParams = @WebInitParam(name = "key", value = "value"))
public class ServletConfigServlet2 extends HttpServlet {

    @Override
    public void init(ServletConfig config) throws ServletException {
        // 打印config对象，判断不同的Servlet初始化是否对应不同的ServletConfig对象
        System.out.println("config = " + config);
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }
}
```

配置好Tomcat启动访问到对应的Servlet虚拟路径执行结果如下：

```shell
# 访问/servlet-config-one执行结果
config = org.apache.catalina.core.StandardWrapperFacade@2bd5d027
servletName = com.inconspicuousy.servlet.ServletConfigServlet1
servletContext = org.apache.catalina.core.ApplicationContextFacade@2fea4185
key = value
initParameter = null
e = null
# 访问/servlet-config-two执行结果
config = org.apache.catalina.core.StandardWrapperFacade@2eeefce4
```

> 从输出结果可以看出：
>
> 1、ServletConfig常与Servlet作绑定， 一个Servlet对应一个ServletConfig对象，该ServletConfig对象存储着Servlet配置信息。
>
> 2、servlet名称在未配置情况下默认为类的全路径名称。
>
> 3、ServletConfig获取null的参数值时返回的也是null。
>
> 4、ServletContext对象获取null对应的初始化参数时会抛出NPE空指针异常。

