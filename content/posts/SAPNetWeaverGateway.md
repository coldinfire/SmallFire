---
title: " SAP NetWeaver Gateway  "
date: 2019-05-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - sapui5

---

## SAP NetWeaver Gateway

### SAP NetWeaver 概述

#### SAP Gateway 框架

![SAP Gateway框架](/images/HANA/FioriGateway1.png)

SAP NetWeaver Gateway 是一种技术，它提供了一种基于市场标准将设备，环境和平台连接到 SAP 软件的简单方法。可以将SAP Gateway理解为是SAP将OData技术产品化的方式。

SAP Gateway提供了以下的能力：

- 支持任何设备，任何平台
- 支持多个对象的聚合数据访问
- 支持基于客户端应用程序的数据筛选
- 生成结构
- CRUD（Create, Read, Update, Delete）操作
- 易于开发简单的API,不需要任何工具知识
- 不需要SAP知识
- 支持快速建立原型
- 基于REST,OData。允许使用功能任何编程语言或模型连接到SAP应用程序
- 开发者可以基于现有的SAP BW query，BAPI，RFC，Web Dynpro屏幕，创建新的SAP Gateway对象

### 将SAP NetWeaver Gateway 连接到 SAP Business Suite

1) 将后端服务器配置为信任系统 : SM59

![1559703243928](/images/SAPUI5/1559703243928.png)

![1559712968906](/images/SAPUI5/1559712968906.png)    

2) SMT1

![1559713194355](/images/SAPUI5/1559713194355.png)

### SAP NetWeaver Gateway部署选项

#### 1) 中央枢纽部署 : 后端系统的开发

​	在此类部署选项中，中央 UI 附加组件，特定于产品的 UI 附加组件和 SAP NetWeaver 网关包含在 ABAP 前端服务器中。后端服务器包含业务逻辑和后端数据。开发在 ABAP 后端系统中进行。

- 它需要单独的 SAP NetWeaver Gateway 系统
- 它允许在没有后端开发授权的情况下更改 UI。
- 它为所有 UI 问题提供单点维护。
- 它为 Fiori Apps 的主题和品牌提供了中心位置。
- 它提供对后端系统的单点访问。
- 由于无法直接访问后端系统，因此增强了安全性。
- 直接本地访问元数据（DDIC）和业务数据以及轻松重用数据。

#### 2) 中央集线器的部署

​	如果必须在后端系统上执行开发，或者在 7.40 之前的版本中执行开发，则使用此选项。如果不允许在**后端**部署 Add-On **IW_BEP**。在这种情况下，开发人员仅限于可通过后端 RFC 访问的接口。

​	开发在 Gateway 集线器系统中进行，并且不触及 Business Suite 后端系统。

- 无法直接访问 **元数据（DDIC）** 和业务数据。因此，数据的重用是有限的。
- 无法远程使用 GENIL 对象。
- 在此配置中，访问仅限于远程启用的接口，如 RFC 模块，BAPI 等。

### SAP Gateway 建模

![SAP Gateway建模](/images/HANA/FioriGateway2.png)

- 通过导入ABAP数据字典对象，可以方便的进行OData实体创建
- 通过图形化的界面创建实体和实体之间的关系
- TCODE:SEGW

### SAP Gateway 业务逻辑的实现

![SAP Gateway业务逻辑](/images/HANA/FioriGateway3.png)



#### SAP Gateway 集成已有的数据源

通过与数据源映射(Map to Data Source)，可以由系统自动生成实现OData服务的代码

可实现映射的对象包括：

- 数据字典中的透明表和数据库视图
- 搜索帮助定义(仅查询)
- RFC
- **CDS视图(仅查询)**
- **BW EasyQuery(仅查询)**

#### SAP Gateway 服务维护

- 在Gateway HU系统中运行

- 服务的激活、状态查询、测试

- TCODE:/IWFND/MAINT_SERVICE

#### 使用Gateway Client进行测试

- 使用相对路径访问服务
- 便于测试更新、创建操作
- 可查看HTTP头信息
- 可存储测试样例以供复用