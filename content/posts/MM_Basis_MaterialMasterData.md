---
title: " MM 物料主数据管理 "
date: 2019-03-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

###  Material Master Data ###

#### 相关事物码

- MM01/02/03：创建 / 修改 / 显示

- MM06：立即删除标记。Material 的删除分两个层次：一个是加 Delete flag material 但号码还存在， 但强制使用还是可以的， 所以建议使用 Status 来控制，将所有的状态都 block 住。

  ![MM06](/images/MM/MM06_Delete.png)

- MM17：批量维护物料主数据内容

- MM50：物料视图扩充

- MM60：物料清单

- MMAM：配置物料类型

- MMD1：MRP文档创建

#### 主要信息 ####

描述性信息：描述，尺寸，体积等

控制性信息：MRP类型，价格控制，采购组等

物料主数据：

 - General：Material Number、Description、Base Unit Of Measure、Technical
 - Plan Specific：MRP type、Planned delivery time、Purchasing group、Batch indicator
 - Valuation：Valuation price、Valuation procedure、Valuated quantity

#### 必要数据

物料编码：物料编码用来唯一地识别有关物料主记录，用户可在物料管理客户化修改中设置长度并记录物料编码的模式。

- 号码范围：(Group、Material type、Number Ranges) 外部给号，内部给号范围的限制；允不允许外部给号，需要设置Group中包不包含外部Number range；

![定义Number Ranger](/images/MM/MM_NR.png)

行业领域：根据不同的行业领域，决定有哪些与行业相关的数据可以显示；

物料类型：按照物料在公司的不同用途，将物料分为不同类型；决定财务和仓库管理的特性。例如：可将物料分为原材料、半成品、成品、服务。贸易型货物或内部、外部拥有的空容器等各种类型。可做如下控制：

- 定义哪个部门可维护物料数据
- 定义采购类型
- 在相关的工厂中定义库存管理类型，例如，可通过物料类型定义更新哪些数据，但价值不变
- 是自动过帐定义的一个因素，财务参照物料类型确定库存科目

标准物料类型：

| Detail                               | Detail                          |
| ------------------------------------ | ------------------------------- |
| 成品（FERT）：Finished Product       | 备品备件（ERSA）：Spare Parts   |
| 半成品（HALB）：Semifinished Product | 贸易货物（HAWA）：Trading Goods |
| 原材料（ROH）：Raw materials         | 可反复利用包装（LEIH）          |
| 包装物料（VERP）：Packaging          | 未估价物料(UNBW)                |
| 管线物料（PIPE）：Pipeline materials |                                 |

物料类型设置：

- SPRO：SAP后台配置 

  ![Material Types](/images/MM/MM_material_type1.png)

- 详细信息：User departments（决定哪些部门需要维护）

  ![Material Types Control](/images/MM/MM_material_type.png)        

  - External no. assignment w/o check, 如果选中就代表，建立 Material 并且外部给号的时候，不会去检查是否符合 External no range。

- 控制数量/价值更新：决定数量和价值的更新（客户供料：只会做数量更新，不会做价钱更新<不属于本公司>）

  ![Material Types Control](/images/MM/MM_material_type2.png)

- 控制内/外部采购订单：决定是不是自生产的(影响采购类型)，这两个配置主要作用于 MRP2 中的 “采购类型” 字段

  - 0：不允许
  - 1：允许外部采购，但是会有警告
  - 2：允许外部采购

  ![Material Types Control](/images/MM/MM_material_type3.png)        

- 决定使用哪个G/L Account：过账时的总账分类账

 ![Material Types Control](/images/MM/MM_material_type4.png) 

更改物料类型：满足以下条件并使用TCode：***MMAM***

- 没有库存;没有预留;没有采购文档（PR，PO，协议，合同）可以更改
- 新的物料类型和旧的物料类型有相同的G/L Account；有相同的数量/价值更新

物料状态定义：控制该物料可以做什么和不可以做什么

- SPRO![Material Status](/images/MM/MM_MS1.png)
- 定义![Material Status](/images/MM/MM_MS2.png)
- 设置![Material Status](/images/MM/MM_MS3.png)

#### 视图设置

- 物料主数据记录中的数据是集合在不同的视图中；
- 公司所有部门都需要使用物料主数据，因此在不同的部门会有不同的物料主数据视图。

视图结构：![视图](/images/MM/MMMain.png)

视图默认值设置：

- 通过缺省值设置默认Industry sector
- 通过缺省值设置默认View
- 通过缺省值设置默认Organization level，可从其他工厂复制
- 通过复制其他的物料号

数据屏幕：可以分为以下三层数据结构

- 初始级别：设置初始化必需数据

- 主体工作级别：主要的数据设置

- 辅助数据级别：在主数据级别外进行额外的信息补充

  ![数据屏幕](/images/MM/MMScreen.png)

字段控制：控制前台字段的显示状态

​	给字段组分配字段；通过组别的方式进行设置字段的显示状态：

![组别类型](/images/MM/MM_FS.png)

![各组别定义](/images/MM/MM_FS2.png)

![各组别定义](/images/MM/MM_FS1.png)

多个因素的优先级：

- Hide
- Display
- Required entry field
- Option entry field

#### 视图选择

**Basic Data1:一般属于物料描述等属性，属于集团类别。**

单位：基本单位，订单单位，转换因子（附加数据中有计量单位的设定，
计量单位组：预先定义好单位转换 <通过后台设置关键字 设置计量单位组中进行设置的>）

- 计量单位(计量单位组)

![计量单位组定义](/images/MM/MM_DUMG.png)

物料组：对物料进行分组，便于分析

