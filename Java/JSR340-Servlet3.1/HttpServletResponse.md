# HttpServletResponse

## 1. 简介

> HttpServletResponse接口继承于ServletResponse接口，主要用于封装HTTP的响应信息，通过Servlet的service方法将响应消息发送给客户端。

## 2. 相关方法

由于 HTTP 相应消息分为状态行、响应消息头、消息体三部分。因此在 HttpServletResponse 接口中定义了向客户端发送响应状态码、响应消息头、响应消息体的方法。

### 2.1 发送状态码相关的方法

| 方法名称  | 方法签名                                                     | 描述                                                     | 注意                                                         |
| --------- | ------------------------------------------------------------ | -------------------------------------------------------- | ------------------------------------------------------------ |
| sendError | public void sendError(int code) throws java.io.IOException   | 该方法用于设置 HTTP 响应消息的状态码，并生成响应状态行。 | 1、在正常情况下，Web 服务器会默认产生一个状态码为 200 的状态行。 |
| sendError | public void sendError(int code,String message)throws java.io.IOException | 该方法用于发送表示错误信息的状态码。                     | 1、方法除了发送状态码以外，还可以增加一条用于提示说明的文本信息，该文本信息将出现在发送给客户端的正文内容中。<br/>2、该方法已经过时，不再推荐使用。 |

### 2.2 发送响应消息头相关的方法

| 方法名称             | 方法签名                                         | 描述                                                         | 注意                                                         |
| -------------------- | ------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| addHeader            | public void addHeader(String name, String value) | 设置 HTTP 协议的响应头字段                                   | 方法可以增加同名的响应头字段                                 |
| setHeader            | public void setHeader(String name, String value) | 设置 HTTP 协议的响应头字段                                   | 方法会覆盖同名的头字段                                       |
| setContentType       | public void setContentType(String type)          | 设置 Servlet 输出内容的 MIME 类型，对于 HTTP 协议来说，就是设置 Content-Type 响应头字段的值 | 1、如果发送到客户端的内容是 jpeg 格式的图像数据,就需要将响应头字段的类型设置为 image/jpeg。<br/>2、如果响应的内容为文本，setContentType() 方法还可以设置字符编码，如 text/html;charset = UTF-8 |
| setLocale            | public void setLocale(Locale loc)                | 设置响应消息的本地化信息，对 HTTP 来说，就是设置 Content-Language 响应头字段和 Content-Type 头字段中的字符集编码部分。 | 1、如果 HTTP 消息没有设置 Content-Type 头字段，则 setLocale() 方法设置的字符集编码不会出现在 HTTP 消息的响应头中。<br/>2、如果调用 setCharacterEncoding() 或 setContentType() 方法指定了响应内 容的字符集编码，则 setLocale() 方法将不再具有指定字符集编码的功能。 |
| setCharacterEncoding | public void setCharacterEncoding(String charset) | 设置输出内容使用的字符编码，对 HTTP 协议来说，就是设置 Content-Type 头字段中的字符集编码部分。 | 1、如果没有设置 Content-Type 头字段，则 setCharacterEncoding 方法设 置的字符集编码不会出现在 HTTP 消息的响应头中。<br/>2、setCharacterEncoding() 方法比 setContentType() 和 setLocale() 方法的优先权高，它的设置结果将覆盖 setContentType() 和 setLocale() 方法所设置的字符码表。 |

> 注意：
>
> 1、addHeader()、setHeader()方法都用于设置各种头字段。
>
> 2、setContetType()、setLoacale() 和 setCharacterEncoding() 方法用于设置字符编码，这些设置字符编码的方法可以有效解决乱码问题。

### 2.3 发送响应消息体相关的方法

由于在 HTTP 响应消息中，大量的数据都是通过响应消息体传递的，因此，ServletResponse 遵循以 I/O 流传递大量数据的设计理念。在发送响应消息体时，定义了两个与输出流相关的方法。

| 方法名称        | 方法签名                                                     | 描述                                            | 注意                                                         |
| --------------- | ------------------------------------------------------------ | ----------------------------------------------- | ------------------------------------------------------------ |
| getOutputStream | public ServletOutputStream getOutputStream() throws IOException | 获取的字节输出流对象为 ServletOutputStream 类型 | 由于 ServletOutputStream是OutputStream 的子类，它可以直接输出字节数组中的二进制数据。因此，要想输出二进制格式的响应正文，就需要使用 getOutputStream() 方法。 |
| getWriter       | public PrintWriter getWriter() throws IOException            | 获取的字符输出流对象为 PrintWriter 类型         | 由于 PrintWriter 类型的对象可以直接输出字符文本内容，因此，要想输出内容全部为字符文本的网页文档，则需要使用 getWriter() 方法。 |

> 注意：
>
> 虽然 response 对象的 getOutputStream() 和 getWriter() 方法都可以发送响应消息体，但是，它们之间互相排斥，不可同时使用，否则会发生 IllegalStateException 异常。

