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