- 通过物料组可以将不同的物料对应到评估类再对应不同的总账科目并实现自动过账，这是MM与FI实现集成的重要配置

- 物料组可以用来在做没有物料号的成本中心采购时，确定费用科目

旧物料号： 便于查找旧系统数据

部门：Division (1) 影响 SD/FI 两个模块 (2) 无值将导致销售无法生成订单，单价录入，报告书等

实验室 / 办公室：描述信息没有特殊描述

产品组：根据公司产品分组，不填写销售无法生成订单

毛重、净重：报关使用,创建SO的时候，重量会被自动带出

附加数据：通过附加数据来维护不同语言环境的物料描述

- 凭证数据、内部批注

**Purchase**



**MRP1：MRP控制**

MRP 类型、MRP 控制者、批量（在 PP 中会使用到的）

MRP 组：

MRP 类型：（固定值 **PD**）

MRP 控制者：谁来负责该MRP控制

批量大小：（固定值 **EX**）

MRP参数文件：MMD1创建

**MRP2：影响 MRP 运算**

采购类型：内部生产或则外部采购

特殊采购类型：有偿加工必填 <31>, 无偿加工品必填 < 30>
外部采购

仓储地点：物料存储仓库

计划边际码：采购品须填写准确日期，影响 MRP 运输

计划交货时间：进口件

**MRP3：可用性检查（02）**

策略组：内部生产品必填，固定值

**MRP4:**

选择方法：内部生产品必填，固定值

重复生产：内部生产品必填，固定值

重复生产参数 文件：内部生产品必填，固定值

工厂数据 / 存储：

存储条件：关系到销售出库

End Item：直买直卖的物品必须勾选

**Account 1**

`评估类、价格控制、价格单位、标准价格`

评估分类：影响会计计算，必须准确

价格确定：系统自动存在，没有时必须输入

**Consting 1**

### 后台配置操作

#### 1.物料主数据视图：

  - 包含：常规，采购，销售，库存， 计划，属性，备注等视图维护。

#### 2.定义全球估价类型(Split Valuation configuration) : OMWC

  - SPRO -> IMG -> MM -> valuation and account assign -> split valuation ->config split valuation


   第一个屏幕（Global Types）

   第二个屏幕（创建新的估价类型 Valuation Types：

```JS
Valuation type:识别估价类型的关键
Ext.PO : 是否允许外部采购订单（0：不允许，1：允许，提示警告，2：允许）
Int.PO  : 是否允许内部订单（0：不允许，1：允许，提示警告，2：允许）
Acct cat : 账户参考必填字段。选择合适的值）
     （创建新的估价类别： Valuation category :
        Def ext.procure:默认外部采购
        ext.procurement mand ： 复选框勾选则无法在PO级别更改评估类型
        Def in-house:
        val.type automatic: 复选框用于拆分评估和批次管理
```
#### 3.定义物料组： OMSF

  - SPRO -> Logistic general -> MM -> Settings for Key Fields -> Define Material Group

 物料组：是物料统计分组的标识，属于无组织机构级别，它在采购信息记录、采购订单等中使用。在创建采购订单时， 当输入物料编号，物料组就自动被带出。物料组用于内部管理，分类较细。用户可以通过物料组获取整个采购状况及当前库存的标准SAP报表。

- [物料组的实用功能](https://blog.csdn.net/weixin_40672823/article/details/109766124)

#### 4.创建物料类型： OMS2

  - SPRO -> Logistic general -> MM -> Basic Settings -> Material Types

物料类型用来控制视图的范围，也用来控制物料的计价方式以及物料是否能够用于库存管理。也是SAP报表和分析涉及的重要对象。作为物料统计和分类的标准之一。如需新建物料类型，不能采用新建条目，只能采用复制方法，具体步骤是：

  - <1>参考已有的物料类型进行复制，但不要修改条目细节的属性值；

  - <2>对新建的物料类型进行修改，以其符合自身需求。

```JS
新建物料类型后，需要维护编码范围；维护数量/价值更新；字段状态维护。
字段参考：为字段状态服务的分组码
项目类别组：设定默认的项目类别组，用于销售项目类别确认、计划类别确认、交货项目类别确认
账户分类参考：设计会计视图的可应用的评估类，与自动记账相关
用户部门：根据业务需求选择部门内部，外部采购订单：根据业务要求选择值
计价：
价格控制：选择适用
关联科目：从列表中选择，检查输入评估类是否允许维护物料主记录中的会计数据
库存项目：不是库存项目，值更新；具有值的库存项目，数量更新和值更新
```

#### 5.Define industry Sectors ： OMS3

  Industry sectors , industry desc , Field Reference.

#### 6.Maintain company codes for material management

  SPRO > Logistics > General > MM > Basic settings > Maintain company code for MM

  CoCD,Company Name,Year,等字段信息更新保存。

#### 7.定义MRP Controller

 - SPRO > IMG > Production > MRP > Master Data > Define MRP Controllers(Plant,MRP Controller, Tel)

  SAP MRP are planned for each plant and every plant has its own MRP data.

#### 8.创建/修改/显示 物料主数据 : MM01/MM02/MM03

  - SPRO > Logistics > MMt > MMa > Material > Create general

#### 9.Create Purchasing info records : ME11/ME12/ME13

  为不同的采购类型创建采购信息记录，例如标准、分包、管道、寄售。用于将供应商和物料在工厂级别或则采购组织数据作为主数据存储。

![Info Record](/images/MM/MMInfoRecord.png)

总账科目确认：

 - 按物料级别设置科目：手动输入的科目

 - 按仓库设置总账科目：仓库定义中的科目

 - 按物料组设置总账科目：物料组定义中的科目