### 2.4 特殊方法之实现页面的重定向

> 请求重定向： 指 Web 服务器接收到客户端的请求后，可能由于某些条件的限制，不能访问当前请求 URL 所指向的 Web 资源，而是指定了一个新的资源路径，让客户端重新发送请求。

#### 2.4.1 请求重定向图解

![image-20201104115036423](https://raw.githubusercontent.com/inconspicuousy-start/image/master/image-20201104115036423.png)

当客户端访问 Servlet1 时，由于在 Servlet1 中调用了 sendRedirect() 方法将请求重定向到 Servlet2，因此，浏览器收到 Servlet1 的响应消息后，立刻向 Servlet2 发送请求，Servlet2 对请求处理完毕后，再将响应消息回送给客户端浏览器并显示。

#### 2.4.2 请求重定向和请求转发的区别

- 请求重定向指的是服务端会通过向客户端响应302的响应码，并指定重定向的资源路径，由客户端再次发起请求，此时会进行两次请求。

- 请求转发值得是服务端直接将当前请求转发给另一个资源处理请求，不会经过客户端再次请求，此时只进行一次请求。

#### 2.4.3 请求重定向的方法

| 方法名称     | 方法签名                                                     | 描述                                                         | 注意                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| sendRedirect | public void sendRedirect(java.lang.String location) throws java.io.IOException | 该方法用于生成 302 响应码和 Location 响应头，从而通知客户端重新访问 Location 响应头中指定的 URL | 1、Location如果是以"/"开头，那么地址就会被解析成相对于Servlet容器的绝对路径。<br/>2、如果Location是相对的，且不以"/"开头，那么就会被解析成相对于当前路径的相对位置。<br/>3、如果Location以两个"/"开头，则容器将其解释为网络路径引用。 |

> 注意：
>
> 1、默认sendRedirect重定向返回的响应码为302，则重定向后的请求方法是不可预测的，有些旧的客户端会将请求换成GET请求。如果我们要保留原始的请求方法和消息主体那么就不能直接使用sendRedirect方法实现重定向，而是需要使用addHeader方法添加Location响应头，并设置响应码为307。
>
> 2、因为响应码302重定向，不同的客户端版本处理请求方法有些不一致，所以我们应该手动设置响应码为303，303对于客户端来说一定会用GET请求重定向的资源。HTTP **303 See Other** 重定向状态码，通常作为 PUT 或 POST 操作的返回结果，它表示重定向链接指向的不是新上传的资源，而是另外一个页面，比如消息确认页面或上传进度页面。而请求重定向页面的方法要总是使用 GET 。

## 3. 代码演示

- Servlet1代码

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
@WebServlet(value = "/response")
public class ResponseServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("=====Servlet1开始处理POST请求====");
        // 不以"/"开头会被解析成相对当前路径的相对路径， 将请求重定向到/response2处理
        // 如果是重定向到Servlet用相对路径比较好，如果是重定向到页面，用绝对路径比较好
        resp.sendRedirect("response2");
    }
}
```

- Servlet2代码

```java
package com.inconspicuousy.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.nio.charset.Charset;
import java.nio.charset.StandardCharsets;
import java.util.Locale;

/**
 * @author peng.yi
 */
@WebServlet(value = "/response2")
public class Response2Servlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("=======Servlet2接收到GET请求======");
        // 设置状态码
        resp.setStatus(HttpServletResponse.SC_OK);
        // 通过setHeader、addHeader设置头部信息
        resp.setHeader("Self", "inconspicuousy");
        resp.addHeader("Self", "inconspicuousy_add");
        // 设置MIME媒体类型的同时指定编码（指定编码方式为ISO-8859-1，看setCharacterEncoding是否会覆盖该编码）
        resp.setContentType("text/plain;charset=ISO-8859-1");
        // 设置本地化信息的同时，指定相应的编码
        resp.setLocale(Locale.CHINA);
        // 设置编码方式
        resp.setCharacterEncoding(StandardCharsets.UTF_8.toString());
        PrintWriter writer = resp.getWriter();
        writer.println("redirect .... ");
        writer.flush();
        writer.close();
    }
}
```

- POSTMAN模拟POST请求Servlet1

```http
POST /servlet_annotation/response HTTP/1.1
Host: localhost:8080
```

- 控制台输出结果

```
=====Servlet1开始处理POST请求====
=======Servlet2接收到GET请求======
```

- POSTMAN接收到响应的正文

```
redirect .... 
```

- POSTMAN接收到的响应的头部信息

```http
Self: inconspicuousy
Self: inconspicuousy_add
Content-Type: text/plain;charset=UTF-8
Content-Language: zh-CN
Transfer-Encoding: chunked
Date: Wed, 04 Nov 2020 05:22:06 GMT
Keep-Alive: timeout=20
Connection: keep-alive
```

从响应头可以发现，addHeader不会覆盖原始的Header信息，重新添加头信息；setCharacterEncoding会重新设置编码方式，优先级最高。

