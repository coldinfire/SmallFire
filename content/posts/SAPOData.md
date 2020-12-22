---
title: " SAP OData  "
date: 2019-05-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Sap Fiori

tags: 
  - sapui5

---

## OData(开放数据协议)

### 概述

1. 概述: OData 用于定义构建和使用 RESTful API 所需的最佳实践

   - OData 提供扩展功能，以满足 RESTful API 的任何自定义需求。
   - REST 代表 Representational State Transfer。
   - 它依赖于无状态，客户端 - 服务器，可缓存的通信协议。几乎在所有情况下，都使用 HTTP 协议。
   - REST 被定义为用于设计网络应用程序的体系结构样式。
   - OData 可帮助您在构建 RESTful API 时专注于业务逻辑，而无需担心定义请求和响应头，状态代码，HTTP 方法，URL 约定，媒体类型，有效负载格式和查询选项等的方法。
   - 用OData就是帮助你将焦点汇聚在业务逻辑的呈现上，而不用费心去考虑前台的展现层和后台业务逻辑层如何交互的细节。

2. OData服务生命周期

   OData 服务生命周期包括 OData 服务的范围。

   - 激活 OData 服务。
   - 维护 OData 服务。
   - 维护模型和服务，直至清理元数据缓存。
   - RESTful 应用程序使用 HTTP 请求发布数据以创建、更新、读取数据、删除数据。REST 对所有四个 CRUD（创建 / 读取 / 更新 / 删除）操作使用 HTTP。
   - REST 是 RPC（远程过程调用）和 Web 服务等机制的轻量级替代方法。

3. OData设置

   在manifest.json中配置服务器：

   ```JS
   "sap.app": { ... "ach": "CA-UI5-DOC", 
       "dataSources": {
           "invoiceRemote": { 
               "uri": "https://services.odata.org/V4/Northwind/Northwind.svc/",   
               "type": "OData",     
               "settings": {      
   			 "odataVersion": "2.0"    
               } 
           } 
       } 
       "sap.ui5": { ... "models": {
           ...   "invoice": {
               "dataSource": "invoiceRemote"   
           } 
       }
   }
   ```

### SAP对OData接口的实现

#### SAP Gateway

- SAP Gateway是一天基于ABAP开发的OData服务器端组件
- SAP Gateway可将ABAP数据字典元素转为OData实体，通过图形化的建模工具创建OData服务
- SAP Gateway将用户对不同实体的不同调用转换为调用不同的ABAP方法，有用户实现具体的调用代码
- SAP Gateway可以与ABAP中已经存在的相关服务进行集成，重用已有的表，视图，函数等对象

#### HANA XS Engine

- HANA XS Engine具有将HANA中的表、信息模型开放为OData服务的能力
- 使用声明式的语法，快速声明OData服务
- 在处理数据更新逻辑时，允许通过存储过程或XSJS函数处理用户输入的数据