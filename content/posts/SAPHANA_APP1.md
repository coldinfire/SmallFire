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

### 组成结构

- Presentation Layer :SAPGUI -> Browser(BSP/Webdynpro) -> SAP Fiori (UX) -> Web application


- Application layer : NW Foundation  (NW7.4->NW7.53)


- DB layer : HANA DB* (Code-to-data)


- OData : HANA XS OData服务，通过声明式的代码，将HANA表或信息模型开放成OData实体


#### CDS(Extract the data) - 工具 Eclipse ADT  

- CDS Create

- @OData.publish: ture (Generate OData)

- Creates an ICF : /n/iwfnd/maint_service







