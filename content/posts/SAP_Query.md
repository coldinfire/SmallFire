---
title: "SAP Query Tutorial"
date: 2020-06-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - SAPbusiness

---

## SAP Query Tutorial in SAP: SQ01, SQ02, SQ03

ABAP Query用于创建 SAP 系统中尚不存在的Report。 它是为几乎不了解 ABAP 编程的用户而设计的。 ABAP Query 为用户提供了广泛的方式来定义报告和创建不同类型的报告，例如基本报表，统计信息等。

SQ03、SQ02、SQ01，这三个事务码是按从大到小的顺序依次操作的：

- SQ03创建User Group，为特定Query限制用户组以及用户权限控制
- SQ02创建Infoset，可以关联表、确认字段，可以在SQ02的附加中直接定义附加字段或写代码
- SQ01创建Query，可以确认字段分类勾选（屏幕条件与Report字段列表），完成后可以分配T-code大家共用

#### ABAP Query由四部分组成：

- Queries
- InfoSets
- User Groups
- Translation Of Query

## User Groups - SQ03

创建一个逻辑组的用户组，将向其分配InfoSet查询。

![SQ03](/images/SAPUtils/SAP_Query_0.png)

在同一应用程序中工作的用户被分配到相同的用户组。实际上，谁在用户组中定义了Query都没有关系。分配给用户组的每个用户都可以执行已定义的Query。

用户组中的用户必须具有必要的权限，才能更改或重新定义Query。系统中的每个用户都可以分配给多个用户组。

## InfoSets - SQ02

InfoSets是数据源的特殊视图。Info Sets描述了可以在查询中报告数据源的哪些字段。可以将一个Info Set分配给多个角色或用户组。

- 通过创建Infoset并将其分配给角色或用户组，系统管理员可以确定各个应用程序部门或最终用户能够使用SAP Query生成的报告范围。
- 最终用户只能使用与其特定区域相关的那些InfoSet（由分配给他们的角色或用户组指定）。

SAP 数据库具有多个表，用于存储事务和主数据，在创建Query时要选择所有此类字段实际上是不可行的。因此，在开始创建Query之前，需要创建 InfoSet。

### Step 1:添加InfoSet Table

SQ02创建InfoSet。

![SQ02](/images/SAPUtils/SAP_Query_1.png)

可以选择使用多表联接、从单个表直接读取、借助逻辑数据库三种方法来创建InfoSet；注意添加表不能添加象 BSEG 一样的簇表。

![SQ02](/images/SAPUtils/SAP_Query_2.png)

在InfoSet 初始界面可以添加和删除表，维护表之间的联接关系。

![SQ02](/images/SAPUtils/SAP_Query_3.png)

添加两个Table:LFB1，LFBK。你还可以使用别名表，当一个查询重复用到同一个表时，可使用别名表(Alias)。

![SQ02](/images/SAPUtils/SAP_Query_4.png)

### Step2:建立InfoSet

Query需要使用的表添加完成后，点击上图中的Infoset按钮，生成InfoSet。

![Filed Group](/images/SAPUtils/SAP_Query_5.png)

Data Fields包含选择的表的所有字段信息，Field Group包含了报表输出或选择屏幕所需的字段；点击“Join”可以跳转到初始设置界面。

![Filed Group](/images/SAPUtils/SAP_Query_6.png)

将Data Fields的字段拖到相关的Field Group中以在后续Report设计中使用。

![Filed Group](/images/SAPUtils/SAP_Query_7.png)

在Field Group中选择Field，可以更改在“输出”上显示的字段的文本。

![Filed Group](/images/SAPUtils/SAP_Query_8.png)

创建InfoSet后，需要通过单击"Generation''图标来生成它。对InfoSet所做的任何更改，都需要每次重新生成。

![Filed Group](/images/SAPUtils/SAP_Query_9.png)

### Step3:Extras 添加附加内容

按 “Extras” 按钮可增加附加 Table 和字段。

![Filed Group](/images/SAPUtils/SAP_Query_25.png)

在此附加 Table 和上一步的添加信息集表有什么不同？在添加信息集表时实际上各表是存在关联关系的，如果需要从某个不大能相互关联的表中取得一个字段，可直接使用附加字段，然后通过代码获附加字段需要的值。

在 Query 中允许增加 ABAP 代码，当存在附加表和附加字段时尤其重要。

