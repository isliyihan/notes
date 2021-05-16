# Tomcat

## 基本概念

- Tomcat 服务器是一个开源的轻量级Web应用服务器

## 下载和安装

- 下载：[https://tomcat.apache.org/download-80.cg](https://tomcat.apache.org/download-80.cg)
- 安装：解压缩

## 目录结构

- bin 主要存放二进制可执行文件和脚本。
- conf 主要存放各种配置文件。
- lib 主要用来存放Tomcat运行需要加载的jar包。
- logs 主要存放Tomcat在运行过程中产生的日志文件。
- temp 主要存放Tomcat在运行过程中产生的临时文件。
- webapps 主要存放应用程序，当Tomcat启动时会去加载该目录下的应用程序。
- work 主要存放tomcat在运行时的编译后文件，例如JSP编译后的文件。

## 启动和关闭

- 启动：bin/startup.bat
    - 闪退原因可能是没配置JAVA_HOME
    - 启动信息乱码的处理方式
        - logging.properties文件修改为 java.util.logging.ConsoleHandler.encoding = GBK
- 关闭：bin/shutdown.bat或者直接关闭dos窗口

## 配置文件

server.xml文件是服务器的主配置文件，可以设置端口号、设置域名或IP、默认加载的项目、请求编码等。

```xml
<Connector port="8888" protocol="HTTP/1.1" connectionTimeout="20000"
redirectPort="8443" />
```

tomcat-users.xml文件用来配置管理Tomcat服务器的用户与权限 。

```xml
<role rolename="manager-gui"/>
<user username="admin" password="123456" roles="manager-gui"/>
```

## 环境变量

- CATALINA_HOME：...\apache-tomcat-8.5.55
    - 使用shutdown关闭不了服务器是因为PATH的顺序导致

## 服务器默认返回路径规则

- localhost:8080，没有项目名时默认访问ROOM目录
- localhost:8080/ROOT，只有项目名时按顺序访问index.html、index.jsp

## IDEA配置tomcat

- 服务器名称、默认浏览器、配置更新classes文件时机、applicationContext
    - DEBUG模式下热部署才对Java文件生效