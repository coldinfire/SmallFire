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

1、中央仓库

就是远程仓库，仓库中jar由专业团队（maven 团队）统一维护。

- 中央仓库的地址：[http://repo1.maven.org/maven2/](http://repo1.maven.org/maven2/)

2、本地仓库

相当于缓存，工程第一次会从远程仓库（互联网）去下载 jar 包，将 jar 包存在本地仓库（本地电脑上）。第二次不需要从远程仓库去下载。先从本地仓库找，如果找不到才会去远程仓库找。

3、私服

在公司内部架设一台私服，其它公司架设一台仓库，对外公开。

### Maven环境搭建

#### Maven下载

可以到 maven 的 [官网下载](http://maven.apache.org/download.cgi)，不同操作系统下载不同的压缩包：Windows: apache-maven-version-bin.zip ；将下载的压缩包解压到D盘根目录，D盘根目录会有下面的文件夹。

#### 本地仓库配置

1、拷贝本地仓库

将资料中的 repository_ssh.zip 解压到D盘

2、配置本地仓库

Maven默认的本地仓库目录位置：`~/,m2/repository`

打开 maven 的安装目录中 conf/settings.xml 文件，在这里配置自定义的本地仓库：目录为D:\repository_ssh

`<localRepository>D:\repository_ssh</localRepository>`

#### Eclipse 配置 Maven

1、配置 Maven 的安装目录

进入 eclipse 选择菜单 windows -> Preferences ；在左侧的树状导航中点击 add 按钮，弹出窗口后选择 maven 的安装目录；然后点击 Apply

2、配置 User Settings

选择左侧树形导航的 User Settings  ,选择 Maven 目录下 conf 下的 settings.xml；然后点击 Update Settings 、Reindex 和 Apply 按钮.

#### 重建本地仓库索引

选择菜单 window --> show View


选择 Rebuild  Index  重新创建索引

### 创建Maven Project

#### 创建项目

选择菜单  File ->new -> Other；选择 Maven 下的 Maven Project,   Next

选中下图红框部分的复选框（跳过骨架），next

定义工程坐标：maven 对所有工程管理基于坐标进行管理。

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
</project>
```

坐标包括：

- groupId：项目的名称，项目名称以域名的倒序，比如：com.baidu.mavendemo

- artifactId：模块名称（子项目名称）

- version：模块的版本；snapshot（快照版，没有正式发行）、release（正式发行版本）
  输入后，Finish 。

完成后如下图

**工程目录结构说明**

Maven 提倡使用一个共同的标准目录结构，Maven 使用约定优于配置的原则。

project_direct

- /src/main/java：项目的 java 源文件（不要放配置文件）
- /src/main/resources：项目所需要的配置文件（不要放java文件）
- /src/test/java：单元测试程序 java 源文件
- /src/test/resources：单元测试程序所用的配置文件
- /target：打包输出目录
- /target/classes：编译输出目录
- /target/test-classes：测试编译输出目录
- pom.xml：Maven进行工作的主要配置文件。

**编写代码**

（1）在src/main/java 目录下创建包 com.baidu.mavendemo

（2）在包cn.itcast.mavendemo下创建HelloWorld 类

```java
package cn.itcast.mavendemo;
public class HelloWorld {
  public static void main(String[] args) {
    System.out.println("Hello World!!");
  }
}
```

#### 设置编译版本

我们现在的Maven工程默认是JDK1.5 ，我们需要将编译版本改为JDK1.7。

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>
  </plugins>
</build>
```

将上边的配置信息粘贴到pom.xml中。点击工程右键  Maven ->  Update Project，弹出窗口后OK 。操作后 ，编译版本改为 1.7。

#### 添加依赖

右键点击工程  Maven -- >  Add Dependency添加 hibernate 包。

添加后打开pom.xml，发现多了以下信息

```xml
<dependencies>
  <dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>5.0.7.Final</version>
  </dependency>
</dependencies>
```

我们再看工程目录下的Maven Dependecies 下又多了很多jar包；奇怪了！我只是加了一个hibernate的核心包，为什么会多出这么多jar包呢？这是因为hibernate的核心包本身又会依赖其它的jar包，所以导入hibernate包自动会添加hibernate所依赖的包。





