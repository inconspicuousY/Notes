# 解决Idea控制台乱码问题

> 场景描述
>
> 在大多数Web服务器中，比如Tomcat，默认设置的控制台编码方式为UTF-8，但是默认情况下，Idea控制台编码方式为GBK，所以我们将两者改为一致即可。

## 1. Tomcat控制台编码方式设置

tomcat控制台日志输出配置文件为tomcat安装位置下`conf/logging.properties`。其中 `java.util.logging.ConsoleHandler.encoding`属性就是设置控制台编码方式的，默认情况下设置为`java.util.logging.ConsoleHandler.encoding = UTF-8`，在不改变Idea为GBK编码方式的情况下，我们只需要将UTF-8更换为GBK即可解决Idea控制台乱码问题。

```
java.util.logging.ConsoleHandler.encoding = GBK
```

## 2. Idea控制台编码方式设置

因为Idea本身是由Java语言编写的客户端，其控制台输出编码方式是由自身的Java虚拟机控制的，所以我们只需要更改Idea客户端对应的虚拟机的编码方式即可，设置方式如下。

- 点击Idea菜单栏 `Help -> Edit Custom VM Options `
- 在弹出的虚拟机配置文件中添加 `-Dfile.encoding=utf-8` 即可。

```
-Dfile.encoding=utf-8
```

> 注意：修改完idea的虚拟机配置之后，需要重新启动Idea，配置文件才会生效，随着Idea的重新启动，Idea的虚拟机会重新加载配置文件。