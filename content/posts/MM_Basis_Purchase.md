---
title: " 有库存的采购流程解析 "
date: 2019-03-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### 采购流程

#### 单据流

- 仓库数量被预约 (采购订单下达)。

- 采购订单收货，总账增加，库存成本增加。


-  应付账款不改变库存数量，根据数量和价格计算价格。

#### 采购物料：创建 PO

-  物料可以被用作销售和采购的，库存物料等。


- 分配科目，会记账到指定科目。


- 物料主数据字段：供应商，制造商目录编号，采购计量单位，进口物料关税组，纳税信息等。   

#### 采购订单收货

和采购订单有关联关系，可以将采购单的数据展现出来。

复制到：从已保存单据复制到新单据，可以在复制后删除物料和调整数量。

复制从：在新单据中输入业务伙伴，从清单中选择一个或多个单据，可定制复制的行和数量等信息。

影响：

- 采购订单会被关闭，已清状态
- 会计凭证自动生成
- 库存量增加
- 基础单据和目标单据产生关联关系

#### 应付发票：应付账款自动产生

- 先收货的话，数量会增加，账不会影响库存
- 成本核算
- 应付账款的凭证
- 收货单关闭，关联关系产生

#### 支付处理：财务部门

- 向供应商支付货款

- 输入应付发票

- 根据付款条件创建付款

- 日记账条目：自动减少现金(贷项)，减少向供应商支付的金额

### PO 创建


一般数据：供应商数据、原始数据、订购单位等

价格和条件：总价格、折扣、运费、关税、其他

控制数据：交付时间、最小数量、容差

文本：采购订单文本、信息记录备注

统计：价格历史记录、采购订单统计、改变历史记录. . . . . 

#### 订单结构

PR Header：采购订单编号、供应商、货币、付款期限、采购订单日期

PR Item：物料编号、短文本、发货日期、采购订单数量、采购订单价格、分类

科目设置对象：

​	A：资产    K：成本中心    P：项目    F：订单(生产，考虑)    C：销售订单    其他

无物料的采购订单：采购订单中不输入物料，只输入描述可以收货，但是必须是直接消耗，没有库存管理。适用于低值品的采购

采购合同   <联系、集成、自动>   采购订单

#### 采购订单相关表关系

| Table | Description                    | Table | Description                 | Table | Description                  |
| :---- | :----------------------------- | :---- | :-------------------------- | :---- | :--------------------------- |
| EKKO  | 采购凭证抬头                   | EINA  | 采购信息记录 - 一般数据     | EKES  | 供应商确认                   |
| EKPO  | 采购凭证行项目                 | EINE  | 采购信息记录 - 采购组织数据 | EKKN  | 采购凭证中的帐户设置         |
| EORD  | 采购货源清单                   | EKET  | 计划协议计划行              | EKBE  | 采购凭证历史                 |
| RBKP  | 凭证表头：发票收据             | RSEG  | 凭证项目：收款发票          | EKBZ  | 每个采购凭证的历史：交货费用 |
| RBCO  | 凭证项目，收到的帐单，科目分配 | RBMA  | 凭证项目: 物料的收款发票    |       |                              |

#### 关系结构：

![Table](/images/MM/Purchasing/PurchaseTable.png)

### SAP 采购模式

#### 标准采购类型

业务人员根据需求部门提报的物资需求计划，用询价/比价的方式，然后根据各项信息创建标准采购订单。这是一种最常用的采购方式。

#### STO（库存转移单）

某些配置方案会把这类采购类型配置成公司间库存转移单。在很多企业中，会有公司间的物资交易。一个集团中，分公司之间的交易，或者分公司与总部的交易。

#### 委外加工类型

也称为带料加工。这种业务的业务场景是：企业有一种 A 规格管材，而需求部门需要一种 B 规格管材。而根据物资的特性，A 是可以加工成 B 的。那么这时候，就可以建立委外加工的采购订单，通过 541 移动类型对这个采购订单发货，发到供应商。之后通过标准的一步或两部收货收到仓库。

#### 寄售采购类型

这是大型企业常用的一种采购方式。在企业的仓库中，会单独建立一个供应商仓库。做寄售采购订单时， 先检查供应商仓库中是否有足够数量的物资，如果有，就把供应商库存的物资通过 411 移动类型收到企业自有仓库，这时产生一个411的物料凭证；如果没有，就先通知供应商往仓库补货，当供应商从外面把货物补到供应商库存以后，会产生一个 101k 移动类型的物料凭证，之后再按照之前同样的方式收到企业自有库存。

在寄售采购订单中没有单价，只有数量，这是因为企业与供应商先约定某些物资的采购单价，那么在一段比较长的时间内，按照这种价格执行。这个价格会维护在货源清单里面。

#### 框架采购类型

这种采购类型比较适合大批量物资的采购，比如石化行业的煤炭采购。因为石化行业的煤炭采购量以万吨计。在做这种采购类型时，企业会与几个供应商分别签订框架协议，这种框架协议分为数量型或金额型。约定企业在某一段时间内，会向该供应商采购一定数量或金额的某类物资。那么在做这类采购订单时，会参照之前签订的框架协议做采购订单。

### 采购组织分配

能通过复制实现的就复制：可以避免一些意想不到的问题发生

工厂创建（通过复制形式实现，不会漏掉一些具体配置）： OX10

采购组织创建： OME4

采购组织分配给工厂：OX17

给工厂分配标准采购组织：

#### 采购组织的使用

- 当工厂有多个采购组织时，当产生先收货然后自动创建 PO 业务时，PO 中的采购组织为标准的采购组织


- 在合同中会使用到采购组织


Purchase Requisition ：Manual / MRP 创建 PR

RFQ/Quotation：询价，报价，比较合理的供应商

Purchase Order：ME21N 创建采购订单

PR&RFQ 确定 PO，可以向供应商或则其它工厂购买，包含Title and Item

Issuing Messages：采购信息的发送

#### Goods Receipt

GR  会产生两个文档

- Material Document：数量等信息

- Accounting Document：记录价钱或种类分类账信息






