---
title: "Maven构建生命周期"
date: 2018-03-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - Maven

---

### 项目生命周期

Maven有三套相互独立的生命周期，分别是：clean、default、site。

- clean 主要是清理项目、

- default 是Maven最核心的的构建项目

- site 是生成项目站点。

每一个大的生命周期又分为很多个阶段。后面的阶段依赖于前面的阶段，这点有点像Ant的构建依赖。生命周期本身相互独立，用户可以仅仅调用生命周期的某一个阶段，也就是说用户调用了 default 周期的任何阶段，并不会触发 clean 周期以及 site 周期的任何事情。

三大生命周期蕴含着小小的阶段，我们按顺序看一下：

#### clean周期

pre-clean：准备清理

clean：真正的清理工作

post-clean：执行清理后的一些后续工作

#### default周期：

validate：验证

initialize：初始化配置

generate-sources：生成源代码编译目录

process-sources：处理项目主资源文件，复制资源文件到outputclasspath

generate-resources：生成资源目录

process-resources：处理资源文件

complie：编译源代码

process-classes：处理编译后文件

generate-test-sources：生成测试目录

process-test-sources：处理项目测试资源文件，复制测试资源文件到outputclasspath

generate-test-resources：生成测试资源文件

process-test-resources：处理测试资源文件

test-compile：编译测试代码

process-test-classes:处理测试代码

test：单元测试运行测试代码

prepare-package：打包前的准备

package：将编译好的代码打包成为jar或者war或者ear等等

pre-integration-test：准备整体测试

integration-test：整体测试

post-integration-test：为整体测试收尾

verify：验证

install：安装到本地Maven库

deploy：将最终包部署到远程Maven仓库

#### site周期

pre-site：准备生成站点

site：生成站点及文档

post-site：站点收尾

site-deploy：将生成的站点发布到服务器上