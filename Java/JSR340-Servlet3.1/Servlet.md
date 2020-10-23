# Servlet

## 1. 简介

> `Servlet` 是在Web服务器中运行的小型Java程序，Servlet通常通过HTTP（超文本传输协议）接收和响应来自Web客户端的请求。

在Java中定义了一个 `Servlet`接口，该接口定义了所有的Servlet必须实现的方法。

> 针对Servlet接口，Sun公司提供了两个默认的实现类: GenericServlet、HttpServlet。其中，GenericServlet是一个抽象类，该类为Servlet接口提供了部分实现，他并没有实现HTTP请求处理。
>
> 一般我们自定义的Servlet类直接继承HttpServlet类即可。

## 2. Servlet的使用

> 在自定义Servlet类之后，如何将自定义的Servlet类交给Servlet容器加载和管理呢？有如下的两种方式
>
> - 通过项目的描述文件web.xml配置自定义Servlet。
> - 通过注解的方式。

在配置之前先创建好对应的Servlet。

```java
package com.inconspicuousy.servlet;


import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Enumeration;

/**
 * 获取 Servlet 配置的Servlet
 * @author peng.yi
 */
public class InitParamServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取到该Servlet初始化参数列表
        Enumeration<String> initParameterNames = getInitParameterNames();
        while (initParameterNames.hasMoreElements()) {
            String key = initParameterNames.nextElement();
            // 根据初始化参数名称获取到参数值
            String value = getInitParameter(key);
            System.out.println(key + " = " + value);
        }
    }
}

```

### 2.1 通过web.xml配置Servlet

```xml
 <servlet>
        <servlet-name>initParam</servlet-name>
        <servlet-class>com.inconspicuousy.servlet.InitParamServlet</servlet-class>
        <!-- 定义initParamServlet独有的初始化参数 -->
        <init-param>
            <param-name>initParamKey</param-name>
            <param-value>initParamValue</param-value>
        </init-param>
    </servlet>
    <!-- 定义映射的虚拟路径 -->
    <servlet-mapping>
        <servlet-name>initParam</servlet-name>
        <url-pattern>/init-param</url-pattern>
    </servlet-mapping>
```

最后我们部署好Tomcat环境并用Get访问initParamServlet时，会打印出如下信息：

```
initParamKey = initParamValue
```

该信息就是我们在web.xml中InitServlet配置的初始化参数信息。

### 2.2 通过注解的方式配置Servlet

我们还是使用InitParamServlet类， 开始之前我们要将web.xml文件中关于InitParamServlet的配置都要去掉，然后给InitParamServlet加上如下的注解：

```java
@WebServlet(name = "initParam", urlPatterns = {"/init-param"}, initParams = {@WebInitParam(name = "initParamKey", value = "initParamValue")})
public class InitParamServlet extends HttpServlet {
```

同样部署好Tomcat环境之后并用Get访问指定的虚拟路径，会打印如下信息：

```
initParamKey = initParamValue
```

## 3. 方法列表

| 方法名称         | 方法签名                                                     | 方法描述                                                     |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| init             | public void init(ServletConfig config)                       | Servlet初始化方法                                            |
| getServletConfig | public ServletConfig getServletConfig()                      | 返回给Servlet做初始化参数的ServletConfig对象                 |
| service          | public void service(ServletRequest req, ServletResponse res) | 处理请求的核心方法，由Servlet容器调用，以允许Servlet响应请求。 |
| getServletInfo   | public String getServletInfo()                               | 返回有关servlet的信息，例如作者，版本和版权。                |
| destroy          | public void destroy()                                        | Servlet的销毁方法                                            |

### 3.1 init

> init方法为Servlet初始化方法， 该方法具有如下的特点：
>
> 1、init方法是在对象Servlet对象创建完之后被调用。
>
> 2、该方法只会被执行一次，并且在Servlet接收请求之前被调用。
>
> 3、当init方法发生如下两种情况时，Servlet容器无法使用Servlet服务：
>
> - 抛出一个ServletException异常。
> - 在Web服务器定义的时间段内不返回。
>
> 4、该方法将ServletConfig作为参数有如下的作用：
>
> - 在init方法中可以用来获取到ServletContext上下文对象，从而获取到应用的初始化参数。
> - ServletConfig对象可以用来获取到Servlet对应的初始化参数。

