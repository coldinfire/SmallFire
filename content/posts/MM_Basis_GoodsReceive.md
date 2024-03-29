---
title: " SAP 收货流程 "
date: 2019-03-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### 1.收货发票 Post Goods Receipt Invoice (GRIN) : MIGO

#### GRIN 清单：

供应商发票上的PO# 与Org PO #相同   

供应商地址和采购订单地址匹配

物料数量应在Org PO中可用

发票价格和采购订单价格匹配

发票和采购订单物料描述相同

供应商名称与发票和PO间匹配

PO和发票中的税匹配

#### 收货：MIGO

输入 PO Number 然后输入详细信息

Delivery Note：交货单输入发票参考

Vendor :检查供应商名称

Bill of Lading : Non-bounded

Header Text：输入项目参考

Tick on collective slip：集体票据(打钩)

附件添加：更新所有必填信息后，可以保留、检查、发布

点击执行按钮保存文档生成 GRIN Number。

![MIGO](/images/MM/GR/MIGO.png)

MIGO ：Select Cancellation and update GRIN No to reverse the GRIN. Post.

### 2.Post goods issue : MB1A (发料)

Update the movement type from options.

Enter the plant code.

Update the reason for movement key from the possible entries.

Updating all the required,press enter to continue.

![MB1A](/images/MM/GR/MB1A.png)

### 3.发票校验：MIRO

#### 介绍

发票校验是MM的一部分。提供物料管理部分和财务会计，成本控制和资产管理部分的连接。

#### 目的

完成物料采购的全过程：物料采购从采购申请(PR)开始，然后下PO和收货,并以收到发票而结束。

允许处理不基于物料采购的发票（服务费，其他花费等）

允许处理贷项凭证，既可以是发票的取消，也可以是打折扣

发票校验不是对支付进行处理，也不是对发票进行分析，这些需要处理的信息传递到其他部门。

#### 任务

输入接收到的发票和贷项凭证，检验发票内容,价格和计算第准确性

执行一个发票的账目记账

更新SAP系统内的一些数据，如未结算项目和物料价格

检查因为与采购订单出入太大而别冻结的发票

#### 过程

基于采购订单的发票校验：输入采购订单号，查看信息，可修改缺省数据。

基于收货的发票校验：先收货，再开票。***一般建议基于收货的发票校验***。

如果发票和PO有误差，系统将会发出警告。如果变化在预先设定的容差范围内，系统将允许该发票被记账，但会自动的冻结发票并支付。如果变化不在允许的范围内，系统将不允许发票被记账。

- 发票必须在一个分开的步骤中被批准。


- 发票被输入时，系统将找到对应的账户科目。系统将自动生成销售税，现金折扣清算和价格差异，这些记账记录被显示出来。如果存在余额，用户要进行修正，***只有余额为零发票才能被记账。***


- 发票被记账后，一些数据会在系统内被更新，如订购的物料的平均价格和采购订单历史。


- 发票记账完成了发票校验。需要被支付的数据包含在系统中，会计部门可以读入这些数据       并作出合适的支付。


#### 校验种类

规则：一张发票表示一个事务，事务的发货方要求被付款。

基于采购订单的发票：基于采购订单的发票校验，一个订单的所有项目可以被一起处理，接收部分收货。所有          的收货被汇总并作为一个项目进行记账。

基于收货的发票：发票不是关联于采购订单，而是关联于分别的交货活动。发票的参考凭证不是采购订单           PO,而是交货通知或则收料单凭证。一个货物接收活动必须在发票已经输入系统之前。

发票输入的方式可能是参考一个交货通知或一个货物接收凭证。

前提条件：有关的 PO 项目必须有基于收货的发票校验标志。

操作：转向采购定单项目详细屏幕。选择字段  GR-IV。保存采购定单。

- 你可以在任何时候显示货物接受和发票的关系。你将在采购定单项目的历史中找到这些信息。 


没有订单的发票：如果没有采购订单做参考，可以直接将事务记入一个物料帐户，一个总帐帐户，或一个资产帐户。

有差异的发票校验

- 差异分为：数量差异，价格差异，计划差异，采购订单价格数量差异，质量检查


- 数量差异：发票数量大于已交货数量和已开发票数量的差异


- 价格差异:发票金额/发票数量<>净订单价格


- 计划差异:PO Delivery Schedule 的收货日期与实际收货日期的差异.


- 采购订单价格数量差异:收货数量/价格与采购订单的数量价格之间的差异.


- 质量管理:如果商品激活了质量管理,则在过账时过到检查库存.


无差异的发票校验

- 过程一样，只是数量和金额无差异。


#### 预置发票：MIR7

相当于发票请求单，可以进行多次修改，保存后不自动过账。做完预置发票后执行MIRO,选择预置发票。

#### 后台发票校验：MIRA

后台校验是在后台进行发票的检查，如果在容差范围之内，将在后台直接过账

后台检查发票的正确性：WC23

### 4.后台配置

#### 容差配置：OMR6

容差是基于公司代码的配置,因为发票是根据公司代码开具.配置上/下限"检查限制",或设置%.一般企业要配置四种容差：

- BD：(自动形成小的差异)


- DQ：(超出金额: 数量偏差)


- PP：(价格变化)


- VP：(移动平均价格差异).


配置供应商容差:PATH:物料管理->后期发票校验->收到的账单->配置指定供应商容差,在发票校验中，供应商特定的参数的设置。在一个公司代码中，只可以将一个容差组分配到一个供应商。在供商主数据(XK02)中有一个容差组

配置发票校验条件:PATH:物料管理->后期发票校验->信息确认->维护条件->修改-发票校验

配置凭证类型:PATH:物料管理->后期发票校验->收到的账单->号码分配->维护科目凭证的编号范围->凭证类型

配置默认税代码: PATH:物料管理->后期发票校验->收到的账单->维护税代码的缺省值　(根据公司代码设置)

开票前准备：在做采购订单时在项目明细“发票”页“基于收货的IV”字段设置为‘X’.

### 5.基于收货的发票校验：MIRO

输入公司代码；选择凭证类型(贷项凭证/发票)，结算总金额为负则为贷项凭证，为正则为发票。当发票即含有进也含有退时在输入采购订单号右边的Button,单击进入后,把退货选成贷向,进货选成借向,否则无法做发票校验。

MIR4：显示发票凭证