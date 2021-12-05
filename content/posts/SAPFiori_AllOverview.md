---
title: " Fiori 简介 "
date: 2019-04-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  SAP Fiori

tags: 
  - sapui5

---

### 框架设计 ###

![SAP Fiori架构概览](/images/HANA/Fiori1.png)

SAP Fiori UI5有五种设计原则。这些原则使SAP Fiori变得简单，并将不同的事务分解为基于任务的简单UI应用程序。

  - 基于角色 - SAP已经分解了各种SAP事务并将其更改为漂亮的用户交互式应用程序，这些应用程序仅向用户显示最相关的信息。

  - 响应能力 - 当SAP Fiori与SAP HANA的强大功能相结合时，它提供了无与伦比的应用程序响应和查询执行时间。

  - 简单 - 为了使SAP Fiori易于满足用户需求，SAP将其设计为1-1-3方案。这意味着1个用户，1个用例和3个屏幕。

  - 无缝体验 - SAP提供了基于相同语言的所有Fiori应用程序，并且在部署和平台上无关紧要。

  - 令人愉快 - SAP Fiori旨在与ECC 6.0协同工作，使用户可以轻松地在现有SAP系统上进行部署。

### Fiori Launch Pad

![Fiori Launch Pad](/images/HANA/FioriLaunchPad.png)

关于 SAP Fiori Launch Pad 的要点如下:

- 基于 Web 的入口点，可跨平台和设备使用 SAP Business 应用程序。
- 作为HTML 客户端的开箱即用思想提供。
- 使用主题，搜索集成，自定义等功能为最终用户提供高生产率。
- 为使用多种设备类型的最终用户提供单一入口点。

### SAP Fiori应用程序分类。

![Fiori App Type](/images/HANA/FioriAppType.png)

它们的功能和基础设施要求非常突出。

   - 交易应用
   - 实况报道
   - 分析应用程序

交易应用程序最重要的功能是 ：

   - SAP Fiori的第一个版本包括25个交易应用程序。
   - SAP Fiori中的交易应用程序用于执行交易任务，例如经理 - 员工交易，例如请假请求，旅行请求等。
   -  事务性应用程序在SAP HANA数据库上运行最佳，但可以与任何性能可接受的数据库一起部署。这些应用程序允许用户在移动设备以及台式机或笔记本电脑上运行简单的SAP事务。

  实况报道的重要特征如下：

   - 实况表用于在业务操作中钻取关键信息和上下文信息。
   - 它还允许您将一个事实表导航到其所有相关的情况说明书。

   - 情况说明书还允许您导航到事务性应用程序以运行SAP事务。一些情况说明书还提供了地理地图的集成选项。

   - 您可以从Fiori Launchpad搜索结果，其他情况说明书或交易或分析应用程序中调用Fact Sheet。

   - 情况说明书仅在SAP HANA数据库上运行，并且还需要ABAP堆栈，并且无法将其移植到SAP HANA Live第2层架构。

   分析应用程序用于

   -  提供有关业务操作的基于角色的实时信息。分析应用程序将SAP HANA的强大功能与SAP业务套件相集成。它从前端Web浏览器中的大量数据中提供实时信息。

#### Fiori事务程序：组成部分

- 前台程序：SAPUI5程序，可使用**SE80**在BSP Application中找到

- 后台服务：使用SAP Gateway创建的OData服务，使用**SEGW**找到
- LPD对象：通过**LPD_CUST** 进入，该配置信息将Fiori程序从ABAP系统开放给Fiori Launch Pad
- 技术目录：预先在Fiori Launch Pad 中配置好的运行此程序的Catalog
- 技术角色：预先配置的具有该App菜单的角色

### Fiori采用的技术汇总

![Fiori Tech](/images/HANA/FioriTech.png)

学习SAP Fiori的需要了解的技术

-  ABAP程序和对象


- HTML5


- JavaScript
- SAP UI5
- ERP实施经验
- OData和SAP NetWeaver网关
- SAP HANA