*附加字段处理*：依次将各表所需字段添加到对应字段组，并将附加字段全部填加到附加字段组。

#### 为附加字段添加代码

在这里编写代码可以使用Infoset中的任何表来编写逻辑：

![Filed Group](/images/SAPUtils/SAP_Query_26.png)

编写代码完毕后，就可以生成InfoSet了。

### Assigning to User Group

将创建好的Infoset分配到对应的用户组，点击Role/User Group Assignment 选择对应的User Group。

![Role/User Group Assignment](/images/SAPUtils/SAP_Query_10.png)

选择需要分配的User Group，可以分配给多个，勾选并保存。

![Role/User Group Assignment](/images/SAPUtils/SAP_Query_11.png)

## Queries - SQ01

一旦创建了Infoset并将其分配给用户组，就需要设计Query基本列表。

最终用户或则模块顾问使用Query组件来维护Query。 可以创建Query，更改Query和执行Query。 

### Step 1:SQ01 创建

同过Tcode：SQ01创建一个Query.

我们需要首先选择需要设计Query的用户组。单击图标选择用户组。

![SQ01](/images/SAPUtils/SAP_Query_14.png)

选择Query Area。

- Standard Area – 特定Client的Query，在其他Client看不到。不会创建工作台请求，可使用RSAQR3TR进行传输

- Global Area – Queries 是跨Client的，但是只能在开发Client进行编辑

![SQ01](/images/SAPUtils/SAP_Query_13.png)

![SQ01](/images/SAPUtils/SAP_Query_15.png)

输入Query名称，然后单击创建选项。 弹出选择框，选择已创建的Infoset。 

![SQ01](/images/SAPUtils/SAP_Query_12.png)

### Step2:维护详细信息

在下一个屏幕中给出Query的描述。指定输出长度，然后从“其他处理选项”框中选择处理选项。

![Query Details](/images/SAPUtils/SAP_Query_16.png)

数据可以以各种格式显示，例如表格，下载到文件以及以Word显示。

### Step3:选择要显示的字段和Query的处理显示数据方式

点击InfoSet Query，选择所需的输出/选择字段。

![InfoSet Query](/images/SAPUtils/SAP_Query_17.png)

在InfoSet Query界面设计选择界面和对应的输出报表界面，并选择Query的处理和显示数据方式。

![InfoSet Query](/images/SAPUtils/SAP_Query_18.png)

可以点击Output 进行显示界面预览。

**Query 的数据处理方式** ：可以通过3种方式处理和显示数据

- Basic List：按功能区定义的顺序显示数据（支持排序和求和）。
- Statistics：显示根据基本数据计算出的统计数字。
- Ranked List：Ranked List是统计的一种特殊化。

一个Query可以具有一个基本列表，最多9个统计信息和最多9个排名列表。

### Step4:完成上述所有选项后，保存Query和执行Query

完成输入界面和输出界面字段显示设计后，点击保存按钮，保存数据。

![InfoSet Query](/images/SAPUtils/SAP_Query_19.png)

回到SQ01界面，点击执行按钮，执行Query。

![Query](/images/SAPUtils/SAP_Query_20.png)

Report 选择界面，输入查询条件并点击执行按钮。

![Query](/images/SAPUtils/SAP_Query_21.png)

显示执行结果Report。

![Query](/images/SAPUtils/SAP_Query_22.png)

### Step5:生成程序

点击Query->More functions->Generate program生成程序。

![Query Menu](/images/SAPUtils/SAP_Query_41.png)

### Translation / Query Component

定义Query，InfoSet和User Group时会生成许多文本。这些文本以登录SAP系统时选择的语言显示。我们可以使用此组件比较文本/语言。

## 为 SAP Query 创建 T-Code

### 使用程序名创建T-Code

查询Query的程序名：SQ01 → Query → More Functions → Display Report Name

创建步骤：

SE93 输入需要创建的T-Code名称，并输入描述；在 Start Object 页签中选择第二个选项 “Program and selection screen(report transaction)” 。

![SE93](/images/SAPUtils/SAP_Query_32.png)

输入 Query 的程序名，勾选 GUI support 页卡的 “SAP GUI for windows” 后保存即可。

![SE93](/images/SAPUtils/SAP_Query_35.png)

通过程序名创建事务代码，是一种十分方便的方式，但它存在一定的风险，因为在不同的 System 中，两个不同的 Query 的程序名有可能相同，那么程序在系统中传输的时候，有可能产生错误。

