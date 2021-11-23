---
title: " Maven 构建生命周期 "
date: 2017-12-27
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - Maven

---

## Maven指令的生命周期

Maven 对项目构建过程分为三套相互独立的生命周期，分别是：

- Clean Lifecycle：在进行真正的构建之前进行一些清理工作

- Default Lifecycle：构建项目过程，是 Maven 最核心的

- Site Lifecycle：生成项目报告，站点，发布站点

每一个大的生命周期又分为很多个阶段。后面的阶段依赖于前面的阶段，这点有点像 Ant 的构建依赖。每一个构建项目的命令都对应了 maven 底层一个插件。

生命周期本身相互独立，用户可以仅仅调用生命周期的某一个阶段，也就是说用户调用了 default 周期的任何阶段，并不会触发 clean 周期以及 site 周期的任何事情。

三大生命周期蕴含着小小的阶段，我们按顺序看一下：

### clean 生命周期

pre-clean：执行一些清理前需要完成的工作

`clean`：清理上一次构建生成的文件

post-clean：执行一些清理后需要完成的工作

### default 生命周期

validate：验证项目是否正确且所有必须信息是可用的

initialize：初始化配置

generate-sources：生成源代码编译目录

process-sources：处理项目主资源文件。一般来说，是对 src/main/resources 目录的内容进行变量替换等工作后，复制到项目输出的主 classpath 目录中

generate-resources：生成资源目录

process-resources：复制和处理资源文件到目标目录，为打包阶段做好准备

`complie`：编译项目的主源码。一般来说，是编译 src/main/java 目录下的 Java 文件至项目输出的主 classpath 目录中

process-classes：处理编译后文件

generate-test-sources：生成测试目录

process-test-sources：处理项目测试资源文件。一般来说，是对 src/test/resources 目录的内容进行变量替换等工作后，复制到项目输出的测试 classpath 目录中

generate-test-resources：生成测试资源文件

process-test-resources：处理测试资源文件，复制和处理测试资源到目标文件

test-compile：编译项目的测试代码。一般来说，是编译 src/test/java 目录下的 Java 文件至项目输出的测试 classpath 目录中

process-test-classes：处理测试源码编译生成的文件

`test`：使用单元测试框架(JUnit)运行测试代码，测试代码不会被打包或部署

prepare-package：打包前的准备

`package`：将编译好的代码打包成为 jar 或者 war 等

pre-integration-test：集成测试前准备

integration-test：集成测试

post-integration-test：为集成测试收尾

verify：验证，对集成测试的结果进行检查，保证质量达标

`install`：安装打包的项目到 Maven 本地仓库，供本地其他项目使用

`deploy`：将最终的工程包部署到远程 Maven 仓库，供其他开发人员和 Maven 项目使用

### site 生命周期

pre-site：执行一些在生成项目站点之前需要完成的工作

site：生成项目站点及文档

post-site：执行一些在生成项目站点之后需要完成的工作，并且为部署做准备

site-deploy：将生成的项目站点发布到服务器上

### 命令行与生命周期

**$mvn clean** ：该命令调用 clean 生命周期的 clean 阶段。实际执行的阶段为 clean 生命周期的 pre-clean 和 clean 阶段

**$mvn test** ：该命令调用 default 生命周期的 test 阶段，实际执行的阶段为 default 周期的 validate、initialize 等，直到 test 的所有阶段

**$mvn clean install** ：该命令调用 clean 生命周期的 clean 阶段和 default 生命周期的 install 阶段。实际执行的阶段为 clean 生命周期的 pre-clean、clean 阶段，以及 default 生命周期的从 validate 至 install 的所有阶段。结合了两个生命周期，在执行真正的项目构建之前清理项目是一个很好的实践

**$mvn clean deploy site-deploy** ：该命令调用 clean 生命周期的 clean 阶段、default 生命周期的 deploy 阶段，以及 site 生命周期的 site-deploy 阶段。实际执行的阶段为 clean 生命周期的 pre-clean、clean 阶段，default 生命周期的所有阶段，以及 site 生命周期的所有阶段。该命令结合了 Maven 所有的三个生命周期，且 deploy 为 default 生命周期的最后一个阶段，site-deploy 为 site 生命周期的最后一个阶段。

由于 Maven 中主要的生命周期阶段并不多，而常用的 Maven 命令实际都是基于这些阶段简单组合而成。