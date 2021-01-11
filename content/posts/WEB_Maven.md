---
title: "Maven使用"
date: 2018-03-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - Maven

---

### Maven简介

#### 什么是Maven

 Maven 是 apache 下的开源项目，项目管理工具，管理java项目。

1、项目对象模型 (Project Object Model)

 POM 对象模型，每个 maven 工程中都有一个 pom.xml 文件，定义工程所依赖的 jar 包、本工程的坐标、打包运行方式。

执行任务或目标时，Maven 会在当前目录中查找 POM。它读取 POM，获取所需的配置信息，然后执行目标。

2、依赖管理系统 (基础核心)

maven 通过坐标对项目工程所依赖的jar包统一规范管理。

3、maven 定义一套项目生命周期

清理、初始化、编译、测试、报告 、打包、部署、站点生成

4、一组标准集合

强调：maven 工程有自己标准的工程目录结构、定义坐标有标准。

5、maven 管理项目生命周期过程都是基于插件完成的

#### Maven仓库

Maven 给我们带来的最大的好处就是管理jar包，Maven 管理 jar 包的模式是从远程仓库中把 jar 包下载到本地仓库中。仓库可以理解为缓存的地址，就是缓存项目需要的 jar 包，那么随着项目的扩大，本地仓库肯定为越来越大。

1、中央仓库

就是远程仓库，仓库中jar由专业团队（maven 团队）统一维护。

