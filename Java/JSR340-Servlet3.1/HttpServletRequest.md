# HttpServletRequest

## 1. 简介

> `javax.servlet.http.HttpServletRequest`是Servlet处理HTTP请求的http包下的一个接口。该接口用于为处理HTTP请求的Servlet提供请求信息。
>
> Servlet容器在接收到HTTP请求时，会创建HttpServletRequest对象，并将该对象作为参数传递给Servlet的service方法。

下面就是 `HttpServlet`的service方法。

```java
@Override
    public void service(ServletRequest req, ServletResponse res)
        throws ServletException, IOException
    {
        HttpServletRequest  request;
        HttpServletResponse response;
        
        // 这里传过来的ServletRequest本质上就是HttpServletRequest
        // 该对象封装了Http请求信息，可以利用该对象获取到请求的信息
        if (!(req instanceof HttpServletRequest &&
                res instanceof HttpServletResponse)) {
            throw new ServletException("non-HTTP request or response");
        }

        request = (HttpServletRequest) req;
        response = (HttpServletResponse) res;

        service(request, response);
    }
```

## 2. 方法列表

基于HTTP请求消息的特点，HTTP请求消息分为请求行、请求消息头、请求消息体三部分。

下面就是利用POST访问某个接口的请求信息：

```http
POST /v1/api/common/auth/check_login HTTP/1.1
Host: http://172.16.12.126:6000
Content-Type: application/json

{
    "tenantId": "Test_01",
	"token": "1596845288000e42e2910260f24519153386e9260666b"
}
```

> 消息特点：
>
> 1、消息的首行就是消息的请求行，描述的是请求方法、排除域名和端口号后的资源路径、请求协议版本号。
>
> 2、第二行开始就是请求头，以key-value的形式存储请求头部信息。
>
> 3、请求头和请求体之间会空一行，表示下面的信息为请求体，这里请求体的信息是一个JSON串。

### 2.1 请求行

| 方法名         | 方法签名                            | 方法描述                                                     |
| -------------- | ----------------------------------- | ------------------------------------------------------------ |
| getMethod      | public String getMethod()           | 返回请求的方法名称，例如GET、POST、PUT等                     |
| getRequestURI  | public String getRequestURI()       | 返回不包含查询参数的资源路径                                 |
| getQueryString | public String getQueryString()      | 返回资源路径中的查询参数，如果没有查询参数，返回null         |
| getContextPath | public String getContextPath()      | 返回Web上下文根路径。该字符串以"/"字符开头，但是不以"/"结尾，如果上下文根路径本身就是"/"，那么该方法返回""空字符串。 |
| getServletPath | public String getServletPath()      | 返回此请求的URL中调用Servlet的名称或者路径，如果用于处理请求的Servlet使用"/*"模式匹配，则返回空字符串。 |
| getProtocol    | public String getProtocol()         | 返回请求的协议和版本号                                       |
| getRequestURL  | public StringBuffer getRequestURL() | 返回不包含查询参数的完整的请求URL，包含协议、服务器名称、端口号、服务器路径，但是不包含查询参数。 |

下面模拟一个GET请求，调用相关方法查看结果。

- Servlet代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * 获取请求行信息
 *
 * @author peng.yi
 */
@WebServlet(value = "/request-line")
public class RequestLineServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("=========利用HttpServletRequest对象获取到请求行信息=======");
        // 获取请求的方法
        String method = req.getMethod();
        System.out.println("method = " + method);

        // 获取请求排除查询参数后的资源路径
        String requestURI = req.getRequestURI();
        System.out.println("requestURI = " + requestURI);

        // 获取到查询参数
        String queryString = req.getQueryString();
        System.out.println("queryString = " + queryString);

        // 获取到上下文路径
        String contextPath = req.getContextPath();
        System.out.println("contextPath = " + contextPath);

        // 获取servlet名称或者路径
        String servletPath = req.getServletPath();
        System.out.println("servletPath = " + servletPath);
        
        // 获取请求的协议和版本号
        String protocol = req.getProtocol();
        System.out.println("protocol = " + protocol);
        
        // 获取不包含查询参数的完整的请求URL
        StringBuffer requestURL = req.getRequestURL();
        System.out.println("requestURL = " + requestURL.toString());
    }
}

