---
title: " SAP HANA 结构分析 "
date: 2019-05-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - HANA

---

### HANA 结构

经典应用程序用例中常见的一些重要的 SAP HANA 数据库服务。

![HAVA Architecture](/images/HANA/HANA_DB_Architecture.png)

在内部，SAP HANA 服务器使用各种组件来处理传入请求并返回结果集。 这些组件分为服务器和引擎，服务器是在操作系统级别运行的进程和服务，引擎是服务器内处理特定查询请求的组件。

#### 组成结构

- Presentation Layer :SAPGUI -> Browser(BSP/Webdynpro) -> SAP Fiori (UX) -> Web application


- Application layer : NW Foundation  (NW7.4->NW7.53)


- DB layer : HANA DB* (Code-to-data)


- OData : HANA XS OData服务，通过声明式的代码，将HANA表或信息模型开放成OData实体



### Platform Capabilities

![HANA_Platform_Capabilities](/images/HANA/HANA_Platform_Capabilities.png)