### 使用参数创建 TCode

SE93 输入需要创建的T-Code名称，并输入描述；在 Start Object 页签中选择第5个选项 “Transaction with Parameters(parameter transaction)” 。

![SE93](/images/SAPUtils/SAP_Query_36.png)

在 Default Values for 页签下，Transaction 字段填入 “START_REPORT”，并勾选 “Skip Initial Screen”。

![SE93](/images/SAPUtils/SAP_Query_37.png)

使用此种方法创建 Query 的 T-Code，需要填入Query所属的UserGroup 以及QueryName 等 3 个字段，以及对应关系。

- D_SREPOVARI-REPORTTYPE：AQ
- D_SREPOVARI-REPORT：UserGroup + 空格 （ UserGroup 与空格相加应为 12 位 ） + G（ G 应为第13位，代表 Global Area ）
- D_SREPOVARI-EXTDREPORT：QueryName
- 如有必要，也可以为事务代码指定变式：D_SREPOVARI-VARIANT。

![SE93](/images/SAPUtils/SAP_Query_38.png)

![SE93](/images/SAPUtils/SAP_Query_39.png)

## Query 传输

### Step1:传SQ03的配置到包

选择要传的User Group:"ZTEST_QUERY"，点击 User group->Change Package。

![SQ03](/images/SAPUtils/SAP_Query_27.png)

选择自己需要保持的Package。

![SQ03](/images/SAPUtils/SAP_Query_28.png)

创建一个新的请求号，填写请求号描述，点击保存。

![SQ03](/images/SAPUtils/SAP_Query_29.png)

### Step2:传SQ02的配置到包

选择要传的InfoSet:"ZVENDOR_DATA"，点击Infoset->More functions->Change Package。

![SQ02](/images/SAPUtils/SAP_Query_30.png)

然后选择上一步创建或则选择的请求号，保存。

### Step3:传SQ01的配置到包

选择要传的Query:"ZVENDOR"，点击Query->More functions->Change Package。

![SQ01](/images/SAPUtils/SAP_Query_31.png)

然后选择上一步创建或则选择的请求号，保存。

### Step4:传SE93创建的T-code的配置到包

选择要传的T-code:"ZQUERY_V"，点击Goto->Object directory entry。

![SQ01](/images/SAPUtils/SAP_Query_34.png)

进入界面后，点击修改按钮，并维护需要配置的包。

### Step5:传输请求SE09

通过T-code：SE09查到已创建的请求号。

![SQ01](/images/SAPUtils/SAP_Query_33.png)

展开折叠，查看验证是否所传信息完整无误。如果没有问题，可以进行传输。

### Step6:正式系统调试

传输完成后，在正式系统配置新的事务码。

传输完成后，在正式系统配置新的事务码。

- 正式系统重新配置SQ03，分配用户；

- 正式系统运行SQ02，SQ01，执行测试，生成程序。

- 运行T-code测试Query。

![SQ01](/images/SAPUtils/SAP_Query_40.png)

**调用程序： RSAQR3TR 执行Query的传输。**

## SAP Query – Info Set – (LDB)逻辑数据库

LDB是ABAP程序的一种特殊类型，它组合了某些相关数据库表的内容，并检索了一些相关数据并将其提供给应用程序。LDB通常由诸如结构，选择和数据库程序之类的组件组成。

简单地说，逻辑数据库可以称为表簇。

使用T-code：SE36查看LDB：

![SE36](/images/SAPUtils/SAP_Query_23.png)

![SE36](/images/SAPUtils/SAP_Query_24.png)

#### 会计相关的Logical Database

| Logical Database | Desc                       | Logical Database | Desc                               |
| ---------------- | -------------------------- | ---------------- | ---------------------------------- |
| ADA              | Assets Database            | CKC              | Order BOM                          |
| AUK              | Settlement documents       | CKW              | Costing run: Material Selection    |
| BRF              | Document Database          | CPK              | Cost Centers – Plan Data           |
| BRM              | Accounting Documents       | KDF              | Vendor Database                    |
| CEK              | Cost Centers – Line Items  | PAK              | CO-PA Segment Level and Line Items |
| CIK              | Cost Centers – Actual Data | SDF              | G/L Account Database               |
| CKA              | Costing                    |                  |                                    |

