---
title: "MM 物料主数据创建"
date: 2019-05-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---


### Enterprise Struture ###

1.Define Company : OX15

   - SPRO -> IMG ->Enterprise Structure -> Definition -> Financial Account -> Define Company

![Define Company](/images/MMMasterData/1.png)
![Details](/images/MMMasterData/2.png)

- 1.Enter company                     
- 2.Enter company name            
- 3.Update address
- 4.Enter country code of company 
- 5.Enter language key 
- 6.Enter local currency

2.Define Company Code : OX02

  - SPRO -> IMG ->Enterprise Structure -> Definition -> Financial Account -> Edit,Copy,Delete Company Code
![Company Code](/images/MMMasterData/3.png)
![Details](/images/MMMasterData/4.png)

3.Assign Company Code to Company : OX16

  - SPRO -> IMG ->Enterprise Structure -> Definition -> Financial Account -> Assign Company Code To Company

![Assign Demo](/images/MMMasterData/5.png)

4.Define Plant : OX10

  - SPRO -> IMG ->Enterprise Structure -> Definition -> Logistics ->General -> Define plant

![Define Plant](/images/MMMasterData/6.png)

5.Assign Plant to Company Code : OX18

  - SPRO -> IMG ->Enterprise Structure -> Assignment -> Logistics -> AssignPlant to Company Code

![Assign](/images/MMMasterData/7.png)

6.Maintain Storage Locations : OX09

  - SPRO -> IMG ->Enterprise Structure -> Definition -> Material Mast -> Maintain storage Location

![Storage Location](/images/MMMasterData/8.png)

7.Maintain Purchase Organization : OX08

   - SPRO -> IMG ->Enterprice Structure -> Definition -> Material Mast -> Maintain Purchasing Org

![Purchase Org](/images/MMMasterData/9.png)

8.Create Purchase groups : OME4

 - SPRO -> IMG ->MM -> Purchasing -> Create Purchasing Groups

![Purchasing Groups](/images/MMMasterData/10.png)

9.Assign Purchasing org to Company Code : OX01

 - SPRO -> IMG -> Enterprice Structure -> Assign -> Material Mast  -> Assign Purchase Org to Company Code
![Assign](/images/MMMasterData/11.png)


10.Assign Purchasing organization to reference purchasing org

  - SPRO -> IMG -> Enterprice Structure -> Assign -> Material Mast -> Assign Purchase Org to RPO

11.Assign Purchase Organization to Plant : OX17

  - SPRO -> IMG -> Enterprice Structure ->Assignment -> Material Mast -> Assign PO to Plant

![Org To Plant](/images/MMMasterData/12.png)

12.Assign  standard purchasing org to plant：SM30(V_001W_E)

- SPRO -> IMG -> Enterprice Structure -> Assignment -> Masterial Management -> Assign standard purchasing org to plant

  一个工厂可以指定多个采购组织，但在一些特定的业务中需要自动确定采购组织，这时就需要在后台对工厂指定唯一的采购组织，也就是标准采购组织.


###  Master Data ###
1.物料主数据视图：

  - 包含：常规，采购，销售，库存， 计划，属性，备注等视图维护。

2.Split Valuation configuration (定义全球估价类型) : OMWC

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
2.Create Material Group ： OMSF

  - SPRO -> Logistic general -> MM -> Settings for Key Fields -> Define Material Group

 物料组：是物料统计分组的标识，属于无组织机构级别，它在采购信息记录、采购订单等中使用。在创建采购订单时， 当输入物料编号，物料组就自动被带出。物料组用于内部管理，分类较细。用户可以通过物料组获取整个采购状况及当前库存的标准SAP报表。

3.Create Material Types ： OMS2

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

4.Define industry Sectors ： OMS3

  Industry sectors , industry desc , Field Reference.

5.Maintain company codes for material management

  SPRO > Logistics > General > MM > Basic settings > Maintain company code for MM

  CoCD,Company Name,Year,等字段信息更新保存。

6.Define MRP Controllers

 - SPRO > IMG > Production > MRP > Master Data > Define MRP Controllers(Plant,MRP Controller, Tel)

  SAP MRP are planned for each plant and every plant has its own MRP data.

7.Create,Change,Display Material : MM01/MM02/MM03

  - SPRO > Logistics > MMt > MMa > Material > Create general

8.Create Purchasing info records : ME11/ME12/ME13

  为不同的采购类型创建采购信息记录，例如标准、分包、管道、寄售。用于将供应商和物料在工厂级别或则采购组织数据作为主数据存储。

![Info Record](/images/MM/MMInfoRecord.png)

总账科目确认：

 - 按物料级别设置科目：手动输入的科目

 - 按仓库设置总账科目：仓库定义中的科目

 - 按物料组设置总账科目：物料组定义中的科目

