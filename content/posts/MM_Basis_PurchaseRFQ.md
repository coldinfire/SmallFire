---
title: " RFQ 流程解析 "
date: 2019-03-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### Request For Quotation (RFQ): ME41 /42/43

- SPRO：Logistics -> MM -> Purchasing -> RFQ/Quotation -> RFQ

- Enter REQ type、Date limit、Purch Org、Purch Group、Plant、Storage Location

  ![RFQ](/images/MM/RFQ/RFQ_ME41.png)

- Enter Collective No to track of all RFQ related to particular instance and ref data

  ![RFQ Head](/images/MM/RFQ/RFQ_Head.png)

- Enter the appropriate item category、material、RFQ Quantity

- Enter the Deliv. Date、Plant、Submission date、Click Create Vendor address


Create Vendor Address Screen:

​	![Vendor Addr](/images/MM/RFQ/Vendor_addr.png)

### Maintain Quotation : ME47 

- SPRO：Logistics -> MM -> Purchasing -> RFQ/Quotation -> Quotation -> Maintain

#### 价格比较：ME49

#### 市场价：MEKH(MP01)

#### Info Record update：选择工厂更新

- A：有或则没有工厂的更新
- B：只允许有工厂的更新
- C：无工厂更新

维护工厂的条件维护：

- SPRO：Logistics -> MM -> Purchasing -> Conditions -> Define Condition Control at Plant level

### Set Tolerance Limits For Price Variance

- SPRO：MM -> Purchasing -> Purchase Order -> Set Tolerance Limits For Price Var


- Tolerance key: PE-价格差异购买              
- SE-最大现金光盘扣除(购买)

![Tolerance](/images/MM/RFQ/Tolerance.png)

### Maintain source list : ME01/ME03/ME04

Enter Valid time、Vendor、Purchasing Org、Fix(固定源)、Blk(供应商阻止采购此物料)，MRP 1.包含

![Source List](/images/MM/RFQ/SourceList.png)

### Create Purchasing Requisition : ME51N/ME52N/ME53N

- SPRO：Logistics -> MM -> Purchasing -> Purchase Requisition -> Create

Header note:部门账号，授权部门签名，交货说明，条款和条件

Select document type for PR

Enter material code

Enter quantity of material

Update delivery date

Enter respective plant code

Enter corresponding storage location

Enter requisition use id

