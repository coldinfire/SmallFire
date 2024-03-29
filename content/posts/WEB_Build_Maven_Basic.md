---
title: " Maven 使用 "
date: 2017-12-14
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

Maven 是 apache 下的开源项目，自动化构件工具，服务于java的项目构件和依赖管理。Maven 提倡”约定优于配置“(Convention Over Configuration)，这是 Maven 最核心的设计理念之一，使用约定可以大量减少配置。

Maven 采用引用的方式将依赖的 jar 包引入项目，不对真实的 jar 包进行 copy。但是打包的时候，运行时所需要的 jar 包都会被 copy 到安装包中。

Maven 是一个项目管理工具，它包含了一个项目对象模型 (POM：Project Object Model)，一组标准集合，一个项目生命周期(Project Lifecycle)，一个依赖管理系统(Dependency Management System)，和用来运行定义在生命周期阶段(phase)中插件(plugin)目标(goal)的逻辑。

1、项目对象模型 (Project Object Model)

 POM 对象模型，每个 maven 工程中都有一个 pom.xml 文件，定义工程所依赖的 jar 包、本工程的坐标、打包运行方式。

执行任务或目标时，Maven 会在当前目录中查找 POM。它读取 POM，获取所需的配置信息，然后执行目标。

2、依赖管理系统 (基础核心)

maven 通过坐标对项目工程所依赖的 jar 包统一规范管理。

3、maven 定义了一套项目生命周期

清理、初始化、编译、测试、报告 、打包、部署、站点生成。

4、一组标准集合

强调：maven 工程有自己标准的工程目录结构、定义坐标有标准。

5、插件(plugin)目标(goal)

maven 管理项目生命周期过程都是基于插件完成的。

#### Maven仓库

Maven 给我们带来的最大的好处就是管理 jar 包，Maven 管理 jar 包的模式是从远程仓库中把 jar 包下载到本地仓库中。仓库可以理解为缓存的地址，就是缓存项目需要的 jar 包，那么随着项目的扩大，本地仓库肯定为越来越大。

1、中央仓库

就是远程仓库；在 maven 软件中内置一个远程仓库地址http://repo1.maven.org/maven2 ，它是中央仓库，服务于整个互联网，它是由 Maven 团队自己维护，里面存储了非常全的 jar 包，它包含了世界上大部分流行的开源项目构件。可以通过修改配置文件，将仓库修改成自己需要的地址源。