```java
	@Override
    public void init(ServletConfig config) throws ServletException {
        // 用于获取到ServletContext对象
        ServletContext servletContext = config.getServletContext();
        // 获取到ServletContext初始化参数
        Enumeration<String> initParameterNames = servletContext.getInitParameterNames();
        while (initParameterNames.hasMoreElements()) {
            String s =  initParameterNames.nextElement();
            System.out.println(s + " = " + servletContext.getInitParameter(s));
        }
        // 返回Servlet的初始化参数
        Enumeration<String> parameterNames = config.getInitParameterNames();
        while (parameterNames.hasMoreElements()) {
            String s = parameterNames.nextElement();
            System.out.println(s + " = " + config.getInitParameter(s));
        }
    }
```

### 3.2 service

> service方法由Servlet容器调用，以允许Servlet响应请求，该方法主要就是Servlet处理请求的核心方法。
>
> 特点：
>
> 1、该方法仅在init方法完成之后才会被调用。我们在init方法也说了， init方法执行如果出现问题，就会导致该Servlet不会被使用。
>
> 2、即使执行service方法抛出异常或者错误都应该为请求响应ServletResponse对象设置响应码。
>
> 3、Servlet通常在多线程Servlet容器中运行，该容器可以同时处理多个请求，我们在处理Servlet时必须要同步对任何共享资源（例如文件，网络连接以及Servlet的类和实例变量）的访问。
>
> 4、该方法的ServletRequest参数就是Servlet容器将请求信息封装后的对象， 可以从该参数中获取到请求的信息。
>
> 5、该方法的ServletResponse参数就是Servlet对请求作出响应后封装的对象，响应信息最终会被封装成ServletResponse对象返回给容器。

这里我们分析下HttpServlet是如何借助Servlet的service方法将请求转发到我们定义好的处理各种请求方法（doGet）的。

1、借助原生service方法，将原始ServletRequest、ServletResponse对象转化成HttpServletRequest、HttpServletResponse对象。

```java
@Override
    public void service(ServletRequest req, ServletResponse res)
        throws ServletException, IOException
    {
        HttpServletRequest  request;
        HttpServletResponse response;
        
        if (!(req instanceof HttpServletRequest &&
                res instanceof HttpServletResponse)) {
            throw new ServletException("non-HTTP request or response");
        }

        request = (HttpServletRequest) req;
        response = (HttpServletResponse) res;

        service(request, response);
    }
```

2、从HttpServletRequet获取到对应的请求方法，根据请求方法的名称将请求交给我们定义好的请求处理方法。

```java
protected void service(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
        String method = req.getMethod();

        if (method.equals(METHOD_GET)) {
            long lastModified = getLastModified(req);
            if (lastModified == -1) {
                // 调用doGet方法处理请求
                doGet(req, resp);
            }
            ...
        } 
    }
```

> 需要我们注意的是，如果我们自定义Servlet类是继承HttpServlet类，那么我们直接重写doXXX某个方法即可，不用重写service方法，因为HttpServlet已经将service方法逻辑写好了，会将请求转发到我们写的方法中。

### 3.3 destroy

> destroy方法由Servlet容器调用，以向Servlet指示该Servlet正在退出服务。
>
> 特点：
>
> 1、仅当Servlet的service方法中所有的线程都已经退出或经过超时时间后，采调用次方法。
>
> 2、servlet容器调用此方法后，将不再在该Servlet上再次调用service方法。
>
> 3、此方法使Servlet有机会清理所持有的任何资源（例如，内存，文件句柄，线程），并确保任何持久状态都与Servlet在内存中的当前状态同步。

在我们平时使用过程中，常常都是关闭Web服务器时就会触发Servlet的destory方法，我们可以在destory中作一些关闭资源的操作，比如我们在Servlet的init方法中初始化了线程池，就可以在destory中关闭这个线程池。