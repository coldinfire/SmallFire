---
title: "Tomcat 使用"
date: 2017-11-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - tomcat
---

### Tomcat 简介

[Tomcat 服务器](http://tomcat.apache.org/)是一个免费的开放源代码的**Web 应用服务器**，属于轻量级应用，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试 JSP  程序的首选。

### Tomcat 安装

Tomcat 需要 java 的运行环境，在安装 Tomcat 时，需要安装 JDK。

#### 下载

Step 1：打开 Tomcat 官网下载：[https://tomcat.apache.org/](https://tomcat.apache.org/)

Step 2：左侧选择下载的版本，然后选择下载 Tomcat 的安装方式 - 解压版和安装版。解压版是第一个选项链接；安装版是最后一个选项链接（Windows Service Installer – Windows服务的安装程序），点击进行下载。

![下载选项](/images/Tomcat/tomcat_download.png)

#### 安装

解压版：直接解压压缩文件

安装版：双击下载的 tomcat.ext 文件进行安装；*安装Tomcat之前需安装并配置 jdk 和 jre ，请在安装并配置完毕后，再进行安装Tomcat*

#### Tomcat 目录结构

![目录结构](/images/Tomcat/tomcat_dir.png)

tomcat的服务器主配置文件在`/conf/server.xml`中，其中指定了默认的3个端口：

```xml
<Connector port="8090" protocol="HTTP/1.1" redirectPort="8443"/>
<Server port="8005" shutdown="SHUTDOWN">
<Connector protocol="AJP/1.3" port="8009" redirectPort="8443" />
```

- 8090: web服务器访问的端口
- 8009：AJP包重定向协议端口，用于本机对其它web服务器发起的连接
- 8005：用于发送 shutdown 指令，生产情况下需要修改此端口和命令。

#### 启动

文件目录下的 `bin/startup.bat` 双击运行该文件即可。

*双击 startup.bat 闪退的解决方法：*

对于免安装版的 Tomcat 来说，在启动 Tomcat 时，需要读取环境变量和配置信息，缺少了这些信息，就不能登记环境变量，导致闪退。

- 首先需要确认 java 环境是否配置正确，jdk 是否安装正确
  - win+R 打开 cmd，输入 java 或者 javac 显示正确即可
- 确认Tomcat的环境变量配置
  - 右击->编辑 `startup.bat` ，在文件的最上面加入下面两行：
    - SET JAVA_HOME=D:\JAVA\bin （java jdk目录）
    - SET JRE_HOME=D:\JAVA\jre（java jre目录）
  - 右击->编辑 `shutdown.bat` ，在文件的最上面加入上面两行

*端口被占用的解决方法：*

- 查看端口占用
  - 查看端口使用情况：`netstat -aon|findstr "8080"`
  - 查看端口被哪个应用占用：`tasklist|findstr "8080"`
- 关闭对应进程
  - 按进程号关闭：`taskkill /pid 20804`
  - 按进程名关闭：`taskkill /im notepad.exe`
  - 有提示的关闭：`taskkill /t /pid 20804`
  - 强行终止进程：`taskkill /f /pid 20804`

访问：浏览器输入 http://localhost:端口号 访问

#### 关闭

- 双击执行 `bin\shutdown.bat`
- 在运行界面执行 `Ctrl + c`

有时通过 tomcat 自带的脚本是无法停止服务的，一般会自定义脚本，使用kill 命令来结束进程，也可以多次使用kill -9 命令终止服务。

在重启 tomcat 服务前，需要删除上次启动服务所加载的缓存文件，默认目录为`/usr/local/tomcat/work`和`/usr/local/tomcat/temp` 避免缓存原因造成不必要的问题。