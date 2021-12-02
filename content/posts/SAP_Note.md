---
title: " SAP Note 使用 "
date: 2021-09-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - utils

---

### 为什么使用 SAP Note

SAP Note 对产品代码或文档中出现的问题进行了澄清。 可以使用它们来找出如何解决影响特定版本的问题。

![SAP Note](/images/SAPUtils/SAP_NOTE_0.png)

#### 什么时候使用

- 在考虑是否升级系统时。 查找产品版本的 SAP Note，以便您可以计划影响它的任何问题。

- 升级系统后，您会看到新的错误消息。 查找带有错误消息的 SAP Note 以了解如何解决该问题。

- 即使在阅读了文档后，您仍然坚持执行特定任务。 查找产品的 SAP Note，其中可能包含文档中缺少的信息。

- 您只是不确定某个功能是否正常工作。 使用功能关键字查找 SAP Note，以查找有关报告问题以及如何解决这些问题的任何信息。

### 查询系统Note

想要知道某个 Note 是否已经应用。 我们要如何检查？

使用事物码 SNOTE --> GOTO --> SAP note browser 

![SNOTE](/images/SAPUtils/SAP_NOTE_1.png)

在选择界面中输入 SAP Note Number。

![Note Browser](/images/SAPUtils/SAP_NOTE_2.png)

如果 note 已经在系统中应用，那么该 note 的状态应该是 Finished，Implementation Status 应为 Completely implemented。

![Note Status](/images/SAPUtils/SAP_NOTE_3.png)

- Can be implemented
- Cannot be implemented
- **Completely implemented**
- Incompletely implemented
- Obsolete
- Obsolete version implemented
- Undefined Implementation State

### 下载 Note

注意：要下载 SAP Note，必须创建到 SAP 的 RFC 连接。

#### 操作步骤

使用事物码 SNOTE，选择 Download SAP Note。

![Note Download](/images/SAPUtils/SAP_NOTE_4.png)

指定要下载的 SAP Notes 的编号，可以使用选择功能加载一个或多个 SAP Note。

![Note Download](/images/SAPUtils/SAP_NOTE_5.png)

确认选择。系统使用 RFC 连接将 SAP 注释加载到您的数据库中。

![Note Download](/images/SAPUtils/SAP_NOTE_6.png)

#### SAP Note 下载具有以下优点

- 可以使用 note 助手通过与 SAP 的 RFC 连接将 SAP Note 直接加载到系统中。
- 如果其他 SAP Note 作为前提条件输入到 SAP Note 中，Note 助手会在实施过程中自动下载前提 SAP Note。
- 只需按一下按钮即可下载 SAP Notes 的更新版本（下载 SAP Note 的当前版本）。

如果下载后 note number 中出现播放符号，则表示尚未实现或可以实现。 否则它已经实施或无法实施。

### SAP Note 上传

上传 SAP Note 不需要与 SAP 建立永久的 RFC 连接。 因此需要先从 SAP Service Marketplace 下载所需的 SAP Note，并将其本地保存在您的前端 PC 上。 然后使用注释助手上传 SAP 注释。查看更正说明并检查该特定代码是否已存在于指定的 include 或 function module 中。

#### 操作步骤

在 service.sap.com/notes 下的 SAP Service Marketplace 中选择正确的 SAP Note。

![Note Download](/images/SAPUtils/SAP_NOTE_7.png)

选择 "Download for SNOTE"。启动 SAP 下载管理器。 要将 SAP Note 保存在您的 PC 本地，请选择“下载”。SAP Note 作为文件加载到您指定的目录中。 

![Note Download](/images/SAPUtils/SAP_NOTE_8.png)

![Note Download](/images/SAPUtils/SAP_NOTE_10.png)

使用 Note Assistant 中的上传功能 Goto --> Upload SAP Note 将 SAP Note 文件加载到您的系统中。

![Note Upload](/images/SAPUtils/SAP_NOTE_9.png)

