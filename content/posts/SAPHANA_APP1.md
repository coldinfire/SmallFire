---
title: " SAP HANA 结构分析"
date: 2019-05-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Sap Fiori

tags: 
  - HANA

---

### 组成结构

​	Presentation Layer ：SAPGUI -> Browser(BSP/Webdynpro) -> SAP Fiori (UX) -> Web application

​	Application layer : NW Foundation  (NW7.4->NW7.53)

​	DB layer : HANA DB* (Code-to-data)

​	OData : 

#### CDS(Extract the data) - EclipseADT  

- CDS Create

- @OData.publish: ture (Generate OData)

- Creates an ICF : /n/iwfnd/maint_service

#### Fiori - WebIDE