要浏览中央仓库的内容，maven 社区提供了一个 URL：[http://search.maven.org/#browse](http://search.maven.org/#browse)。使用这个仓库，开发人员可以搜索所有可以获取的代码库。

2、本地仓库

相当于缓存，工程第一次会从远程仓库（互联网）去下载 jar 包，将 jar 包存在本地仓库（本地电脑上）。第二次获取 jar 包时不需要从远程仓库去下载，先从本地仓库找，找到了则直接使用；如果本地仓库找不到，Maven 才会去远程仓库找，找到后下载到本地仓库再使用。

Maven默认的本地仓库目录位置：`${user.home}\.m2\repository`

3、私服仓库

私服是一种特殊的远程仓库，为了节省带宽和时间，在局域网内架设一个私有的仓库服务器，用其代理所有外部的远程仓库。内部的项目还能部署到私服上供其他项目使用。

### Maven环境搭建

#### Maven下载

可以到 maven 的 [官网下载](http://maven.apache.org/download.cgi)，不同操作系统下载不同的压缩包：Windows: apache-maven-version-bin.zip ；将下载的压缩包解压到[D]盘的根目录下，maven 文件夹包含以下内容：

![Maven Folder](/images/WEB/Maven1.png)

- bin：存放了 maven 的可执行程序，比如 mvn tomcat:run

- boot：存放了一些 maven 本身的引导程序，如类加载器等
- conf：存放了 maven 的一些配置文件，如 setting.xml 文件
- lib：存放了 maven 本身运行所需的一些 jar 包

#### 默认仓库位置配置

1、创建 .m2 文件夹

我们会发现`${user.home}`下面没有`.m2`文件夹，这时候需要创建文件夹。新建文件夹的时候，windows是不支持文件命以小数点开头的，这里需要用命令行创建文件夹。在想要创建 .m2 文件夹的文件夹下按住shift加鼠标右键打开右键菜单，进入 powerShell 或者命令行。

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
  <name>aliyun maven</name>
  <url>https://maven.aliyun.com/nexus/content/groups/public/</url>
</mirror>
<mirror>
  <id>repo1</id>
  <mirrorOf>central</mirrorOf>
  <name>central repo</name>
  <url>http://repo1.maven.org/maven2/</url>
</mirror>
```

#### 本地仓库配置

1、到官网下载 maven

将下载的 apache-maven-version-bin.zip 解压到[D]盘根目录下

2、配置本地仓库

打开 maven 的安装目录中 conf/settings.xml 文件，在这里配置自定义的本地仓库，目录为：D:\Maven-Repository

`<localRepository>D:\Maven-Repository</localRepository>`

#### 全局 Setting 与用户 Setting

在 maven 安装目录下的有 conf/setting.xml 文件，此 setting.xml 文件用于 maven 的所有项目，它作为 maven 的全局配置。

如需要个性配置则需要在用户配置中设置，用户配置的 setting.xml 文件默认的位置在：`${user.home}/.m2/settings.xml` 位置。

配置文件查找顺序：maven 会先找用户配置，如果找到则以用户配置文件为准，否则使用全局配置文件。

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

选择菜单  File ->new -> Other；选择 Maven 下的 Maven Project，则出现以下界面：

![Maven Project](/images/WEB/Maven7.png)

选中红框部分的复选框（跳过骨架），如果不跳过骨架，可以选择并使用一些项目骨架模板来创建 Maven 项目(例如：maven-archetype-quickstart)。

![Maven Project](/images/WEB/Maven14.png)

点击 next，输入项目参数：

![Maven Project](/images/WEB/Maven8.png)

这里有几个参数groupId、artifactId、version、packaging。

- groupId：定义当前 Maven 项目隶属的实际项目，项目的名称；项目名称以域名的倒序，比如：com.baidu.mavendemo
- artifactId：该元素定义实际项目的中的一个 Maven 项目(模块名称即子项目名称)
- version：定义 Maven 项目当前所处的版本，snapshot 为快照版本即非正式版本；release 为正式发布版本。
- packaging：定义 Maven 项目的打包方式，有三个值 jar、pom 和 war。
  - jar：代表普通 java 项目，执行 package 会打成 jar 包
  - war：代表 web 项目，执行 package 会打成 war 包
  - pom：用于集合 maven 组件的时候使用，通常父工程设置成 pom

#### 工程目录结构说明

Maven 提倡使用一个共同的标准目录结构，Maven 使用约定优于配置的原则。

- src/main/java：核心代码部分，项目的 java 源文件（不要放配置文件）
- src/main/resources：项目所需要的配置文件（不要放 java 文件）
- src/main/webapp：页面资源，js，css，图片等资源
- src/test/java：单元测试程序 java 源文件
- src/test/resources：单元测试程序所用的配置文件
- target：打包输出目录
- target/classes：编译输出目录
- target/test-classes：测试编译输出目录
- pom.xml：Maven进行工作的主要配置文件。

#### 生成 web.xml 文件

新建好的 web 项目下面是没有 web.xml 的，需要在右击 Deploymeny Descriptor，然后点击 Generate Deployment Descriptor，生成 web.xml 文件。

#### 定义工程坐标：pom.xml

maven 对所有工程管理基于坐标进行管理。Nexus-indexer 是一个对 Maven 仓库编纂索引并提供搜索功能的类库，它是 Nexus 项目的一个子模块。

Maven 坐标为各种构件引入了秩序，任何一个构件都必须明确定义自己的坐标，一组 Maven 坐标是通过一些元素定义的，他们是 groupId、artifactId、version、packaging、clas-sifier。

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
    <!-- 产品版本号 -->
    <version>1.0</version>
    <!-- 打包方式 jar,war,pom 等 -->
    <packaging>war</packaging>
    <!-- 项目构建配置，配置编译、运行插件等 -->
    <build><plugins><plugin>...</plugin></plugins></build>
    <!-- 项目依赖构件配置，配置项目依赖构件的坐标 -->
    <dependencies><dependency>...</dependency></dependencies>
</project>
```

#### 设置编译版本

Maven 的核心插件之一 compiler 插件支持的 JDK 版本较低 ，我们需要将编译版本改为自己所需的版本，这里设置为 JDK1.8。

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-war-plugin</artifactId>
        <version>3.3.1</version>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.2</version>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
          <encoding>UTF-8</encoding>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
        <configuration>
          <!-- 设置编码格式 -->
          <uriEncoding>UTF-8</uriEncoding>
          <!-- 控制 tomcat 端口号 -->
          <port>80</port>
          <!-- 项目发布到 tomcat 后的名称 -->
          <!-- 如果写 /，相当于项目发布后的名称为 ROOT -->
          <!-- 如果写 /abc，相当于项目发布后的名称为 abc -->
          <path>/</path>
        </configuration>
      </plugin>
    </plugins>
  </build>
```

将上边的配置信息粘贴到pom.xml中。点击工程右键  Maven ->  Update Project，弹出窗口后OK 。操作后 ，编译版本改为 1.8。

![Maven Project](/images/WEB/Maven9.png)

#### Maven dependency for Servlet API

For Tomcat 8 (Java 8, Servlet 3.1)

```xml
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <version>3.1.0</version>
  <scope>provided</scope>
</dependency>
<dependency>
  <groupId>javax.servlet.jsp</groupId>
  <artifactId>javax.servlet.jsp-api</artifactId>
  <version>2.3.0</version>
  <scope>provided</scope>
</dependency>
<dependency>
  <groupId>javax.el</groupId>
  <artifactId>javax.el-api</artifactId>
  <version>3.0.0</version>
  <scope>provided</scope>
</dependency>
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>jstl</artifactId>
  <version>1.2</version>
</dependency>
```

#### 执行 Maven 命令

在 Maven 项目上点击鼠标右键，在弹出的选择菜单中共选择 Run As，可以看到常见的 Maven 命令。

![Maven Command](/images/WEB/Maven12.png)

如果默认选项中没有我们想要执行的 Maven 命令，可以选择 `Maven build ...` 来自定义 Maven 运行命令。在 Goals 中输入想要执行的命令：tomcat7:run，设置下自定义名称，点击 Run 即可。下可以直接选择 `Maven build` 来使用之前配置的命令。

![Maven Command](/images/WEB/Maven13.png)



