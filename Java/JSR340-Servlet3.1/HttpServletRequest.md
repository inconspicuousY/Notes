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

#### 2.2.2 Session + Cookie

> 默认服务器生成的Session信息是由Cookie来维护的，当生成Session对象时，服务器会设置一个名称固定为JSESSIONID，值为Session唯一的识别码的Cookie作为响应头返回给客户端，客户端会将该Cookie信息保存下来，默认该Cookie的作用域对象为Session，即当前浏览器会话，当浏览器会话关闭时，该Cookie自动失效。
>
> 服务端拿到客户端请求的Cookie信息，发现存在JSESSIONID的Cookie时，拿到Session对应的唯一识别码从内存中查找到对应的Session对象，就可以获取到该Session设置的相关属性，比如保存在的某些属性和属性值。

| 方法名                | 方法签名                                      | 方法描述                                                     |
| --------------------- | --------------------------------------------- | ------------------------------------------------------------ |
| getCookies            | public Cookie[] getCookies()                  | 以Cookie数组的形式返回请求头中Cookie信息。如果请求头没有Cookie的信息则返回null。 |
| getSession            | public HttpSession getSession(boolean create) | 返回与此请求关联的当前的HttpSession对象。如果没有当前会话且create为true；则返回一个新会话。如果create为false且请求没有有效的HttpSession，则此方法返回null。如果容器使用cookie来维护会话的完整性，并且提交响应时被要求创建新的会话，则抛出IllgalStateException异常。 |
| getSession            | public HttpSession getSession()               | 返回与此请求相关联的当前的HttpSession对象，如果请求没有一个当前会话对象则创建一个。 |
| getRequestedSessionId | public String getRequestedSessionId()         | 返回Session的唯一识别码。如果没有SessionId，直接返回null。   |

这里我们再次模拟GET请求来验证Session和Cookie的信息。

- Servlet代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;
import java.io.IOException;
import java.util.Arrays;
import java.util.Enumeration;
import java.util.Objects;

/**
 * @author peng.yi
 */
@WebServlet(value = "/cookie-session-servlet")
public class CookieSessionServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        System.out.println("==========开始处理Session和Cookie==========");
        // 从请求头Cookie中获取到所有的Cookie对象，如果不存在Cookie头不存在则返回null
        Cookie[] cookies = req.getCookies();
        System.out.println("cookies = " + Arrays.toString(cookies));
        if (Objects.nonNull(cookies)) {
            Arrays.stream(cookies).forEach(cookie -> {
                String name = cookie.getName();
                System.out.println("name = " + name);
                String value = cookie.getValue();
                System.out.println("value = " + value);
                // 获取到Cookie的最大生存时间，默认是当浏览器关闭Cookie销毁时返回-1
                int maxAge = cookie.getMaxAge();
                System.out.println("maxAge = " + maxAge);
            });
        }

        // 获取到当前的Session对象， 如果不存在创建Session对象
        // 创建的Session对象会随着响应信息作为Cookie返回给客户端
        HttpSession session = req.getSession();
        System.out.println("session = " + session);
        // Session对象以键值对的形式维护了多个属性和属性值。
        Enumeration<String> attributeNames = session.getAttributeNames();
        while (attributeNames.hasMoreElements()) {
            String sessionName = attributeNames.nextElement();
            System.out.println("sessionName = " + sessionName);
            Object sessionValue = session.getAttribute(sessionName);
            System.out.println("sessionValue = " + sessionValue);
        }
        // 设置一组Session的属性和属性值
        session.setAttribute("SESSION_ID", "SESSION_VALUE");
        
         // 获取到Session的唯一识别码
        String requestedSessionId = req.getRequestedSessionId();
        System.out.println("requestedSessionId = " + requestedSessionId);

    }
}
```

- 清除浏览器中 `http://172.16.2.148:8080` 的Cookie信息。
- 请求路径

```http
http://localhost:8080/servlet_annotation/cookie-session-servlet
```

- 第一次请求控制台打印信息

```shell
cookies = null
session = org.apache.catalina.session.StandardSessionFacade@1ec3de03
requestedSessionId = null
```

- 查看HTTP响应的Cookie信息

```http
Set-Cookie: JSESSIONID=50B8BE77E4F71891B187743987283289; Path=/servlet_annotation; HttpOnly
```

