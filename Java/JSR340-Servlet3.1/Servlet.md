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

## 4. Servlet的生命周期

### 4.1 图解生命周期

![image-20201023174212215](https://raw.githubusercontent.com/inconspicuousy-start/image/master/image-20201023174212215.png)

> 注意：
>
> 1、对于第三步和第四步，Servlet只会被创建一次并执行一次init方法，Servlet容器如果检查到内存中已经存在Servlet对象说明Servlet对象已经执行完init方法了，那么就会直接调用service方法处理请求。
>
> 2、Servlet不是在Web服务器启动的时候全部加载到内存中的， 是在Servlet容器解析请求时，根据请求路径解析到对应的Servlet，然后再去加载Servlet。

我们大致可以将Servlet的生命周期分为三个阶段：

- 初始化阶段

> Servlet容器解析请求找到处理请求的Servlet，一旦Servlet还没有被创建，那么此时就会触发初始化阶段。初始化阶段分为两步：
>
> 1、创建Servlet实例。
>
> 2、执行Servlet的初始化方法。
>
> 注意，初始化步骤只会执行一次，一旦被执行就不会再执行。

- 运行阶段

> Servlet容器解析请求找到处理请求的Servlet，一旦Servlet还没有已经被创建，那么就会直接调用Servlet的service方法处理请求，运行阶段只会涉及到service方法。
>
> 值得注意的是，每一次请求service必会被执行。

- 销毁阶段

> 当服务器关闭或者Web应用被移除容器时，Servlet会随着Web应用的关闭而销毁。在销毁Servlet之前，Servlet容器会调用Sevlet的destroy方法。
>
> 值得注意的是，destory方法只会在Servlet销毁时执行一次。

### 4.2 代码演示

1、自定义Servlet，并重写相关方法。

```java
package com.inconspicuousy.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * @author peng.yi
 */
public class LifecycleServlet extends HttpServlet {

    public LifecycleServlet() {
        System.out.println("我是Servlet构造器");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.print("每次请求的Servlet对象 => ");
        System.out.println(this);
        System.out.println("执行Service方法转发的doGet方法");
    }

    @Override
    public void destroy() {
        System.out.println("我是Servlet销毁的方法");
    }

    @Override
    public void init() throws ServletException {
        System.out.println("我是Servlet的初始化方法");
    }
}

```

2、配置好web.xml以及tomcat之后，启动tomcat，发现控制台并没有打印执行Servlet构造器方法的相关文字，说明Web服务器启动之后，Servlet容器并没有直接就创建Servlet对象。

3、进行第一次Get请求打印信息如下：

```
我是Servlet构造器
我是Servlet的初始化方法
每次请求的Servlet对象 => com.inconspicuousy.servlet.LifecycleServlet@249c2b2b
执行Service方法转发的doGet方法
```

说明第一次请求时，Servlet容器解析路径并没有发现Servlet对象，就会执行初始化阶段，创建Sevlet对象并执行初始化方式，待初始化阶段完成之后，执行运行阶段，执行service方法对请求做处理。

4、进行第二次Get请求打印信息如下：

```
每次请求的Servlet对象 => com.inconspicuousy.servlet.LifecycleServlet@249c2b2b
执行Service方法转发的doGet方法
```

说明在Servlet运行阶段，不会再执行初始化阶段了，并且每次处理请求的servlet都是同一个对象，说明对于web程序来说Servlet可以说是单例的，这里只执行了service方法。

5、关闭服务器之后打印信息如下：

```
我是Servlet销毁的方法
```

随着服务器的关闭，Servlet容器调用Servlet的销毁方法，关闭Servlet，Servlet生命周期结束。

## 5. Servlet处理用户请求的完整流程

### 5.1 图解Servlet处理用户请求流程

![image-20201027224517367](https://raw.githubusercontent.com/inconspicuousy-start/image/master/image-20201027224517367.png)

 在Servlet的每次请求， Web服务器在调用service（）方法之前，都会创建ServletRequest和ServletResponse对象。其中ServletRequest对象用于封装HTTP请求信息，简称request对象。ServletResponse对象用于封装HTTP响应信息，简称response对象。

> 注：在Web服务器运行阶段，每个Servlet都只会创建一个实例对象，针对每次HTTP请求，Web服务器都会调用Servlet实例的service方法，每次调用都会重新创建request和response对象。

