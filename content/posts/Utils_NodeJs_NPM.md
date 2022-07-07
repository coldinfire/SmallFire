---
title: " NPM 使用 "
date: 2018-02-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - NodeJs

---

### NPM 使用介绍

NPM 是随同 NodeJS 一起安装的标准的软件包管理工具，能解决 NodeJS 代码部署上的很多问题，常见的使用场景有以下几种：

- 允许用户从 NPM 服务器下载别人编写的第三方包到本地使用。
- 允许用户从 NPM 服务器下载并安装别人编写的命令行程序到本地使用。
- 允许用户将自己编写的包或命令行程序上传到 NPM 服务器供别人使用。

由于新版的 nodejs 已经集成了 npm，所以之前npm也一并安装好了。同样可以通过输入 `npm -v` 来测试是否成功安装。

- npm 可以管理项目依赖的下载。

### NPM 安装包管理

| Command                   | Description                                                  |
| :------------------------ | :----------------------------------------------------------- |
| npm help Command          | 可以查看某条命令的详细帮助                                   |
| npm install npm -g        | Windows系统升级命令                                          |
| npm install ModuleName    | 安装模块命令，本地安装                                       |
| npm install ModuleName -g | 安装模块命令，全局安装                                       |
| npm list -g               | 查看所有全局安装的模块                                       |
| npm list ModuleName       | 查看某个模块的版本号                                         |
| npm uninstall ModuleName  | 卸载 Node.js 模块                                            |
| npm update ModuleName     | 把当前目录下 node_modules 子目录里边的对应模块更新至最新版本 |
| npm update ModuleName -g  | 把全局安装的对应模块更新至最新版                             |
| npm ls                    | 查看 /node_modules/ 目录下的包                               |
| npm search ModuleName     | 搜索模块                                                     |
| npm cache clear           | 清空 NPM 本地缓存                                            |

#### 本地安装

- 将安装包放在 ./node_modules 下（运行 npm 命令时所在的目录），如果没有 node_modules 目录，会在当前执行 npm 命令的目录下生成 node_modules 目录。
- 可以通过 require() 来引入本地安装的包，无需指定第三方包路径。

#### 全局安装

- 将安装包放在 /usr/local 下或者你 node 的安装目录。
- 可以直接在命令行里使用。

#### 版本号

使用NPM下载和发布代码时都会接触到版本号。NPM 使用语义版本号来管理代码，这里简单介绍一下。

语义版本号分为 X.Y.Z 三位，分别代表主版本号、次版本号和补丁版本号。当代码变更时，版本号按以下原则更新。

- 如果只是修复 bug，需要更新 Z 位。
- 如果是新增了功能，但是向下兼容，需要更新 Y 位。
- 如果有大变动，向下不兼容，需要更新 X 位。

版本号有了这个保证后，在申明第三方包依赖时，除了可依赖于一个固定版本号外，还可依赖于某个范围的版本号。例如"argv": "0.0.x"表示依赖于 0.0.x 系列的最新版 argv。

#### 使用 package.json

package.json 位于模块的目录下，用于定义包的属性。

| Attribute    | Description | Attribute    | Description                                                  |
| :----------- | :---------- | :----------- | :----------------------------------------------------------- |
| name         | 包名        | contributors | 包的其他贡献者                                               |
| version      | 包的版本号  | author       | 包的作者                                                     |
| desciption   | 包的描述    | homepage     | 包的官网 url                                                 |
| keywords     | 关键字      | repository   | 包代码存放的位置                                             |
| dependencies | 依赖包列表  | main         | main 字段指定了程序的主入口文件。require('moduleName') 就会加载这个文件。这个字段的默认值是模块根目录下面的 index.js。 |

#### 安装所有依赖

如果项目具有 `package.json` 文件，则通过运行：`npm install` 它会在 `node_modules` 文件夹（如果尚不存在则会创建）中安装项目所需的所有东西。