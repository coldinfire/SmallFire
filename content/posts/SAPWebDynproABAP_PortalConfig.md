---
title: " 将开发好的Web Dynpro 挂载到Portal上 "
date: 2018-10-29
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

### Web dynpro create role 

在浏览器中登陆Portal，选择Content Administration页签，然后选择Portal Content 下面需要使用的文件夹。

选择文件夹，点击鼠标右键 New -> Role -> Workcenter Role。

![Web Dynpro Role](/images/webdynpro/webdynproABAP/Portal1.png)

进去后输入Role名称，点击完成，则Role就创建成功了： **Role 名称就是以后单独显示标签的Text** 。

![Web Dynpro Role](/images/webdynpro/webdynproABAP/Portal2.png)

### Create iView

选中文件夹，右键点击New -> iView -> iView from Template。

![Web Dynpro iView](/images/webdynpro/webdynproABAP/Portal3.png)

然后选择SAP Web Dynpro
for ABAP iView, 然后选择Next。

![Web Dynpro iView](/images/webdynpro/webdynproABAP/Portal4.png)

写好文件名，这里切记 **iView ID不要填写，之前填写就会出错，不能保存成功** 选择Next。

![Web Dynpro iView](/images/webdynpro/webdynproABAP/Portal5.png)

接下来对Web Dynpro for ABAP Parameters进行设置。

![Web Dynpro iView](/images/webdynpro/webdynproABAP/Portal6.png)

点击Next，则iView创建成功。

**ADD User parameter in iView 
：userid=<User.LogonUid>** 

选中iView，右键选择Open -> Properties。

![Web Dynpro Properties](/images/webdynpro/webdynproABAP/Portal8.png)

选择Properties -> All -> Application Parameters 维护用户ID参数并保存。

![Web Dynpro Properties](/images/webdynpro/webdynproABAP/Portal7.png)

### Add iView to Role

这里注意，首先要点击role，然后把它打开，然后再右键iView,才能有add as iView的选项。如果这样还没有则可能是有锁住，要进行解锁才行。

打开Role后，然后选中需要添加的iView,右键选择add as iView的选项,选择Delta Link。

![Add iView to Role](/images/webdynpro/webdynproABAP/Portal9.png)

此时iView成功加到Role中了,可以在右侧打开的Role的Views看到已添加的View。

#### **解锁方式**

当选择Role打开有报错信息：`Read-only mode. Object is currently locked by user`，则表示Object锁住了，需要解锁。

选择System
Administration -> Monitoring,选中锁住的对象，然后点击Unlock即可。

![Unlock Role](/images/webdynpro/webdynproABAP/Portal10.png)

### Create Transport Package

选中文件夹，右键New -> Transport
Package来创建。

![Create Transport Package](/images/webdynpro/webdynproABAP/Portal11.png)

输入Transport package名称，然后点击Next。

![Create Transport Package](/images/webdynpro/webdynproABAP/Portal12.png)

Transport
Package创建成功

![Create Transport Package](/images/webdynpro/webdynproABAP/Portal13.png)

### Add iView and Role to Transport package

选中 **Transport package** ,然后右键Open -> Package
Content.

![Create Transport Package](/images/webdynpro/webdynproABAP/Portal14.png)

然后将iView和Role从左边拖进去即可.

![Create Transport Package](/images/webdynpro/webdynproABAP/Portal14_1.png)

#### Preview

接下来在User Administration Tab将Role分配给用户， 用户就可以看到Web Dynpro挂在Portal上了。

![Create Transport Package](/images/webdynpro/webdynproABAP/Portal14_2.png)

