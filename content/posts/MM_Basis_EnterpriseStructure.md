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

### Enterprise Structure ###

#### 整体架构

集团：是 R/3 系统里的一个商业的组织层次。有自己的数据、主记录及各种报表。从业务角度看，集团当作各个实体的组合。

公司代码：一个公司代码代表一个独立的会计实体。每个公司代码都有它自己得到资产负债表和损益表。

工厂：是公司内的组织单元，生产产品、提供服务或销售产品，工厂可以是（制造厂、仓库中转中心、 区域销售办公司、公司总部）。

库存地点：是工厂内存放不同类型物料的组织单位，如：材料仓，成品仓，材损仓。

采购组织：是负责为一个或多个工厂工厂采购物料和提供服务及与供应商协商价格和供货条款的主旨单位。价格条件在采购组织层次设置。如果一个采购组织为多个工厂采购，则价格在各个工厂都有效。

采购组：是采购组织的进一步细分，负责日常的采购活动，一个采购组也可以为多个采购组织工作。

#### 集团 / 公司代码 / 工厂 ####

![架构关系](/images/MM/EnterpriseStructure/Client1.png)

#### 公司代码 / 工厂 / 库存地点 ####

![架构关系](/images/MM/EnterpriseStructure/Client2.png)

### 创建并配置各组件

#### Define Company：OX15

   - SPRO：Enterprise Structure -> Definition -> Financial Accounting -> Define Company

![Define Company](/images/MM/EnterpriseStructure/DefineCompany.png)

![Details](/images/MM/EnterpriseStructure/DefineCompanyDetails.png)

- Enter company                     
- Enter company name            
- Update address
- Enter country code of company 
- Enter language key 
- Enter local currency

#### Define Company Code：OX02

  - SPRO：Enterprise Structure -> Definition -> Financial Account -> Edit,Copy,Delete Company Code

![Company Code](/images/MM/EnterpriseStructure/DefineCompanyCode.png)

![Company Code Details](/images/MM/EnterpriseStructure/DefineCompanyCodeDetails.png)

#### Define Plant：OX10

  - SPRO：Enterprise Structure -> Definition -> Logistics General -> Define,copy,delete plant

![Define Plant](/images/MM/EnterpriseStructure/DefinePlant.png)

#### Assign Company Code to Company：OX16

  - SPRO：Enterprise Structure -> Assignment -> Financial Account -> Assign company code to company

![Assign Company Code to Company](/images/MM/EnterpriseStructure/AssignCompanyCodeToCompany.png)

#### Assign Plant to Company Code：OX18

  - SPRO： Enterprise Structure -> Assignment -> Logistics General -> Assign Plant to Company Code

![Assign Plant to Company Code](/images/MM/EnterpriseStructure/AssignPlantToCompanyCode.png)

#### Maintain Storage Locations：OX09

  - SPRO： Enterprise Structure -> Definition -> Materials Management -> Maintain storage location

![Storage Location](/images/MM/EnterpriseStructure/StorageLocation.png)

#### Maintain Purchase Organization：OX08

   - SPRO： Enterprise Structure -> Definition -> MM  -> Maintain Purchasing Org

![Purchase Org](/images/MM/EnterpriseStructure/PurchaseOrg.png)

#### Create Purchase groups：OME4

 - SPRO：Material Management -> Purchasing -> Create Purchasing Groups

![Purchasing Groups](/images/MM/EnterpriseStructure/PurchaseGroup.png)

#### Assign Purchasing org to Company Code：OX01

 - SPRO：Enterprise Structure -> Assignment -> MM -> Assign Purchase Org to Company Code

![Assign Purchasing org to Company Code](/images/MM/EnterpriseStructure/AssignPurOrgToCompanyCode.png)

#### Assign Purchase Organization to Plant：OX17

  - SPRO： Enterprise Structure -> Assignment -> MM -> Assign Purchase Org to Plant

![Org To Plant](/images/MM/EnterpriseStructure/AssignPurOrgToPlant.png)

#### Assign  standard purchasing org to plant：SM30(V_001W_E)

- SPRO：Enterprise Structure -> Assignment -> MM -> Assign standard purchasing org to plant

![Org To Plant](/images/MM/EnterpriseStructure/StandardPurOrgToPlant.png)

一个工厂可以指定多个采购组织，但在一些特定的业务中需要自动确定采购组织，这时就需要在后台对工厂指定唯一的采购组织，也就是标准采购组织。

