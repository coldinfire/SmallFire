---
title: " SAP HANA 知识链接"
date: 2019-05-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Sap Fiori

tags: 
  - HANA

---

### 了解 HANA 的途径有

http://help.sap.com 

http://www.sdn.sap.com/irj/sdn/in-memory 

http://service.sap.com/hana

#### HANA主要组成成分

- 客户端：HANA Studio(In-memory studio)

- 内存内数据库：HANA Database

- 数据复制组件：SLT/Sybase Replication Sever/BusinessObjects DataServices

#### 三种复制数据方法

- Trigger-based：使用的是 SAP 自己开发的已经有一段历史的工具 SAP Landscape Transformation Replication Server (SLT RS)，这是与底层数据库无关的技术。
- ETL-based：其实就是 ETL 数据到 HANA，使用的当然是 SAP EIM 的旗舰产品BusinessObjects DataServices。
- Log-based：是 Sybase 的 Replication Server，而这曾经是 SAP HANA 的首选，是基于数据库 change log 的复制技术。

#### SAP HANA应用场景

​	HANA DB 是一个列存储的数据库 ,列存储的数据库更容易压缩，聚合结果更快，所以是为分析所设计的。这是 HANA 将会使数据分析提速的因素之一。

​	HANA DB 是内存内计算数据库，也就是说不仅仅是部分数据存储在内存里，更重要的是，一些逻辑计算发生在内存的数据里，这样肯定要比在应用层计算快得多。这也是 HANA 使数据分析提速的重要因素。



