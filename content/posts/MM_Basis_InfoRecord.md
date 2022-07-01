---
title: " Info Record 使用 "
date: 2019-03-19
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### 创建 Info Record

信息记录 (Info record)：一个物料在某个供应商那里，卖多少钱，有什么特殊条件等信息的存储。

创建方式：手工输入，报价单选择，采购订单，框架协议更新

#### 手工输入：ME11/12/13/14/15、MEMMASG

- SPRO > IMG > MM > Purchasing > Main data > Info Recore

主要数据：

- 供应商，物料，采购组织

- 信息类别
  - 标准
  - 分包：委外
  - 管道线：石油，水，电
  - 寄售：供应商协议

详细信息：

- 供应商数据

  - 供应商物料编号：对应供应商方提供物料的编号，方便对方发货

  - 子范围：同一供应商具有不同的发货点

    开启功能：SPRO>商业伙伴>供应商>控制>定义科目组合字段选择>【相关的供应商子域，相关工厂级别】

    定义供应商时：设置对应的子范围和子范围对应的替代数据
    
    使用：创建PO时>Item>主数据>定子范围

- 供应选择

  - 可使用时间段
  - 常规供应商

- 订单单位(从物料主数据获取)

  - 订单单位
  - 转换关系

采购组织数据1：

- 控制

  - 计划交货时间：Lead Timd = L/T，前置时间，（下PO到供应商送货日期）

  - Underdel.tol/Overdeliv.tol：收货数量容差百分比  

    Unlimited：不加收货数量限制

  - 采购组

  - Standard qty：等级（不同购买量，价格等级）

  - Minimum qty、Maximum qty

  - 剩余货架寿命(Rem.shelf life)：收货后物品失效日期

  - GR-based IV

  - NO ERS：财务定期结账；前提是设置【供应商的采购数据>控制数据>设定评估】 TCode:`MRRL`

  - 需求确认：收到货后是否需要供应商确认
  
- Condation

  - 设定价格有效期，不同时间段不同价格
  - 等级对比（F2）,设定购买不同数量级的价格

采购组织数据2：

- 采购凭证：与采购订单相关联

#### 报价单选择：A/B/C

在报价单创建时，选择Info Record update。产生报价单时，会产生Info Record

#### 采购订单

ME21N > Item > MM > Info Record Update

#### 框架协议

### 更新

Customizing：Define control of conditions at plan level

- NULL：Conditions allowed with or w/o plant
- +：Only plant-based conditions allowed
- -：No plant-based conditions allowed

### 使用

**Info Record和价钱决定：ME21N 创建 PO**

1）如果是手工输入价钱，得到输入的价钱

2）如果手工没有输入，系统检查是否有 Info record，没有 Info record 需要手工输入；如果有 Info Record 且 Info Record 中有有效的价格，采用 Info record 价格。

3 ）如果 Info record 中没有 Info record 价格，最后采用 Last  PO 价格，也可以不从 Last PO 带入价格。

- 物料 > 采购 > 定义采购员的缺省值：定义采用价格 > 一直复制
- Purchasing > Env Data > Define default values for Buyers

