---
title: " Product Order 的增强 "
date: 2018-11-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - SAPbusiness

---

### 生产订单的增强

如果SAP系统的标准功能不能满足业务需求，用户出口或 BADI 可能会有所帮助。

#### Customer Exit

- SMOD:SAP增强管理

- CMOD:SAP增强编辑器维护。

用于订单创建和执行的用户出口：存在于Function Group **XCO1**

| Enhanment（SMOD） | Desc                                                         |
| ----------------- | ------------------------------------------------------------ |
| PPCO0001          | Application development: PP orders                           |
| PPCO0002          | Check exit for setting delete mark / deletion indicator      |
| PPCO0003          | Check exit for order changes from sales order                |
| PPCO0004          | Sort and processing exit: Mass processing orders             |
| PPCO0005          | Storage location/backflushing when order is created          |
| PPCO0006          | Enhancement to specify defaults for fields in order header   |
| PPCO0007          | Exit when saving production order                            |
| PPCO0008          | Enhancement in the adding and changing of components         |
| PPCO0009          | Enhancement in goods movements for prod. process order       |
| PPCO0010          | Enhancement in make-to-order production - Unit of measure    |
| PPCO0012          | Production Order: Display/Change Order Header Data           |
| PPCO0013          | Change priorities of selection crit. for batch determination |
| PPCO0015          | Additional check for document links from BOMs                |
| PPCO0016          | Additional check for document links from master data         |
| PPCO0017          | Additional check for online processing of document links     |
| PPCO0018          | Check for changes to production order header                 |
| PPCO0019          | Checks for changes to order operations                       |
| PPCO0021          | Release Control for Automatic Batch Determination            |
| PPCO0022          | Determination of Production Memo                             |
| PPCO0023          | Checks Changes to Order Components.                          |

用于确认时的User exits:

| Enhancement（SMOD） |                                                            |
| ------------------- | ---------------------------------------------------------- |
| CONFPP01            | PP order conf.: Determine customer specific default values |
| CONFPP02            | PP order conf.: Customer specific input checks 1           |
| CONFPP03            | PP order conf.: Cust. specific check after op. selection   |
| CONFPP04            | PP order conf.: Cust. specific check after op. selection   |
| CONFPP05            | PP order conf.: Customer specific enhancements when saving |
| CONFPP06            | PP Order Confirmations: Actual Data Transfer               |
| CONFPP07            | Single Screen Entry: Inclusion of User-Defined Subscreens  |

#### BADI

这种增强使用事务码 SE18 定义、SE19 实现。

SE18用于创建及维护BADI对象；SE19用于维护BADI的实例。

SAP预定义了一些接口（Interface），客户可以自行定义实现Interface的类（Class），在标准程序中会调用客户自定义类的实例。

| BADI（SE24）                  | Desc                                                        |
| ----------------------------- | ----------------------------------------------------------- |
| WORKORDER_CONFIRM             | Business Add-In PM/PP/PS/PI Orders  Operation: Confirmation |
| WORKORDER_CONFIRM_CUST_SUBSCR | Confirmation: Subscreen with Customer's Own Fields          |
| WORKORDER_CONFIRM_EA_APPL     | Confirmation: Function Calls from EA-APPL                   |
| WORKORDER_DFPS_PIC_ATP        | Defense BADI for Availability Check                         |
| WORKORDER_DOCLINKS            | BADI: Document Links (Production Orders)                    |
| WORKORDER_FINANCIALS          | Connection between FIN Add-On and Manufacturing Orders      |
| WORKORDER_GOODSMVT            | BADI: PM/PP/PS/PI Orders: Automatic Goods Movement          |
| WORKORDER_INFOSYSTEM          | BADI: PP and PI Order Information System                    |
| WORKORDER_MODIFY_SCREEN       | Screen Adjustments                                          |
| WORKORDER_PISHEET             | BADI: PM/PP/PS/PI Orders Operation: Discarding of PI sheets |
| WORKORDER_REWORK              | BADI: PP Orders Operation: Rework                           |
| WORKORDER_UPDATE              | BADI: PM/PP/PS/PI Orders  Operation: UPDATE.                |

