# Tomcat启动乱码问题

> 情景描述：
>
> 1、当使用Tomcat的 `startup.bat`启动Tomcat时，启动的控制台出现乱码。
>
> 2、当将外部Tomcat作为Idea的Web服务器时，Idea的控制台输出出现乱码。

## 1. 原因及其解决方案

乱码出现的原因则是因为字符的编码方式和解码方式不一致导致的。在windows电脑中，系统默认的编码方式为GBK，而Tomcat默认的控制台的编码方式UTF-8，我们只要将其更换为一致即可。

## 2. 解决方式

### 2.1 Tomcat控制台编码方式配置

- 从Tomcat安装目录中打开 `conf/logging.properties` 文件
- 更改 `java.util.logging.ConsoleHandler.encoding = UTF-8` 该属性，将编码方式值替换为我们指定的编码方式即可。

```properties
java.util.logging.ConsoleHandler.encoding = GBK
```

