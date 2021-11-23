---
title: " MIGO 收货后自动打印功能 "
date: 2020-05-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### MIGO 简介

内向交货的收货流程是供应链的重要组成部分。此过程包括创建采购订单后的步骤：通知，收货，随后的货物下达以及所订购货物的收货过帐。

根据未清采购订单数量处理好收货。使用称为移动类型的三位数密钥系统，将货物接收到工厂中并移动到其他存储位置。有助于区分货物移动。

好的收据通过以下方式记录货物或物料的向内移动：

- 根据订单验证收货

- 评估采购订单中的物料

- 创建物料和会计凭证以记录事件

- 为发票校验提供依据

### Goods receive 的结果

物料凭证的创建

- 成功过帐收货后，系统会自动创建一个物料凭证，以作为货物移动的凭证。

创建会计凭证

- 与物料凭证并行，系统还会创建会计凭证。会计凭证包含移动所需的过帐行（用于对应帐户）。

### 物料凭证的创建 - 选项和功能：

收货完成后，系统将根据移动类型和输出条件类型在 MIGO 屏幕的明细数据选项卡中创建物料凭证。该凭证将包含收货日期，当前日期，工厂详细信息和描述，供应商编号的详细信息。用户签发的地址和地址，项目编号，材料编号，描述，数量和详细信息。

SAP 默认情况下会维护本地打印机设备，以获取特定工厂的物料收据单的打印输出，以及打印工厂参数中默认条件类型的相应存储位置。

但是我们可以根据特定的输出条件类型，为特定工厂和相应的存储位置配置默认值以外的输出设备。

本文档说明了根据为 MIGO 维护的输出类型来配置输出设备以进行物料收据单打印输出的过程。

#### Step1:维护输出条件类型

SPRO路径：Materials Management->Inventory Management and Physical Inventory->Output Determination->Maintain Conditions 

Tcode：MN21/MN22/MN23

为了维护采购订单类型的收货对应的输出条件类型，我们可以根据所需的输出将条件类型维护为 WE01，WE02 和 WE03。例如，如果需要有个人有效收据单，则应选择 “个人收据” 的 “ WE01” 选项。同样，如果要求合并打印支票，则应选择条件类型为 “WE03”。

在本文中使用了合并打印：WE03 来配置输出类型条件。使用TCode执行：MN22（更改输出条件类型）

输入输出类型条件 WE03 : GR Note Vers.3

![MN22](/images/MMGR/MIGO_OUTPUT_DEVICES1.png)

在下个窗口，输入对应的参数并执行

- Transaction/Event Type：Select the option of “WE”- Good receipt for purchase order
- Version for printing GR/GI slip：Select the option of Print Version: 3- Collective Version
- Printing of document item：Select the Print Item option of Material Document printout: 1

![MN22](/images/MMGR/MIGO_OUTPUT_DEVICES2.png)

在新窗口选择Condition Recs输入一行数据

![MN22](/images/MMGR/MIGO_OUTPUT_DEVICES3.png)

点击Communication按钮，在新页面中维护打印机名称和其他配置
![MN22](/images/MMGR/MIGO_OUTPUT_DEVICES4.png)

#### Step 2：通过工厂 / 存储位置确定打印机

下一步是确定打印机，在步骤 1 中为各个工厂和打印工厂参数中的相应存储位置维护的输出条件类型。

用户可以通过三种方式确定打印机：

- 通过工厂 / 存储位置确定打印机
- 按工厂 / 存储位置 / 用户组确定的打印机 - 根据用户主记录中定义为参数 ND9 的用户组。
- 按输出类型 / 用户确定打印机

在本文档中，我们使用了第一个选项，即按工厂 / 存储位置确定打印机以进行配置。

SPRO 路径：Materials Management–>Inventory Management and Physical Inventory->Output Determination->Printer Determination->Printer 
Determination by Plant/Storage Location

Tcode: OMJ3

输入Tcode OMJ3 或则使用SPRO路径，点击"New Entries"创建一条新数据

![OMJ3](/images/MMGR/MIGO_OUTPUT_DEVICES5.png)

在输入字段中输入在步骤 1 中维护的条件类型以及相应的工厂和存储地点详细信息。另外，输入要配置的所需输出设备，然后选中 “立即打印” 复选框，然后按保存按钮。

![OMJ3](/images/MMGR/MIGO_OUTPUT_DEVICES6.png)

打印工厂参数将被保存并显示在打印工厂参数的主列表中。为了将变更从开发系统转移到生产，用户必须在开发系统中创建运输请求以将变更从开发系统转移到生产系统。

#### 步骤 3：检查可修改字段-Collective slip TCode : OMJN

在Collective Slip字段中检查字段名称：RM07M-WVERS3，并根据要求选择相应的单选按钮，例如必输，突出显示，显示或隐藏该字段。根据需求选择。

![OMJN](/images/MMGR/MIGO_OUTPUT_DEVICES7.png)

#### 步骤 4：检查 MIGO / GOOD 收货单中的物料单

一旦按上述步骤详细配置了步骤 1-3，用户便可以根据需求执行采购订单的 MIGO / Goods receipt。为了便于理解，下面以 MIGO 循环为例进行了详细介绍： 

输入数据：

- Purchase Order Number
- Quantity
- Storage Location
- Steps 1 - 3 confirgured

处理 MIGO 并执行Post。在Posting完成后，将生产物料凭证编号。

再次打开Tcode：MIGO 并选择显示物料凭证选项，然后输入物料凭证编号，然后按执行按钮显示该凭证信息。

![MIGO](/images/MMGR/MIGO_OUTPUT_DEVICES8.png)

在详细数据字段中，单击输出选项卡，然后单击显示输出。

将打开一个新窗口，其中将以绿色或黄色列出您的输出状态，输出类型，描述，语言，处理日期和时间详细信息。

![MIGO](/images/MMGR/MIGO_OUTPUT_DEVICES9.png)

#### 步骤 5：使用 MIGO 打印错误（可选）

通过维护输出条件类型：WE03 的发送时间，用户将能够从 MIGO 自身中提取重要单据 / 有效收据单的打印内容。

SPRO 路径：Materials Management->Inventory Management and Physical Inventory->Output Determination->Maintain Output Types

TCode：NACE

选择输出类型为 “WE03”，然后单击其中的显示选项。

![NACE](/images/MMGR/MIGO_OUTPUT_DEVICES10.png)

在显示输出条件类型时：WE03，选择默认值。在这种情况下，将调度时间的默认值更改为从具有定期计划作业的发送立即发送（保存应用程序时）。

因为这是默认值，并且如果我们将其更改为 WE03 条件类型，则将为所有用户维护该值，因为这是主数据。因此，不建议将调度时间默认值更改为 “立即发送（保存应用程序时）”。

![NACE](/images/MMGR/MIGO_OUTPUT_DEVICES11.png)

对于不打算立即打印输出的其他用户，这也可能会在某些时候发布错误。

为避免此错误，如果紧急需要，用户可以使用**Tcode：MB02 / MB03** 进行立即打印。

#### 结论

在确认采购订单中有关数量订单的收货时，物料凭证是重要凭证。为了跟踪入库货物，在系统中完成输入的正确记录是验证的重要部分。打印出实质性文件有助于保持在收货地点收到的货物的实物证明，并且一些供应商还要求按时交货的证明和正确的数量需要接收货物的用户签名的实质性文件。

因此，如果有任何出入，物料收货单在确认交付的货物方面起着重要的作用。