- 中央仓库的地址：[http://repo1.maven.org/maven2/](http://repo1.maven.org/maven2/)

2、本地仓库

相当于缓存，工程第一次会从远程仓库（互联网）去下载 jar 包，将 jar 包存在本地仓库（本地电脑上）。第二次不需要从远程仓库去下载。先从本地仓库找，如果找不到才会去远程仓库找。

3、私服

在公司内部架设一台私服，其它公司架设一台仓库，对外公开。

### Maven环境搭建

#### Maven下载

可以到 maven 的 [官网下载](http://maven.apache.org/download.cgi)，不同操作系统下载不同的压缩包：Windows: apache-maven-version-bin.zip ；将下载的压缩包解压到[D]盘的根目录下，maven 文件夹包含以下内容：

![Maven Folder](/images/WEB/Maven1.png)

#### 默认仓库位置配置

Maven默认的本地仓库目录位置：`${user.home}/.m2/repository`

1、创建 .m2 文件夹

我们会发现`${user.home}`下面没有`.m2`文件夹，这时候需要创建文件夹。新建文件夹的时候，windows是不支持文件命以小数点开头的，这里需要用命令行创建文件夹。在想要创建.m2文件夹的文件夹下按住shift加鼠标右键打开右键菜单，进入powerShell或者命令行。

![powerShell](/images/WEB/Maven3.png)

输入命令 `mkdir .m2`创建文件夹

![powerShell](/images/WEB/Maven4.png)

2、在 .m2 文件夹下创建必要的文件结构

![Maven Forder](/images/WEB/Maven5.png)

找到之前下载的 maven 目录，复制`conf/settings.xml`文件到上方的.m2文件夹；然后创建一个repository文件夹在 .m2文件夹下面。

最好在 mirrors 标签下加上阿里云的镜像连接站点，这样能极大的加快下载 jar 包的速度。

```xml
<mirror>
  <id>aliyunmaven</id>
  <mirrorOf>central</mirrorOf>
  <name>aliyun repository</name>
  <url>https://maven.aliyun.com/repository/central</url>
</mirror>
<mirror>
  <id>repo1</id>
  <mirrorOf>central</mirrorOf>
  <name>central repo</name>
  <url>http://repo1.maven.org/maven2/</url>
</mirror>
<mirror>
  <id>aliyunmaven</id>
  <mirrorOf>apache snapshots</mirrorOf>
  <name>aliyun apache repository</name>
  <url>https://maven.aliyun.com/repository/apache-snapshots</url>
</mirror>
```

#### 本地仓库配置

1、到官网下载 maven

将下载的 apache-maven-version-bin.zip 解压到[D]盘根目录下

2、配置本地仓库

打开 maven 的安装目录中 conf/settings.xml 文件，在这里配置自定义的本地仓库，目录为：D:\Maven-Repository

`<localRepository>D:\Maven-Repository</localRepository>`

### Eclipse 配置 Maven

#### 使用 Eclipse 自带的 maven 插件

 1、配置 Maven 的安装目录

进入 eclipse 选择菜单 Windows -> Preferences ；在左侧的树状导航中找到 Maven，选择 User Settings 可以看到默认的本地仓库目录位置、默认的User Settings 位置：

![Eclipse Maven](/images/WEB/Maven2.png)

2、配置 User Settings

选择 .m2 目录下的 settings.xml；然后点击 Update Settings -> Apply 按钮。

#### 重建本地仓库索引

选择菜单 window -> show View -> Other 选择 Maven Repositories

![Eclipse Maven Repositories](/images/WEB/Maven6.png)


打开Global  Repositories 选择对应的 mirror 右键选择 Rebuild  Index 重新创建索引，完成后可以在 D:\Maven-Repository 看到下载的 Jar 包内容。

### 创建Maven Project

#### 创建项目

选择菜单  File ->new -> Other；选择 Maven 下的 Maven Project：

![Maven Project](/images/WEB/Maven7.png)

选中红框部分的复选框（跳过骨架），next：

![Maven Project](/images/WEB/Maven8.png)

这里有几个参数Group Id、Artifact Id、Version、Packaging。其中Group Id表示组的id，在一些大型的分组开发中会使用。Artifact Id就是以前的项目名。Version代表版本。Packaging有三个值jar、pom和war，其中jar代表普通java项目，war代表web项目，pom用于集合MAVEN组件的时候使用的。

#### 工程目录结构说明

Maven 提倡使用一个共同的标准目录结构，Maven 使用约定优于配置的原则。

project_direct

- src/main/java：项目的 java 源文件（不要放配置文件）
- src/main/resources：项目所需要的配置文件（不要放java文件）
- src/test/java：单元测试程序 java 源文件
- src/test/resources：单元测试程序所用的配置文件
- target：打包输出目录
- target/classes：编译输出目录
- target/test-classes：测试编译输出目录
- pom.xml：Maven进行工作的主要配置文件。

#### 生成 web.xml 文件

新建好的 web 项目下面是没有 web.xml 的，需要在右击 Deploymeny Descriptor，然后点击 Generate Deployment Descriptor，生成web.xml文件。

#### 定义工程坐标：pom.xml

maven 对所有工程管理基于坐标进行管理。

```xml
<project xmlns = "http://maven.apache.org/POM/4.0.0"
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- 模型版本 -->
    <modelVersion>4.0.0</modelVersion>
    <!-- 公司或者组织的唯一标志，并且配置时生成的路径也是由此生成， 如com.companyname.project-group -->
    <!-- maven会将该项目打成的jar包放本地路径：/com/companyname/project-group -->
    <groupId>com.companyname.project-group</groupId>
    <!-- 项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->
    <artifactId>project</artifactId>
    <!-- 版本号 -->
    <version>1.0</version>
    <!-- 打包方式 -->
    <packaging>war</packaging>
</project>
```

坐标包括：

- groupId：项目的名称，项目名称以域名的倒序，比如：com.baidu.mavendemo

- artifactId：模块名称（子项目名称）

- version：模块的版本；snapshot（快照版，没有正式发行）、release（正式发行版本）
  输入后，Finish 。

#### 设置编译版本

我们现在的Maven工程默认是JDK1.5 ，我们需要将编译版本改为JDK1.8。

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.1</version>
      <configuration>
        <source>1.8</source>
        <target>1.8</target>
      </configuration>
    </plugin>
  </plugins>
</build>
```

将上边的配置信息粘贴到pom.xml中。点击工程右键  Maven ->  Update Project，弹出窗口后OK 。操作后 ，编译版本改为 1.8。

![Maven Project](/images/WEB/Maven9.png)

#### 编写代码

（1）在src/main/java 目录下创建包 com.baidu.mavendemo

（2）在包com.baidu.mavendemo下创建HelloWorld 类

```java
package com.baidu.mavendemo;
public class HelloWorld {
  public static void main(String[] args) {
    System.out.println("Hello World!!");
  }
}
```

#### 添加依赖

右键点击工程  Maven -- >  Add Dependency 添加 hibernate 包，spring-webmvc等包都可以直接搜索添加到pom.xml 文件中。

![Add Dependency](/images/WEB/Maven10.png)

添加后打开pom.xml，发现多了以下信息

```xml
<dependencies>
  <dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>5.0.7.Final</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.3.4.RELEASE</version>
  </dependency>
</dependencies>
```

我们再看工程目录下的Maven Dependecies 下又多了很多jar包；

![Add Dependency](/images/WEB/Maven11.png)

我只是加了一个hibernate的核心包和一个spring-webmvc包，为什么会多出这么多jar包呢？这是因为hibernate的核心包本身又会依赖其它的jar包，所以导入hibernate包自动会添加hibernate所依赖的包；spring-webmvc一样的道理。




