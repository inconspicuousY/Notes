## 初识Servlet

### 1. Servlet、Servlet容器、Tomcat之间的关系。

> - `Servlet`- Servlet只是Java的一个Web组件。简单来说就是Jar包，可以通过引入Servlet的Jar包使用Servlet的功能。Servlet是由Servlet容器进行管理。
> - `Servlet容器`- 也叫Servlet引擎，Servlet容器是Web服务器或者是应用程序服务器的一部分，提供发送请求和响应的网络服务，也负责管理Servlet的整个生命周期。
> - `Tomcat`- Tomcat是一个Web服务器，他既可以做Servlet容器，也可以将Servlet容器作为他的扩展，主要用于存放网页资源，实现了对Servlet和JSP的支持。

#### 1.1 客户端与Tomcat交互该过程

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master/20200930155656.png)



> 注意：
>
> Servlet容器因为负责HTTP请求和响应与表示请求和响应的对象相互转化，所以Servlet容器必须支持HTTP协议。

### 2. Servlet与其他技术比较

> 在功能方面，Servlet位于通用网关接口（CGI）和专有服务器扩展之间。所以对比其他服务器扩展有如下的优点：
>
> 1. 因为使用不同的处理模式，一般情况下Servlet会比其他CGI脚本更快。
> 2. 他们使用一个由许多Web服务器支持的标准API。
> 3. 它们具有Java编程语言的所有优点，包括易于发展和平台独立性。
> 4.  他们可以访问Java平台可用的大量API。

### 3. Servlet3.1与Servlet2.5的适配

> 