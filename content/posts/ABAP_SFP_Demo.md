---
title: " SAP Adobe Form Demo "
date: 2020-04-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  ABAP

tags: 
  - abaputils

---

### 创建 Interface

进入事务代码：SFP，输入 Interface 名字，点击创建。

![SFP Interface](/images/ABAP/ABAP_SFP0.png)

在接口中添加自定义参数

![Interface](/images/ABAP/ABAP_SFP1.png)

### 创建 Form

进入事务代码：SFP，在 Form 中输入要创建的 form 名字，点击创建，在弹出框中输入之前创建的 Interface。

![SFP Form](/images/ABAP/ABAP_SFP2.png)

在 Context tab 中，我们需要把输入的参数加入到 context 中，这个就是一个拖拽的动作。