Cookie的值默认是JESSIONID，Cookie的值为当前Session对象的唯一识别码，客户端会将该Cookie保存起来，再次请求时会将该Cookie信息在请求头中传递给服务器。

- 第二次请求控制台打印信息

```shell
==========开始处理Session和Cookie==========
cookies = [javax.servlet.http.Cookie@249c2b2b]
name = JSESSIONID
value = 7B3CD55C67576B074A5E1027458050AD
maxAge = -1
session = org.apache.catalina.session.StandardSessionFacade@1ec3de03
sessionName = SESSION_ID
sessionValue = SESSION_VALUE
requestedSessionId = 7B3CD55C67576B074A5E1027458050AD

```

从打印信息来看就验证了Session和Cookie的相关关系。

#### 2.2.3 认证相关

| 方法名           | 方法签名                                                  | 方法描述                                                     |
| ---------------- | --------------------------------------------------------- | ------------------------------------------------------------ |
| getAuthType      | public String getAuthType()                               | 返回用于保护Servlet的身份验证方案的名称。所有的Servlet容器都支持BASIC_AUTH（基础认证）、FORM_AUTH（表单认证）、CLIENT_CERT_AUTH（客户端身份认证），并可能还支持DIGEST_AUTHENTICATION（摘要认证）。如果servlet未经过身份验证则返回null。 |
| isUserInRole     | public boolean isUserInRole(String role)                  | 返回一个Boolean，指明经过身份验证的用户是否包含指定的逻辑角色中。可以使用部署描述符（web.xml）定义角色和角色成员身份。如果用户尚未经过身份验证，则该方法将返回false。 |
| getUserPrincipal | public java.security.Principal getUserPrincipal()         | 返回一个Principal对象，其中包含当前经过身份验证的用户的名称。如果用户尚未通过身份验证，则该方法返回null。 |
| authenticate     | public boolean authenticate(HttpServletResponse response) | 使用为ServletContext配置的容器登录机制来验证发出此请求的用户。如果getUserPrincipal、getRemoteUser和getAuthType都是非空的话则返回true。如果身份验证不完整并且底层登录机制已在响应中提交了要返回给用户的消息和HTTP状态代码，则返回false。此方法可以修改此请求的响应参数。 |
| getRemoteUser    | public String getRemoteUser()                             | 如果用户已通过身份验证，则返回发出此请求的用户的登录名；如果用户未通过身份验证，如果用户未通过身份验证，则返回null。用户名是否随每个后续请求一起发送，取决于浏览器和身份验证类型。 |
| login            | public void login(String username, String password)       | 在为ServletContext配置的Web容器登录机制所使用的密码验证领域中，验证提供的用户名和密码。如果配置的登录机制不支持用户名密码身份验证，或者在登录之前已经建立了非空调用者身份，或者对提供的用户名和密码的验证失败时抛出ServletExption。当此方法返回而没有引发异常时，他必须已建立非空值，作为getUserPrincipal、getRemoteUser、getAuthType返回的值。 |
| logout           | public void logout()                                      | 退出登录 ，调用getUserPrincipal、getRemoteUser、getAuthType时返回null。 |

这里我们再次模拟GET请求来验证认证和授权的信息。

- 在 `web.xml` 中定义Servlet容器验证用户身份的类型。

```xml
 	<!-- 用于配置应使用的身份验证方法 -->
    <!-- 此应用程序应使用的realm-name, 以及表单登录机制所需的属性 -->
    <login-config>
         <!--用于为Web应用程序配置身份验证机制 -->
         <!--作为访问授权约束保护的任何Web资源的先决条件，用户必须已使用配置的机制进行了身份验证 -->
         <!--合法值为“BASIC”，“DIGEST”，“FORM”，“CLIENT-CERT”或特定于供应商的身份验证方案 -->
        <auth-method>BASIC</auth-method>
    </login-config>
```

- 因为是由 Servlet容器对用户的请求进行身份验证，所以一般来说是在Servlet容器中定义用户信息，这里我们采用Tomcat的话，那么就在Tomcat的安装目录的 `conf/tomcat-users.xml` 文件中定义用户信息。

```xml
<!-- 定义用户名称和密码以及角色信息 -->
<user username="admin" password="admin" roles="admin"/>
<user username="test" password="test" roles="test"/>
```

