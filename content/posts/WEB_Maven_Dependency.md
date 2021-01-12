---
title: "Maven依赖管理"
date: 2018-03-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - Maven

---

## 依赖管理

### 依赖范围

Maven在编译主代码的时候需要使用一套 classpath，在编译和执行测试的时候会使用另一套 classpath，实际运行项目的时候，又会使用一套classpath。

依赖范围的作用就是用来控制依赖与这3种 classpath（编译classpath，测试classpath，运行classpath）的关系。

依赖范围由强到弱的顺序是：`compile>provided>runtime>test`。

maven有以下几种依赖范围:

1、compile ：编译、测试、运行有效；此范围为默认依赖范围；A在编译时依赖B，并且在测试和运行时也依赖。

- strus-core、spring-beans 打到 war 包或 jar 包

2、provided ：编译、测试有效，在运行时无效；A在编译和测试时需要B。

- servlet-api 就是编译和测试有用，在运行时不用（tomcat容器已提供），不会打到war

3、runtime：测试、运行有效，编译主代码时无效。

- jdbc驱动包 ，在开发代码中针对java的jdbc接口开发，编译不用。在运行和测试时需要通过jdbc驱动包（mysql驱动）连接数据库，需要打到war

4、test：只是测试有效，在编译主代码或者运行项目的使用时将无法使用此类依赖。

- junit 不会打到war

5、system：system 范围依赖与 provided 类似；但是你必须显式的提供一个对于本地系统中 JAR 文件的路径，需要指定 systemPath 磁盘路径，system 依赖不推荐使用。

#### 设置依赖范围

比如我们要将 mysql 驱动的依赖设置为 runtime 范围，配置如下：

```xml
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>5.1.6</version>
  <scope>runtime</scope>
</dependency>
```

将 servlet 依赖设置为 provided

```xml
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>servlet-api</artifactId>
  <version>2.5</version>
  <scope>provided</scope>
</dependency>
```

如果是 compile 就不需要设置了，因为 compile 是 scope 的默认值。重新执行打包为war , 会发现servlet-api.jar已经不存在。

### 依赖传递

#### 什么是依赖传递？

依赖传递(Transitive Dependencies)是 Maven 2.0 开始的提供的特性，依赖传递可以让我们不需要去寻找和发现所必须依赖的库，而是将会自动将需要依赖的库帮我们加进来。

A -> B(compile)  第一关系: a依赖b - compile

B -> C(compile)  第二关系: b依赖c - compile

1、直接依赖：A依赖B，B是A的直接依赖。在A的pom.xml中添加B的坐标。

2、传递依赖：B依赖C，C是A的传递依赖。

3、中间部分：传递依赖的范围，A依赖C的范围。

第一直接依赖的范围和第二直接依赖的范围决定了传递性依赖的范围。如下图所示，最左边代表第一直接依赖范围，最上面一行表示第二直接依赖范围，中间的交叉单元格则表示传递性依赖范围。

|          | compile  | test   | provided | runtime  |
| -------- | -------- | ------ | -------- | -------- |
| compile  | compile  | ------ | ------   | runtime  |
| test     | test     | ------ | ------   | test     |
| provided | provided | ------ | provided | provided |
| runtime  | runtime  | ------ | ------   | runtime  |

#### 依赖传递问题

当依赖层级很深的时候，可能造成循环依赖(cyclic dependency)

当依赖的数量很多的时候，依赖树会非常大

### 依赖调节原则

`A--->B--->C(V1)` ：项目A依赖于项目B，项目B依赖于项目C 

 `A--->D--->E--->C(V2)` ：项目A依赖于项目D，项目D依赖于项目E,项目E依赖于C

项目A隐形依赖了两个版本的C，那到底采用哪个版本呢？

分析：

- 依赖调解第一原则：**路径优先**，很明显，第一种路径深度是3，第二种路径深度是4，所以，maven会采用C（V1）

- 依赖调解第二原则：**声明优先**，假设路径深度相等，那么声明在前的会被引用。

### 版本锁定

在 Maven 中 dependencyManagement 的作用其实相当于一个对所依赖 jar 包进行版本管理的管理器。

pom.xml 文件中，jar 包的版本判断的两种途径：

- 如果 dependencies 里的 dependency 自己没有声明 version 元素，那么 maven 就会到 dependencyManagement 里面去找有没有对该 artifactId 和 groupId 进行过版本声明，如果有就继承它，如果没有就会报错，告诉你必须为 dependency 声明一个 version 。

- 如果 dependencies 中的 dependency 声明了 version，那么无论 dependencyManagement 中有无对该 jar 包的version 声明，都以 dependency 里的 version 为准。

### 排除依赖

我们仔细观察 Maven Dependencies 下的 jar 包，会发现存在了两个 javassist 包，一个是 javassist-3.18.1-GA ，另一个是 javassist-3.11.0-GA  。这是因为我们引入三大框架的jar包，hibernate 依赖 javassist-3.18.1-GA  ，而 struts 依赖     javassist-3.11.0-GA 。这就是我们通常所说的 jar 包版本冲突，如果这两个 jar 包同时存在，会导致后续某些操作会存在问题（比如 openSessionInView 失效），所以需要排除低版本的jar包。

如何来排除依赖呢？添加 exclusions 排除struts中依赖的 javassist jar 包。

```xml
<dependency>
  <groupId>org.apache.struts</groupId>
  <artifactId>struts2-core</artifactId>
  <version>2.3.24</version>
  <exclusions>
    <exclusion>
      <groupId>javassist</groupId>
      <artifactId>javassist</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

