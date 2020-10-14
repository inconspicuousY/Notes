# Servlet

## 1. 简介

> `Servlet` 是在Web服务器中运行的小型Java程序，Servlet通常通过HTTP（超文本传输协议）接收和响应来自Web客户端的请求。
>
> 在Java中定义了一个 `Servlet`接口，该接口定义了所有的Servlet必须实现的方法。

## 2. 方法列表

| 方法名称         | 方法签名                                                     | 方法描述 |
| ---------------- | ------------------------------------------------------------ | -------- |
| init             | public void init(ServletConfig config)                       |          |
| getServletConfig | public ServletConfig getServletConfig()                      |          |
| service          | public void service(ServletRequest req, ServletResponse res) |          |
| getServletInfo   | public String getServletInfo()                               |          |
| destroy          | public void destroy()                                        |          |