- Servlet代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.security.Principal;
import java.util.Objects;

/**
 * @author peng.yi
 */
@WebServlet(value = "/auth")
public class AuthServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("=======开始处理认证相关信息=======");

        try {
            // 登录，验证提供的用户名和密码
            // 由Web容器提供的登录机制验证用户名和密码
            req.login("admin", "admin");
        } catch (ServletException e) {
            System.out.println("登录失败 | errMsg => " + e.getMessage());
        }

        // 获取登录成功的用户的用户名
        String remoteUser = req.getRemoteUser();
        System.out.println("remoteUser = " + remoteUser);

        // 返回当前Servlet容器认证的方式
        String authType = req.getAuthType();
        System.out.println("authType = " + authType);

        // 返回当前认证成功的用户对应的Principal对象
        Principal userPrincipal = req.getUserPrincipal();
        if (Objects.nonNull(userPrincipal)) {
            System.out.println("userPrincipal = " + userPrincipal.getName());
        } else {
            System.out.println("userPrincipal = null");
        }

        // 判断用户是否包含某个角色
        boolean admin = req.isUserInRole("admin");
        System.out.println("admin = " + admin);

        // 判断用户的认证状态
        boolean authenticate = req.authenticate(resp);
        System.out.println("authenticate = " + authenticate);

        // 退出登录
        req.logout();
    }
}
```

- 请求路径

```http
http://localhost:8080/servlet_annotation/auth
```

- 控制台打印信息

```shell
=======开始处理认证相关信息=======
remoteUser = admin
authType = BASIC
userPrincipal = admin
admin = true
authenticate = true
```

- 将登录信息切换成test进行登录

```java
 req.login("test", "test");
```

- 控制台打印信息

```shell
=======开始处理认证相关信息=======
remoteUser = test
authType = BASIC
userPrincipal = test
admin = false
authenticate = true
```

#### 2.2.4 其他请求头获取方法

> 注意：请求头名称都不区分大小写。

| 方法名               | 方法签名                                                 | 方法描述                                                     |
| -------------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| getHeader            | public String getHeader(String name)                     | 返回指定请求头对应的请求头值。如果该请求头不存在，则返回null；如果有多个同名的请求头，则返回请求头中第一次出现的请求头对应的值。请求头名称不区分大小写。 |
| getHeaders           | public Enumeration&lt;String&gt; getHeaders(String name) | 以枚举的方法返回指定请求头对应的所有的请求头值。如果请求不包含该请求头，则返回一个空的枚举类；如果Servlet容器没有权限访问到该请求头信息，那么直接返回null。 |
| getHeaderNames       | public Enumeration&lt;String&gt;getHeaderNames()         | 返回一个包含所有请求头名称的枚举类。如果请求没有请求头部信息则返回一个空的枚举类；如果Servlet容器没有权限访问到该方法，则返回null。 |
| getIntHeader         | public int getIntHeader(String name)                     | 以整数的形式返回指定请求头对应的请求头值。如果请求头不存在，则返回-1；如果请求头值不能转化成整数则抛出NumberFormatException异常。 |
| getDateHeader        | public long getDateHeader(String name)                   | 以Date形式返回指定头对应的请求头值，最后会将Date的值转化成对应的时间戳。如果请求头不存在返回-1；如果请求头值不能转化成Date形式则抛出IllegalArgumentException异常。 |
| getContentType       | public String getContentType()                           | 返回请求体的MIME类型。如果类型未知未知则返回null。           |
| getContentLength     | public int getContentLength()                            | 以字节为单位，返回请求主体的长度，该长度可由输入流提供。如果长度未知或者大于Integer.MAX_VALUE的值则返回-1。 |
| getCharacterEncoding | public String getCharacterEncoding()                     | 返回请求体的编码方式。如果没有指定请求编码方式则返回null。设置请求编码方式的优先级（从高到低）：每个请求 &gt; 每个Web应用程序（通过ServletContext.setRequestCharacterEncoding设置）&gt; 每个容器（对于该容器中部署的所有的Web应用程序）。 |
| getParts             | public Collection&lt;Part&gt; getParts()                 | 返回请求中Part集合。如果form表单提交的方式为 multipart/form-data，那么每一个组件就是一个part，最后返回的就是所有的组件组成的Part集合。如果请求类型不是multipart/form-data，那么就会抛出ServletException异常。如果web.xml没有配置multipart-config标签，也会抛出IllegalStateException，通过 multipart-config 标签可以配置maxFileSize和maxRequestSize属性，一旦超过也会抛出IllegalStateException异常。也可通过@MultipartConfig注解配置。 |
| getPart              | public Part getPart(String name)                         | 根据名称返回对应的Part。                                     |

我们通过Postman模拟form表单提交来执行相关方法查看结果。

- Servlet代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.MultipartConfig;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.Part;
import java.io.IOException;
import java.io.InputStream;
import java.util.Collection;
import java.util.Enumeration;

/**
 * @author peng.yi
 */
@WebServlet(value = "/header")
// 通过MultipartConfig 来配置multipart/form-data的属性， 如果不配置处理Part会抛出异常
@MultipartConfig(maxFileSize = Integer.MAX_VALUE, maxRequestSize = Integer.MAX_VALUE, fileSizeThreshold = Integer.MAX_VALUE)
public class HeaderServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("====开始处理普通请求头信息====");

        // 获取请求的MIME类型
        String contentType = req.getContentType();
        System.out.println("contentType = " + contentType);

        // 获取到类型的字节长度
        int contentLength = req.getContentLength();
        System.out.println("contentLength = " + contentLength);

        // 获取请求的编码方式
        String characterEncoding = req.getCharacterEncoding();
        System.out.println("characterEncoding = " + characterEncoding);

        // 获取到所有的请求头名称
        Enumeration<String> headerNames = req.getHeaderNames();
        while (headerNames.hasMoreElements()) {
            String headerName = headerNames.nextElement();
            System.out.println("headerName = " + headerName);
            String header = req.getHeader(headerName);
            // 获取到请求头对应的请求头值
            System.out.println("header = " + header);
        }

        Collection<Part> parts = req.getParts();
        parts.forEach(part -> {
            // 得到part的名称
            String name = part.getName();
            System.out.println("part name = " + name);
            // 得到part的MIME类型
            String partContentType = part.getContentType();
            System.out.println("partContentType = " + partContentType);
            // 得到Part的输入流，根据输出入流就可以获取到数据
            try (InputStream is = part.getInputStream()) {
                System.out.println("is = " + is);
            }catch (Exception e) {
                e.printStackTrace();
            }
        });
    }
}
```

