---
title: " Extjs 应用程序创建 "
date: 2021-05-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - ExtJS

---

### 下载资源

ExtJS 提供 2 种使用协议：GPL3.0(免费) 和 商业协议(付费)。

这里使用 GPL 版本的 SDK。

#### 下载  Ext JS GPL SDK

下载地址：[https://www.sencha.com/legal/gpl/](https://www.sencha.com/legal/gpl/)

Step1：输入信息，重要的是邮箱地址，然后点击 Download SDK。下载的链接会发送到刚刚填写的邮箱。

![ext-X.X.X-gpl.zip](/images/EXTJS/extjs_download_1.png)

Step2：登录邮箱，会看到 Sencha 团队发的邮件，邮件的底部有个 `Download Ext JS GPL` 按钮，点击就会开始 ext-X.X.X-gpl.zip 的下载。

![ext-X.X.X-gpl.zip](/images/EXTJS/extjs_download_2.png)

Step3：将下载的文件解压

![ext-X.X.X-gpl.zip](/images/EXTJS/extjs_download_3.png)

- build 文件夹

  ![Zip build](/images/EXTJS/extjs_dirs_1.png)

  - **ext-bootstrap.js** ：引导文件，根据当前运行环境自动加载 ext-all.js 或者 ext-all-dev.js 文件。
  - **ext.js** ：已压缩 js 代码；核心文件，其中包含运行应用程序的所有功能。
  - **ext-all.js** ：已压缩 js 代码；此文件包含在文件中没有注释的所有缩小的代码。
  - **ext-debug.js** ：未压缩 js 代码；基本框架，动态加载扩展类。
  - **ext-all-debug.js** ：无压缩的 Ext 全部的源码，用于调试。
  - **ext-all-rtl.js** ：已压缩 js 代码；包括 ExtJS 整个框架并支持从右到左阅读。
  - **ext-all-rtl-debug.js** ：未压缩 js 代码；包括 ExtJS 整个框架并支持从右到左阅读。
  - **ext-all-rtl-sandbox.js** ：已压缩 js 代码；包括 ExtJS 整个框架、支持从右到左阅读以及支持以沙盒方式使用其他版本的 ExtJS。
  - **ext-all-rtl-sandbox-debug.js** ：未压缩 js 代码；包括 ExtJS 整个框架、支持从右到左阅读以及支持以沙盒方式使用其他版本的 ExtJS。
  - **ext-all-sandbox.js** ：已压缩 js 代码；包括 ExtJS 整个框架并支持以沙盒方式使用其他版本的 ExtJS。
  - **ext-all-sandbox-debug.js** ：未压缩 js 代码；包括 ExtJS 整个框架并支持以沙盒方式使用其他版本的 ExtJS。

#### 下载安装  Sencha Cmd

Sencha CMD 是一个提供 Ext JS 代码缩小，脚手架，生产构建生成功能的工具。

下载地址：[https://www.sencha.com/products/extjs/cmd-download/](https://www.sencha.com/products/extjs/cmd-download/)

Step1：点击下载到本地

![Sencha Cmd](/images/EXTJS/extjs_download_4.png)

Step2：解压文件后，执行 exe 文件

- 点击 Next

  ![Sencha Cmd](/images/EXTJS/extjs_download_5.png)

- 选择 Accept the agreement，点击 Next
  ![Sencha Cmd](/images/EXTJS/extjs_download_6.png)

- 选择需要保存的文件夹路径

  ![Sencha Cmd](/images/EXTJS/extjs_download_7.png)

- 选择需要安装的组件

  ![Sencha Cmd](/images/EXTJS/extjs_download_8.png)

- 将安装目录添加到 path，这里选择 Yes；点击 Next 完成安装

  ![Sencha Cmd](/images/EXTJS/extjs_download_9.png)

Step3：验证是否安装成功，打开 cmd 输入 `sencha help` 查看是否安装成功

![Sencha Cmd](/images/EXTJS/extjs_download_10.png)

### 使用 Sencha Cmd 创建应用程序

Step1：选择目录

- 打开 cmd 选择需要创建项目的空目录；`cd <folder-to-create-the-app>`

Step2：创建应用程序

- `sencha -sdk [解压后的ext sdk目录的位置] generate app -classic [项目名称] [项目地址]`

  - -classic | -modern可以省略，Ext JS 的 6.0 版本其中一个主要的更新就是把 Ext JS 和 Touch 合并，在创建时可以选择哪一种类型，classic 对应 Ext JS，而 modern 对应 Touch。如果省略，则创建所谓的 universal 类型，即 classic 和 modern 并存。在本文中演示的是桌面端程序，所以我们选择 classic。
  
  ![Sencha Cmd APP](/images/EXTJS/extjs_app_1.png)

Step3：运行应用程序

- 打开文件夹位置 `cd [项目地址]`；运行应用程序：`sencha app watch` ；Sencha 命令将构建应用程序。完成后，命令窗口将记录以下消息：

  ![Sencha Cmd APP](/images/EXTJS/extjs_app_2.png)

Step4：在浏览器中共运行应用程序

- 打开任何浏览器窗口，输入访问地址`http://localhost:1841/`；如果执行了多个 extjs 项目，端口会随机变化。
- 修改端口：`sencha fs web -port 8020 start -map [项目地址]`
  - -port：定义访问端口，8020 设置端口为 8020
  - -map：指定 Web 的访问目录

![Sencha Cmd APP](/images/EXTJS/extjs_app_3.png)

Step5：编译应用程序

- 使用 `sencha app build` 会在项目文件夹中的 build 文件下生成最终的 Extjs  项目代码，文件大小较小。

  ![Sencha Cmd APP](/images/EXTJS/extjs_app_5.png)

- Extjs 项目代码

  ![Sencha Cmd APP](/images/EXTJS/extjs_app_4.png)

#### Cmd 生成的应用程序的结构

ExtJS 应用程序遵循统一的目录结构，每个应用程序都相同。在官方推荐的布局中，所有 Store、Model、ViewModel 和 ViewController 类都放在 app 文件夹中。使用类似的命名结构将 ViewControllers 和 ViewModels 逻辑地组合在文件夹内的子文件`app/view/`夹中。

- Models 放在 model 文件夹中
- Stores 放在 store 文件夹中 
- ViewModels/ViewControllers 放在 view 文件夹中

目录结构解析

![Sencha Cmd APP](/images/EXTJS/extjs_app_7.png)

- .sencha：在该文件夹下有app和workspace两个文件夹，主要包含一些生成应用程序所需的配置文件。一般情况下，不需要修改.sencha文件夹下的任何文件。
- app：业务代码文件。在该文件夹下有view、model、store三个文件夹，分别用来存放应用程序的控制器、模型、存储数据源组件和视图文件。
  - Application.js 文件：该文件是用来加载视图、控制器和存储的，在后续开发中随着视图、控制器和存储的添加，需要不断的修改该文件。
  - 在app\view文件夹下，已经生成了Viewport.js和Main.js两个主界面视图，后续的开发将从修改这两个文件开始。
- ext：Ext JS 的框架文件所在的文件夹。
- resources：存放应用程序的资源文件，包含 CSS 和 image。
-  sass文件夹：存放自定义的主题或样式文件。
- app.js：应用程序的启动文件。
- app.json：应用程序的配置文件。
- bootstrap.js：样式引导文件。代替直接加载 ext-all.css 或其他编译好的样式文件。该文件会在生成应用程序时被修改。
- bootstrap.json：用来保存应用程序中脚本和样式文件的路径，为 bootstrap.js 脚本和样式文件提供加载地址。
- build.xml：生成应用程序时需要使用到的配置文件。
- index.html：应用程序启动时需要使用到的页面文件。
  - 系统的首页，加载样式表、核心库、程序入口 app.js
- workspace.json：一个大的项目可能时由几个小项目组成的，而这些小项目会有一些共享代码，这时候就需要用到工作空间来解决共享代码的问题，本文件就是用来定义共享空间的。

可以看出，整个目录只有一个 html 文件，其他的业务都是通过 js 文件创建。ExtJS 非常适合 SAP(单页面应用) 的程序，除了入口的 index.html ，其他业务都可通过 js 文件来进行开发和管理。

### Eclipse 配置 ExtJS

#### 项目构建

将 Ext 开发包 copy 到 eclipse 项目的 /WebRoot 目录下：

不需要整个 Ext 开发包全部导入，这样很容易造成 eclipse 卡死，因为 eclipse 会自动检测 js 的合法性，会占用大量的检测时间、cpu和内存。在web项目的静态页面中，引入的文件：

- Extjs\ext-bootstrap.js

- Extjs\build\ext-all.js 或者 Extjs\build\ext-all-debug.js

- Extjs\build\classic\locale\locale-zh_CN.js

- Extjs\build\classic\theme-crisp\resources\theme-crisp-all.css 

#### 在web页面中通过 script 标签引入 ext 的库文件 (注意引入顺序)

```html
<link rel="stylesheet" type="text/css" href="extjs/resources/theme-classic-all.css">
// 引入样式文件 
<script type="text/javascript" src="extjs/ext-all.js"></script>
<script type="text/javascript" src="extjs/ext-bootstrap.js"></script>
<script type="text/javascript" src="extjs/resources/locale-zh_CN.js"></script>
<script type="text/javascript" src="extjs/resources/theme-crisp/resources/theme-crisp-all.css"></script>
```

#### 初始化组件

```html
<script>
  Ext.onReady(function(){
    ... ///在这里面创建及使用ext控件
  });
</script>
```

