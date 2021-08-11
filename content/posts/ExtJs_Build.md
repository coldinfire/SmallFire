---
title: "Extjs 应用程序创建"
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
  - **ext.js** ：已压缩 js 代码；基本框架，动态加载扩展类。
  - **ext-all.js** ：已压缩 js 代码；包括 ExtJS 整个框架。
  - **ext-debug.js** ：未压缩 js 代码；基本框架，动态加载扩展类。
  - **ext-all-debug.js** ：未压缩 js 代码；包括 ExtJS 整个 debug 框架。
  - **ext-all-rtl.js** ：已压缩 js 代码；包括 ExtJS 整个框架并支持从右到左阅读。
  - **ext-all-rtl-debug.js** ：未压缩 js 代码；包括 ExtJS 整个框架并支持从右到左阅读。
  - **ext-all-rtl-sandbox.js** ：已压缩 js 代码；包括 ExtJS 整个框架、支持从右到左阅读以及支持以沙盒方式使用其他版本的 ExtJS。
  - **ext-all-rtl-sandbox-debug.js** ：未压缩 js 代码；包括 ExtJS 整个框架、支持从右到左阅读以及支持以沙盒方式使用其他版本的 ExtJS。
  - **ext-all-sandbox.js** ：已压缩 js 代码；包括 ExtJS 整个框架并支持以沙盒方式使用其他版本的 ExtJS。
  - **ext-all-sandbox-debug.js** ：未压缩 js 代码；包括 ExtJS 整个框架并支持以沙盒方式使用其他版本的 ExtJS。

#### 下载安装  Sencha Cmd

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

- `sencha -sdk [解压后的ext sdk目录的位置] generate app [项目名称] [项目地址]`

  ![Sencha Cmd APP](/images/EXTJS/extjs_app_1.png)

Step3：运行应用程序

- 打开文件夹位置 `cd [项目地址]`；运行应用程序：`sencha app watch` ；Sencha 命令将构建应用程序。完成后，命令窗口将记录以下消息：

  ![Sencha Cmd APP](/images/EXTJS/extjs_app_2.png)

Step4：在浏览器中共运行应用程序

- 打开任何浏览器窗口，输入访问地址`http://localhost:1841/`；如果执行了多个 extjs 项目，端口会随机变化。

![Sencha Cmd APP](/images/EXTJS/extjs_app_3.png)

Step5：编译应用程序

- 使用 `sencha app build` 会在项目文件夹中的 build 文件下生成最终的 Extjs  项目代码，文件大小较小。

  ![Sencha Cmd APP](/images/EXTJS/extjs_app_5.png)

- Extjs 项目代码

  ![Sencha Cmd APP](/images/EXTJS/extjs_app_4.png)

#### Cmd 生成的应用程序的结构

ExtJS 应用程序遵循统一的目录结构，每个应用程序都相同。在我们推荐的布局中，所有 Store、Model、ViewModel 和 ViewController 类都放在 app 文件夹中（Models 放在 model 文件夹中，Stores 放在 store 文件夹中 ，ViewModels/ViewControllers 放在 view 文件夹中）。

最佳实践是：使用类似的命名结构将ViewControllers和 ViewModels 逻辑地组合在文件夹内的子文件`app/view/`夹中，您将使用该命名结构本身（请参阅“app/view/main/”和“classic/src/view /main/”文件夹）。



- **appname**目录：应用程序的根目录。

- **app**目录：业务代码。

- **extjs**目录：存放ExtJS各JS文件。

- **resources**目录：资源目录；包含CSS和图片。

- **app.js**文件：应用程序的逻辑js文件。

- **index.html**文件：应用程序的入口点。

可以看出，整个目录只有一个 html 文件，其他的业务都是通过 js 文件创建。

ExtJS 非常适合 SAP(单页面应用) 的程序，除了入口的 index.html ，其他业务都可通过 js 文件来进行开发和管理。

### Eclipse 配置 ExtJS

#### 项目构建

将 Ext 开发包 copy 到 eclipse 项目的 /WebRoot 目录下：

不需要整个 Ext 开发包全部导入，这样很容易造成 eclipse 卡死，因为 eclipse 会自动检测 js 的合法性，会占用大量的检测时间、cpu和内存。通常普通的开发只需要用到 \extjs-4.2.1\resources 文件包、build\ext-all.js 这两个资源就可以，需要中文化再导入 \locale\ext-lang-zh_CN.js 就可以了。

#### 在web页面中通过 script 标签引入 ext 的库文件(注意引入顺序)

```html
<link rel="stylesheet" type="text/css" href="extjs-4.2.1/resources/css/ext-all.css">
// 引入样式文件 
<script type="text/javascript" src="extjs-4.2.1/bootstrap.js"></script>
<script type="text/javascript" src="ext-4.2.1/locale/ext-lang-zh_CN.js"></script>// 中文化 
```

### (4) 调用Ext.onReady()初始化组件

   

```javascript
 <script>
   Ext.onReady(function(){
    …///在这里面创建及使用ext控件
    });
</script>
```