- postman请求信息

```http
POST /servlet_annotation/header HTTP/1.1
Host: localhost:8080
Content-Length: 356
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="name"

Tom
----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="sex"

1
----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="img"; filename="/E:/Images/红色郁金香.jpg"
Content-Type: image/jpeg

(data)
----WebKitFormBoundary7MA4YWxkTrZu0gW

```

这里模拟传入三个Part，两个字符串一个图片信息。

- 控制台打印信息

```
====开始处理普通请求头信息====
contentType = multipart/form-data; boundary=--------------------------630221069390582297078331
contentLength = 1385472
characterEncoding = null

headerName = postman-token
header = 634ad56b-9ff1-4069-a6ae-f96075625c56
headerName = host
header = localhost:8080
headerName = accept-encoding
header = gzip, deflate, br
headerName = connection
header = keep-alive
headerName = content-type
header = multipart/form-data; boundary=--------------------------630221069390582297078331
headerName = content-length
header = 1385472

part name = name
partContentType = null
is = java.io.ByteArrayInputStream@7172f4bc
part name = sex
partContentType = null
is = java.io.ByteArrayInputStream@6fba8740
part name = img
partContentType = image/jpeg
is = java.io.ByteArrayInputStream@4852bcfa

```

### 2.3 请求体

| 方法名称           | 方法签名                                                     | 描述                                          | 注意                                                         |
| ------------------ | ------------------------------------------------------------ | --------------------------------------------- | ------------------------------------------------------------ |
| getParameter       | public String getParameter(String name)                      | 以字符串的形式返回指定请求参数的值            | 1、参数不存在返回null。<br>2、参数包含查询字字符串和POST发送的表单数据。<br>3、如果参数对应多个值，请使用getParameterValues。<br>4、如果参数对应多个值，则返回的值等于getParameterValues返回的数组中的第一个值。<br>5、如果参数数据是在请求体中，直接通过getInputStream或getReader读取请求体会干扰次方法的执行。<br> |
| getParameterNames  | public Enumeration&lt;String&gt; getParameterNames()         | 以枚举的形式返回请求中包含的参数集合          | 1、如果请求没有参数，则该方法返回一个空的枚举对象。          |
| getParameterValues | public String[] getParameterValues(String name)              | 以字符串数组的形式返回指定请求参数的值列表    | 1、该方法针对的就是一个请求参数对应多个值得情况。<br>2、如果参数不存在则返回null。 |
| getParameterMap    | public Map&lt;String, String[]&gt; getParameterMap()         | 以Map的形式返回请求的参数名和对应的参数值列表 | 1、返回的Map是不可变的。<br>                                 |
| getInputStream     | public ServletInputStream getInputStream() throws IOException | 以二进制流的形式返回请求体                    | 1、不能同时调用此方法和getReader方法读取请求体，否则会抛出IllegalStateException。<br>2、如果发生输入或输出异常，会抛出IOException。<br/>3、不能获取到POST、form-data数据，form-data数据推荐用getParameter方法。 |
| getReader          | public BufferedReader getReader()                            | 以字符流的形式返回请求体                      | 1、不能同时调用此方法和getInputStream方法读取请求体，否则会抛出IllegalStateException。<br/>2、如果发生输入或输出异常，会抛出IOException。<br/>3、Reader对象会根据请求体的编码方式来解析请求体，如果不支持所使用的字符集编码并且文本无法解码，会抛出UnsupportedEncodingException异常。<br/>4、不能获取到POST、form-data数据，form-data数据推荐用getParameter方法。 |

