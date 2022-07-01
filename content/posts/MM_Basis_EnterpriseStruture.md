---
title: " SAP 企业架构 "
date: 2019-03-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### Enterprise Struture ###

#### 整体架构

集团：是 R/3 系统里的一个商业的组织层次。有自己的数据、主记录及各种报表。从业务角度看，集团当作各个实体的组合。

公司代码：一个公司代码代表一个独立的会计实体。每个公司代码都有它自己得到资产负债表和损益表。

工厂：是公司内的组织单元，生产产品、提供服务或销售产品，工厂可以是（制造厂、仓库中转中心、 区域销售办公司、公司总部）。

库存地点：是工厂内存放不同类型物料的组织单位，如：材料仓，成品仓，材损仓。

采购组织：是负责为一个或多个工厂工厂采购物料和提供服务及与供应商协商价格和供货条款的主旨单位。价格条件在采购组织层次设置。如果一个采购组织为多个工厂采购，则价格在各个工厂都有效。

采购组：是采购组织的进一步细分，负责日常的采购活动，一个采购组也可以为多个采购组织工作。

#### 集团 / 公司代码 / 工厂 ####

![架构关系](/images/MM/Client1.png)

#### 公司代码 / 工厂 / 库存地点 ####

![架构关系](/images/MM/Client2.png)

### 创建并配置各组件

#### 1.Define Company : OX15

   - SPRO -> IMG ->Enterprise Structure -> Definition -> Financial Account -> Define Company

![Define Company](/images/MMMasterData/1.png)
![Details](/images/MMMasterData/2.png)

- 1.Enter company                     
- 2.Enter company name            
- 3.Update address
- 4.Enter country code of company 
- 5.Enter language key 
- 6.Enter local currency

#### 2.Define Company Code : OX02

  - SPRO -> IMG ->Enterprise Structure -> Definition -> Financial Account -> Edit,Copy,Delete Company Code
![Company Code](/images/MMMasterData/3.png)
![Details](/images/MMMasterData/4.png)

#### 3.Assign Company Code to Company : OX16

  - SPRO -> IMG ->Enterprise Structure -> Definition -> Financial Account -> Assign Company Code To Company

![Assign Demo](/images/MMMasterData/5.png)

#### 4.Define Plant : OX10

  - SPRO -> IMG ->Enterprise Structure -> Definition -> Logistics ->General -> Define plant

![Define Plant](/images/MMMasterData/6.png)

#### 5.Assign Plant to Company Code : OX18

  - SPRO -> IMG ->Enterprise Structure -> Assignment -> Logistics -> AssignPlant to Company Code

![Assign](/images/MMMasterData/7.png)

#### 6.Maintain Storage Locations : OX09

  - SPRO -> IMG ->Enterprise Structure -> Definition -> Material Mast -> Maintain storage Location

![Storage Location](/images/MMMasterData/8.png)

#### 7.Maintain Purchase Organization : OX08

   - SPRO -> IMG ->Enterprice Structure -> Definition -> Material Mast -> Maintain Purchasing Org

![Purchase Org](/images/MMMasterData/9.png)

#### 8.Create Purchase groups : OME4

 - SPRO -> IMG ->MM -> Purchasing -> Create Purchasing Groups

![Purchasing Groups](/images/MMMasterData/10.png)

#### 9.Assign Purchasing org to Company Code : OX01

 - SPRO -> IMG -> Enterprice Structure -> Assign -> Material Mast  -> Assign Purchase Org to Company Code
![Assign](/images/MMMasterData/11.png)

#### 10.Assign Purchasing organization to reference purchasing org

  - SPRO -> IMG -> Enterprice Structure -> Assign -> Material Mast -> Assign Purchase Org to RPO

#### 11.Assign Purchase Organization to Plant : OX17

  - SPRO -> IMG -> Enterprice Structure ->Assignment -> Material Mast -> Assign PO to Plant

![Org To Plant](/images/MMMasterData/12.png)

#### 12.Assign  standard purchasing org to plant：SM30(V_001W_E)

- SPRO -> IMG -> Enterprice Structure -> Assignment -> Masterial Management -> Assign standard purchasing org to plant

一个工厂可以指定多个采购组织，但在一些特定的业务中需要自动确定采购组织，这时就需要在后台对工厂指定唯一的采购组织，也就是标准采购组织.

