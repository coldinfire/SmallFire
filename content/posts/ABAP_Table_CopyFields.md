---
title: " 复制其他的内表/结构到当前表/结构 "
date: 2018-09-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

有时需要创建一个结构，但是字段和其他表或则结构类似，就可以采用复制的方式快速创建，而不用一个一个字段去输入。

#### 1.SE11选择需要添加字段的表/结构

输入表或则结构的名称，点击修改。

![查询数据](/images/ABAP/copyfield1.png)

#### 2.选择Edit列，并点击选项Transfer Field

![按钮选择](/images/ABAP/copyfield2.png)

#### 3.在弹出框输入需要Copy字段的表/结构

![输入目标数据](/images/ABAP/copyfield3.png)

可以选择`Copy All`或则选择`Selection`，Copy all则复制所有的字段数据，选择Selection则会跳入以下界面,选择需要复制的字段：

![选择需要复制的字段](/images/ABAP/copyfield4.png)

#### 4.选中Component列，点击Paste按钮进行复制字段

![复制字段](/images/ABAP/copyfield5.png)

#### 5.激活

