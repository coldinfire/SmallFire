---
title: "RFQ"
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

- SPRO > IMG > Logistics > MM > Purchasing > RFQ/Quotation > RFQ

- Enter REQ type,Date limit,Purch Org,Purch Group, Plant ,Storage Location

  ![RFQ](/images/MMPurchasing/RFQ_ME41.png)

- Enter Collective No to track of all RFQ related to particular instance and ref data

  ![RFQ Head](/images/MMPurchasing/RFQ_Head.png)

- Enter the appropriate item category

- Enter the material NO , RFQ Quantity

- Enter the Deliv. Date，Plant code , Submission date , Click Create Vendor address

  ![RFQ Item](/images/MMPurchasing/RFQ_Items.png)

Create Vendor Address Screen:

​	![Vendor Addr](/images/MMPurchasing/Vendor_addr.png)

### Maintain Quotation : ME47 

- SPRO > IMG > Logistics > MM > Purchasing > RFQ/Quotation > Quotation > Maintain

#### 价格比较：ME49

#### 市场价：MEKH(MP01)

#### Info Record update：选择工厂更新

- A：有或则没有工厂的更新
- B：只允许有工厂的更新
- C：无工厂更新

维护工厂的条件维护：SPRO > IMG > Logistics > MM > Purchasing > Conditions > Define Condition Control at Plant level



### Set Tolerance Limits For Price Variance

- SPR > IMG > MM > Purchasing > Purchase Order > Set Tolerance Limits For Price Var


- Tolerance key: PE-价格差异购买              
- SE-最大现金光盘扣除(购买)

![Tolerance](/images/MMPurchasing/Tolerance.png)

### Maintain source list : ME01/ME02/ME03

Enter Valid time,Vendor,Purchasing Org,Fix(固定源),Blk(供应商阻止采购此物料)，MRP 1.包含

![Source List](/images/MMPurchasing/SourceList.png)

### Create Purchasing Requisition : ME51N/ME52N/ME53N

- SPRO > IMG > Logistics > MM > Purchasing > Purchase Requisition > Create

0.Header note:部门账号，授权部门签名，交货说明，条款和条件

1.Select document type for PR

 2.Enter material code

 3.Enter quantity of material

 4.Update delivery date

 5.Enter respective plant code

 6.Enter corresponding storage location

 7.Enter requisitioner use id

