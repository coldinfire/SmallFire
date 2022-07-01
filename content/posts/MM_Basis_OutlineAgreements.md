---
title: " SAP Outline agreements "
date: 2019-03-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### SAP 框架协议

框架协议：与供应商的长期购买协议，产品范围比较固定，供应商的特点比较固化。

- 框架协议是与供应商的长期购买协议，其中包含与供应商提供的材料有关的条款和条件。
- 框架协议的条款在一定时期内有效，并涵盖一定的预定义数量或价值。

#### 合同

主要包括数量、价值的合同，有限制管控的作用。要进行release order的订单环节的处理，最终生成 contract release order。

- 数量合同：总价值是根据供应商要提供的材料总量确定的。后续参考合同协议创建 PO，数量累计不能超合同的总量。否则会警告或错误信息。

- 价值合同：总价值是根据该材料应支付给卖方的总金额指定的，价值累计不超合同的总额。

#### 计划协议

计划协议是卖方和订购方之间关于预定义材料或服务的长期概述协议，该预定义材料或服务是在时间框架内的预定日期获得的。只针对单价、数量的长期合同，针对低价值的产品。最终生成 Delivery Schedule。

**做采购订单参考合同或计划协议的时候，默认把价格带过来。且价格优先级高于 Info Recorde。**

### 合同（Contract）

创建合同：`ME31K`（Logistics - Materials Management - Purchasing - Outline Agreement - Contract - Create）。

- 输入内容：供应商名称，合同类型，采购组织，采购组和工厂以及协议日期

 SAP系统中有两种标准合同类型

- MK：数量合同
- WK：价值合同

#### 分散采购：可设置针对工厂、采购组织等



#### 集中采购合同：不指定工厂



#### 项目类型为 M 或 W

要求采购订单科目必须为 K 成本中心。

- M：Material Unkonwn，要求输入 Material Group
- W：Material Group

对于合同设置 M 创建采购订单，科目类别需要设置为 K（成本中心）。价格自动由合同带入，数量同样受合同限制。



### 计划协议（Scheduling）

创建计划协议：`ME31L`（Logistics - Materials Management - Purchasing - Outline Agreement - Scheduling Agreement - Create）。

- 协议类型一般选择 `LP`。



自动带入价格：



创建完成后：

- 通过 ME51N 创建采购申请 --> ME21N 采购订单
- 通过 ME38 创建交货计划 （Delivery Schedule） 做送货计划 --> ME21N 采购订单

#### Delivery Schedule：ME38

维护计划交货日期（和PO类似，也可以参考采购申请，也可以直接建）。

MIGO 做收货时不能提前收货（相当于叫料计划），只能收取 scheduling 中当前或之前的数量。

