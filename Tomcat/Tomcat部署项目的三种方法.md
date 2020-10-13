# Tomcat部署项目的三种方式

> 场景描述：
>
> 在服务器上（这里我们就以自己的开发机为例），我们有一个JavaEE Web项目，我们要将其部署到Tomcat上使其运行起来，那么Tomcat部署Web项目有哪些方式呢？

## 1.  准备工作

- Tomcat

- JavaEE Web项目的War包，一般可采用Maven的package阶段获取到War包。

## 2. 部署方式

### 2.1 方式一：将war包直接放到webapps目录中

#### 2.1.1 操作步骤

- 将war包直接放到webapps目录中

![](https://raw.githubusercontent.com/inconspicuousy-start/image/master/20201013103557.png)

- 在bin目录中，运行对应的启动脚本文件

这里我是Windows，所以直接运行 `startup.bat` 文件即可， 运行之后tomcat会自动将war解押。

> 注意：如果控制台出现乱码问题，请将 `conf/logging.properties` 文件中 `java.util.logging.ConsoleHandler.encoding` 属性的属性值更换为指定的编码方式，Windows的话更换为 `GBK`即可。

#### 2.1.2 结果展示

我们打开浏览器输入 `localhost:8080/项目名称`即可访问到项目。

![image-20201013104726793](https://raw.githubusercontent.com/inconspicuousy-start/image/master/image-20201013104726793.png)

### 2.2 方式二：修改conf/server.xml文件

#### 2.2.1 操作步骤

- 自行解押war包，将war包放到任意目录。

![image-20201013105401790](https://raw.githubusercontent.com/inconspicuousy-start/image/master/image-20201013105401790.png)

- 打开 `conf/server.xml` 文件，在Host标签中添加Context子标签

```xml
<!-- path: 浏览器访问项目时的项目名称 -->
<!-- docBase：指定项目所在的位置 -->
<Context path="/servlet-xml" docBase="D:\web\servlet-xml-1.0-SNAPSHOT" />
```

#### 2.2.2 结果展示

打开浏览器输入 `localhost:8080/path指定的项目名称`即可访问到项目。

![image-20201013110604298](https://raw.githubusercontent.com/inconspicuousy-start/image/master/image-20201013110604298.png)

### 2.3 方式三：在conf/Catalina/localhost目录下添加项目配置文件

#### 2.3.1 操作步骤

- 自行解押war包，将war包放到任意目录。

这里我们还是将其放到 `D:\web\servlet-xml-1.0-SNAPSHOT` 目录下。

- 在 `conf/catalina/localhost`目录下创建 `项目名.xml`的配置文件。

![image-20201013112106127](https://raw.githubusercontent.com/inconspicuousy-start/image/master/image-20201013112106127.png)

#### 2.3.2 结果展示

打开浏览器输入 `localhost:8080/xml配置文件的文件名称` 即可访问到项目。

![image-20201013112241557](https://raw.githubusercontent.com/inconspicuousy-start/image/master/image-20201013112241557.png)

## 3. 三种方式的对比

| 部署方式                                                    | 优点                                                         | 缺点                                                         |
| ----------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| war包直接部署到webapps目录下                                | 无须配置，操作简单。                                         | 需要将war包复制tomcat中。                                    |
| 配置conf/server.xml文件                                     | 项目可部署在任意位置。                                       | server.xml文件为tomcat主要的配置文件，采用这种方式的话，如果项目较多，会造成server.xml比较臃肿。 |
| 在conf/Catalina/localhost目录下添加项目配置文件（推荐使用） | 项目可部署在任意位置而且项目的配置文件与tomcat的配置文件分离。 | 项目配置基本与Tomcat配置分离                                 |