```

- 请求的路径（当前上下文路径在部署tomcat时配置的`/servlet_annotation`）

```http
http://localhost:8080/servlet_annotation/request-line?key=value
```

- 输出结果

```shell
=========利用HttpServletRequest对象获取到请求行信息=======
method = GET
requestURI = /servlet_annotation/request-line
queryString = key=value
contextPath = /servlet_annotation
servletPath = /request-line
protocol = HTTP/1.1
requestURL = http://localhost:8080/servlet_annotation/request-line
```



### 2.2 请求头

#### 2.2.1 获取主机名+端口号

| 方法名        | 方法签名                      | 方法描述                                                     |
| ------------- | ----------------------------- | ------------------------------------------------------------ |
| getRemoteAddr | public String getRemoteAddr() | 返回发送请求的客户端的IP地址。                               |
| getRemoteHost | public String getRemoteHost() | 返回发送请求客户端的完整的主机名（域名）。如果主机名不存在则返回客户端的IP地址。 |
| getRemotePort | public int getRemotePort()    | 返回发送请求客户端的端口号。                                 |
| getLocalAddr  | public String getLocalAddr()  | 返回接收请求的服务端的IP地址                                 |
| getLocalName  | public String getLocalName()  | 返回接收请求的服务端的主机名                                 |
| getLocalPort  | public int getLocalPort()     | 返回接收请求服务端的端口号                                   |
| getServerName | public String getServerName() | 返回当前请求所指向的主机名，即请求头中Host字段所对应的主机名部分 |
| getServerPort | public int getServerPort()    | 返回当前请求所指向的端口号，即请求头中Host字段所对应的端口号部分 |

> 注意：
>
> 1、getServerName是Host请求头中的主机名，不会对主机做解析；getLocalName是接收请求的主机名，是真正接收到请求的服务器主机名（可以在终端用hostname命令查看接收服务器的主机名）。

再次模拟GET请求，调用相关方法查看结果。

- Servlet代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * 解析请求头中 关于请求客户端和服务端的主机名和端口号
 * @author peng.yi
 */
@WebServlet(value = "/request-host")
public class RequestHostServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("========获取请求头中关于主机名和端口号的信息=======");

        // 客户端主机名
        String remoteHost = req.getRemoteHost();
        System.out.println("remoteHost = " + remoteHost);

        // 客户端IP地址
        String remoteAddr = req.getRemoteAddr();
        System.out.println("remoteAddr = " + remoteAddr);

        // 客户端端口号
        int remotePort = req.getRemotePort();
        System.out.println("remotePort = " + remotePort);

        // 服务端的IP地址
        String localAddr = req.getLocalAddr();
        System.out.println("localAddr = " + localAddr);

        // 服务端的主机名
        String localName = req.getLocalName();
        System.out.println("localName = " + localName);

        // 服务端的端口号
        int localPort = req.getLocalPort();
        System.out.println("localPort = " + localPort);

        // Host请求头中主机名
        String serverName = req.getServerName();
        System.out.println("serverName = " + serverName);

        // Host请求中端口号
        int serverPort = req.getServerPort();
        System.out.println("serverPort = " + serverPort);

    }
}
```

- 请求的路径

```http
http://localhost:8080/servlet_annotation/request-host
```

- 输出结果

```
========获取请求头中关于主机名和端口号的信息=======
remoteHost = 0:0:0:0:0:0:0:1
remoteAddr = 0:0:0:0:0:0:0:1
remotePort = 63174
localAddr = 0:0:0:0:0:0:0:1
localName = 0:0:0:0:0:0:0:1
localPort = 8080
serverName = localhost
serverPort = 8080
```

- 在虚拟机中请求本机服务

```http
http://172.16.2.148:8080/servlet_annotation/request-host
```

- 输出结果

```
========获取请求头中关于主机名和端口号的信息=======
remoteHost = 172.16.2.148
remoteAddr = 172.16.2.148
remotePort = 63333
localAddr = 172.16.2.148
localName = DESKTOP-TUNMANJ
localPort = 8080
serverName = 172.16.2.148
serverPort = 8080
```

#### 2.2.2 其他请求头获取方法

> 注意：请求头名称都不区分大小写。

| 方法名               | 方法签名                                                 | 方法描述                                                     |
| -------------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| getHeader            | public String getHeader(String name)                     | 返回指定请求头对应的请求头值。如果该请求头不存在，则返回null；如果有多个同名的请求头，则返回请求头中第一次出现的请求头对应的值。请求头名称不区分大小写。 |
| getHeaders           | public Enumeration&lt;String&gt; getHeaders(String name) | 以枚举的方法返回指定请求头对应的所有的请求头值。如果请求不包含该请求头，则返回一个空的枚举类；如果Servlet容器没有权限访问到该请求头信息，那么直接返回null。 |
| getHeaderNames       | public Enumeration&lt;String&gt;getHeaderNames()         | 返回一个包含所有请求头名称的枚举类。如果请求没有请求头部信息则返回一个空的枚举类；如果Servlet容器没有权限访问到该方法，则返回null。 |
| getIntHeader         | public int getIntHeader(String name)                     | 以整数的形式返回指定请求头对应的请求头值。如果请求头不存在，则返回-1，如果请求头值不能转化成整数则抛出NumberFormatException异常。 |
| getDateHeader        | public long getDateHeader(String name)                   |                                                              |
| getContentType       |                                                          |                                                              |
| getContentLength     |                                                          |                                                              |
| getCharacterEncoding |                                                          |                                                              |

