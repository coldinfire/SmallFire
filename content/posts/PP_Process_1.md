---
title: " PP 基本流程 "
date: 2019-05-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - PP

---

### MRP

MRP 执行方式分为两种：

- Material  Requirement Planning：根据当前或则将来的需求而提出对于产品数量的需求预测（针对订单生产）

- Consumption-based Planning：就消耗的计划，基于历史的消耗数据对未来的消耗数量提出预测（针对库存生产）

### 物料创建（MM01）

创建物料时的主要视图：Basic Data、Purchasing、MRP1-4、Work Scheduling。

其中需要注意的是，在 MRP1 中，MRP Type 选择 PD（表示为 MRP）；Lot Size 选择 EX（批量选择有一定的策略，EX 表示需求有多少生产多少）。

### BOM定义（CS01）

BOM（Bill of material）：物料清单，是部件完整、正式的结构化清单，反映一个成品的物料层级关系。包括每个部件的项目号、损耗、数量和计量单位。

#### 创建 BOM （CS01）

输入事物码 CS01 创建 BOM：

![CS01](/images/PP/BOM_Create_1.png)

-  Alternative BOM 的意思是指，对于一个物料可以建立多个 BOM（在业务中，就相当于一个产品有多种生产方式）

在 Item 里面输入下层物料以及需要的数量：

![CS01](/images/PP/BOM_Create_2.png)

- SAP 系统中 BOM 表是单层结构，也就是说最顶层的物料只能在它的 BOM 表中创建它下面一层的物料，如果要创建完整的 BOM 可以为下层物料再创建具体的 BOM 表，或则选择 Subitem，在该 TAB 下继续创建子 BOM。 

### 新建 Capacity（CR11）

Capacity 指的是生产能力，比如人工、机器等。

输入事物码 CR11 创建 Capacity：

![CR11](/images/PP/BOM_Create_3.png)

- Capacity category：表示的是生产能力的分类，可以是 person, machine 等。

维护详细值：

![CR11](/images/PP/BOM_Create_4.png)

- Capacity Utilization：能力利用率
- No. of individual capacity：次数
- 比如上例：每天的工作时间 9 小时，休息一小时，利用率为 80%，一共五天，所以结果为：(9-1) x 0.8 x 5 = 32 H

### 创建 Work Center（CR01）

 Work Center 指的是生产过程中某种生产能力的单元，可以用來负责一个单独的工序。

参考链接：[Work Center 的维护](https://www.twblogs.net/a/5b8cc33d2b71771883352c89)

### 创建 Routing（CA01）

Routing 即工艺流程，指的是生产一个成品的流程，由多个工序 (前面设置的Work center) 组成。

![CA01](/images/PP/Routing_1.png)

这里的 From Lot Size 可以用来控制多条 Routing 的时候选择，比如小于某个量的时候用 Routing 1，否则用 Routing 2。选择界面上方的 Routing 可以进入该产品的具体流程，如果该产品有多个 Routing，则结果会出现多行数据。

界面上方有多个选项，选择 Operation 根据自己的设定输入对应的 Work Center 确定工序。输入确定后，会进入每一个工序的具体设置，要求输入 setup、machine、labor 的用时。

![CA01](/images/PP/Routing_2.png)

点击界面上方的 CompAlloc，进入产品的物料清单，点击 New Assignment，将该物料投入给某一个具体的工序。

### 产品成本估算（CK11N）

系统将根据 BOM 表为生产的成品估算成本，该成本也会在物料主数据中更新。

### 标记并发布价格（CK24）

### 创建 Planned Independent Requirement（MD61)

计划独立需求是区别于客户订单的外部需求，可以通过计划自行生产产品囤积在仓库种。

在初始界面选择计划时间区间，计划时间单位；进入计划界面后输入不同周期计划的生产成品数量。

### 运行 MRP（MD02）

执行 MD02 运行 MRP，两次确认后 MRP 计算完成。

![CA01](/images/PP/MRP_Run.png)

MRP 控制变量：

- Processing Key 的取值意义
  - NETCH：总水平的净变化；有变更总会参与 MRP 计算
  - NETPL：计划区间里的净变化；在一定计划期间内有变更的物料参与 MRP 计算
  - NEUPL：再生计划；MRP 运行之前会删除当前存在的所有计划文件，然后重新生产计划文件
- Planning Mode
  - normal mode：让系统自动判断如何处理 MRP
  - re-explode BOM and routing：当 BOM 或则 Routing 发生变化时使用该选项
  - Delete and recreate：当订单数量，交期发生变化时使用该选项

### 查看 MRP 转计划订单（MD04）

MD04 可以看到物料的需求和对应的生产计划，选中对应的 PldOrd 将其转换为 Production Order。通过点击列表旁的 Order Report 按钮查看成品对应的 BOM 展开表，将对应的半成品的 Planned order 转为 Production order，将原材料的 Planned order 转为 Production order 并记录对应的Production Order Number。

### 对原材料收货（MIGO）

根据 Purchase Order 进行原材料的收货，用于后续产品生产使用，移动类型为 101。

### 下达生产订单（CO02）

输入 Production order number，点击页面的 Realease 按钮下达订单，点击保存。

### 对生产订单发货（MB1A）

选择初始界面的 TO Order，在弹出框中选择前面已经 Release 的 Production Order（如果半成品没有库存，需要先对半成品进行生产），移动类型为 261。

### 对生产订单收货（MIGO）

根据 Production Order 进行成品的收货，移动类型为 101，确认收货过账。收货过账完成后，可以通过 MMBE 查看物料对应的库存结果。