# Servlet配置虚拟路径映射

## 1. 简介

> 虚拟路径：在web.xml文件中，一个&lt;servlet-mapping&gt;元素用于映射一个Servlet的对外访问路径，该路径也就是所说的虚拟路径。
>
> Servlet容器就是解析客户端的请求路径，根据符合条件的虚拟路径找到能够处理请求的Servlet的。

```xml
 <servlet-mapping>
        <servlet-name>LifecycleServlet</servlet-name>
        <!-- url-pattern就是配置虚拟路径 -->
        <url-pattern>/lifecycle</url-pattern>
    </servlet-mapping>
```

当然，如果采用的是注解方式，那么@WebServlet注解的value或者urlPatterns属性也就是对应虚拟路径集合。

```java
@WebServlet(name = "initParam", urlPatterns = {"/init-param"}, initParams = {@WebInitParam(name = "initParamKey", value = "initParamValue")})
```

从注解中我们可以看到urlPatterns是一个字符串集合，也就是说一个Servlet可以对应多个虚拟路径，此时专业术语就是Servlet的多重映射。

## 2. Servlet的多重映射

> Servlet的多重映射指同一个Servlet可以被映射成为多条虚拟路径。也就是说，客户端可以通过多条路径实现对同一个Servlet的访问。

这里我们不考虑注解的方式，直接在通过在web.xml文件中进行配置有如下两种方式：

- 配置多个&lt;servlet-mappping&gt;标签元素。

```xml
<servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>com.inconspicuousy.servlet.HelloServlet</servlet-class>
        <init-param>
            <param-name>helloKey</param-name>
            <param-value>helloValue</param-value>
        </init-param>
    </servlet>

    <!-- 这里我们通过配置多个servlet-mapping标签的方式实现多重虚拟路径映射 -->
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>

    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <!-- 这里可以通过/hello.do也可以实现对HelloServlet的访问 -->
        <url-pattern>/hello.do</url-pattern>
    </servlet-mapping>
```

- 配置多个&lt;url-pattern&gt;子元素

```xml
<servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>com.inconspicuousy.servlet.HelloServlet</servlet-class>
        <init-param>
            <param-name>helloKey</param-name>
            <param-value>helloValue</param-value>
        </init-param>
    </servlet>

    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    <!-- 这里我们通过配置多个url-pattern标签的方式实现多重虚拟路径映射 -->
        <url-pattern>/hello.do</url-pattern>
    </servlet-mapping>
```

## 3. Servlet虚拟映射路径中使用通配符

> 在实际的开发过程中，开发者有时会希望某个目录下的所有的路径都访问同一个Servlet，这时，可以在Servlet映射的路径中使用通配符*。

### 3.1 通配符的格式

通配符的使用格式主要有两种：

- `*.扩展名`

例如 `*.do`可以匹配到.do结尾的所有的URL地址。

- `/*`

例如 `/abc/*`可以匹配到以/abc/开始的所有的URL地址。

> 注意
>
> 1、这两种通配符个不能混合使用。例如，/abc/*.do 是不合法的映射路径，这样配置的话在启动应用程序时会直接抛出java.lang.IllegalArgumentException: servlet映射中的&lt;url pattern&gt;[/lifecycle/\*.do]无效。
>
> 2、当客户端访问一个Servlet时，如果请求的URL地址能够匹配到多条虚拟路径，那么Tomcat将采取最具体匹配原则查找与请求URL最接近的虚拟映射路径。

#### 4. 默认的Servlet

> 如果某个Servlet的映射路径仅仅是一个正斜线（/）,那么这个Servlet就是当前Web应用的默认的Servlet。Servlet服务器在接收到请求时，如果web.xml文件中找不到匹配的虚拟路径，则会将访问请求交给默认的Servlet处理。

值得注意的是，在Tomcat安装目录下的web.xml文件中默认也配置了一个默认的Servlet。

```xml
   <servlet>
        <servlet-name>default</servlet-name>
        <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
        <init-param>
            <param-name>debug</param-name>
            <param-value>0</param-value>
        </init-param>
        <init-param>
            <param-name>listings</param-name>
            <param-value>false</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
<servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```

该默认的Servlet对Tomcat服务器上所有的Web应用都有效，当Tomcat服务器中某个web应用没有默认的Servlet时，就会将DefaultServlet作为默认的Servlet。

> DefaultServlet的特点：
>
> 当客户端访问Tomcat服务器中某个静态HTML文件时，DefaultServlet会判断HTML是否存在，如果存在，则会将数据以流的形式会送给客户端，否则会报404错误。