采用POST请求来演示该部分方法的执行。

- Servlet代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.BufferedReader;
import java.io.IOException;
import java.util.Arrays;
import java.util.Enumeration;
import java.util.Map;

/**
 * @author peng.yi
 */
@WebServlet(value = "/body")
public class BodyServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("======开始处理请求体信息======");

        System.out.println("==========调用getReader之前");
        // 以Map的形式获取到所有的参数名和参数值
        System.out.println("=====map======");
        Map<String, String[]> parameterMap = req.getParameterMap();
        parameterMap.forEach((key, value) -> {
            System.out.println("key = " + key);
            System.out.println("value = " + Arrays.toString(value));
        });

        // 读取到所有的正文信息
        BufferedReader reader = req.getReader();
        StringBuilder stringBuilder = new StringBuilder();
        reader.lines().forEach(stringBuilder::append);
        System.out.println("==========调用getReader");
        System.out.println("stringBuilder.toString() = " + stringBuilder.toString());

        System.out.println("==========调用getReader之后");

        // 获取到所有的参数名称列表
        System.out.println("======getParameterNames======");
        Enumeration<String> parameterNames = req.getParameterNames();
        while (parameterNames.hasMoreElements()) {
            String key = parameterNames.nextElement();
            System.out.println("key = " + key);
            // 获取到参数名对应的参数值
            String parameterValues = req.getParameter(key);
            System.out.println("parameterValues = " + parameterValues);
        }

    }
}
```

- 利用postman模拟post表单请求。

```http
POST /servlet_annotation/body?a=1&b=2 HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded
Content-Length: 8

a=11&c=3
```

- 控制台输出结果

```shell
======开始处理请求体信息======
==========调用getReader之前
=====map======
key = a
value = [1, 11]
key = b
value = [2]
key = c
value = [3]
==========调用getReader
stringBuilder.toString() = 
==========调用getReader之后
======getParameterNames======
key = a
parameterValues = 1
key = b
parameterValues = 2
key = c
parameterValues = 3
```

- 利用postman模拟POST、application/json请求

```http
POST /servlet_annotation/body?a=1&b=2 HTTP/1.1
Host: localhost:8080
Content-Type: application/json
Content-Length: 30

{
    "a": 11,
    "c": 3
}
```

- 控制台输出结果

```shell
======开始处理请求体信息======
==========调用getReader之前
=====map======
key = a
value = [1]
key = b
value = [2]
==========调用getReader
stringBuilder.toString() = {    "a": 11,    "c": 3}
==========调用getReader之后
======getParameterNames======
key = a
parameterValues = 1
key = b
parameterValues = 2

```

