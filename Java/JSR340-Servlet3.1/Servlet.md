# Servlet

## 1. 简介

> `Servlet` 是在Web服务器中运行的小型Java程序，Servlet通常通过HTTP（超文本传输协议）接收和响应来自Web客户端的请求。
>
> 在Java中定义了一个 `Servlet`接口，该接口定义了所有的Servlet必须实现的方法。

## 2. 方法列表

| 方法名称         | 方法签名                                                     | 方法描述          |
| ---------------- | ------------------------------------------------------------ | ----------------- |
| init             | public void init(ServletConfig config)                       | servlet初始化方法 |
| getServletConfig | public ServletConfig getServletConfig()                      |                   |
| service          | public void service(ServletRequest req, ServletResponse res) |                   |
| getServletInfo   | public String getServletInfo()                               |                   |
| destroy          | public void destroy()                                        |                   |

### 2.1 init

> init方法为Servlet初始化方法， 该方法具有如下的特点：
>
> 1、init方法是在对象Servlet对象创建完之后被调用。
>
> 2、该方法只会被执行一次，并且在Servlet接收请求之前被调用。
>
> 3、当发生如下两种情况时，