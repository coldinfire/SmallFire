---

title: " SAP Number Range Object "
date: 2018-06-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  ABAP

tags: 
  - abaputils

---

### 操作说明

在 SAP 系统中，各类主数据及单据都需要使用编号进行唯一性标识，以此形成后台有着大量编号范围维护的配置操作，种类繁多。

编号范围维护分为两部分：

- 号码段的定义

- 给数据对象分配号码段

两步操作根据不同数据类型可能是在一个操作中完成，也可能分步完成。这两步操作都属于配置，但前者（号码段定义）为避免传输的目的系统（如测试系统、生产系统）发生冲突，不会自动打进传输请求包，一般手工在目的系统加以配置。

### 创建Number Range

SNRO/SNUM : (**S**AP **N**umber **R**ange **O**bject)

1.输入Tcode:SNRO 点击Create 

![Create](/images/ABAP/ABAP_NumberRange1.png)

2.输入描述，数字长队对应的Domain和Warning 百分比，保存

![Create and save](/images/ABAP/ABAP_NumberRange2.png)

3.点击Number ranges，弹出显示，修改，创建intervals界面

![Maintain Number Range Intevals](/images/ABAP/ABAP_NumberRange3.png)

4.点击Insert Interval 维护值并保存，完成创建和激活

![Maintain Number Range Intevals](/images/ABAP/ABAP_NumberRange4.png)

5.为避免造成传输目的系统的编号混乱，在配置系统无论 Client 参数如何设置，编号范围定义配置都不会自动放入传输请求中，如真有需求则可以手动加入。

![Transport](/images/ABAP/ABAP_NumberRange5.png)

如有跳号现象，可以禁用对象的 Buffer 试试.

### 维护分类

#### 类型1

每个数据对象只拥有一组号码段，内部、外部号码段选择其一或都选，定义与分配是分离的，如供应商、客户号码范围。

此类型的号码定义是相同的，但分配的操作并不一样，有些是在数据对象的属性定义处分配，例如销售订单。

此类的编号范围对象隶属于 Client，在 client 下号码段不可重复，可供一个或多个类型下的多个对象使用。例如销售凭证、外向交货单、内向交货单等共用一个编号范围对象。

#### 类型2

每个对象只拥有一组号码段，内部、外部号码段选择其一或都选，定义与分配是集成的，典型如物料主数据。

#### 类型3

数据对象下没有子类，但需要有多个号码段，如会计凭证号码范围，每个公司代码下有多个号码段。此类编号范围对象定义只归属于指定数据对象。

#### 类型4

数据对象下有多个子类，在数据对象下需定义一套完整的号码段，每个子类分配一组号码段，每组包可含 1 个内部段和 1 个外部段（两者可选其一或都含有），典型如成本控制范围。

### 维护明细表

下表中列出了常用的编号范围对象的维护信息，包括前台 TCODE、后台路径、SNRO/SNUM 维护值，以及分类。每种分类的操作可参考不同的链接。

| **模块** | **IMG** **路径**                                             | **对象**     | **TCOCE** | **SNRO/SNUM** **维护值** | **类型** |
| -------- | ------------------------------------------------------------ | ------------ | --------- | ------------------------ | -------- |
| FI       | 财务会计 (新)→财务会计全局设置 (新)→凭证→凭证号范围→条目视图中的凭证→定义条目视图的凭证编号范围 | 会计凭证     | FBN1      | RF_BELEG                 | 3        |
| CO       | 控制→一般控制→组织结构→维护成本控制凭证的编号范围            | 成本控制     | KANK      | RK_BELEG                 | 4        |
| MM       | 后勤 - 常规→物料主数据→基本设置→物料类型→定义每个物料类型的号码范围 | 物料主数据   | MMNR      | MATERIALNR               | 2        |
| MM       | 后勤 - 常规→业务合作伙伴→供应商→控制→定义供应商主记录的编号范围 <间隔> | 供应商主数据 | OMSJ      | KREDITOR                 | 1        |
| SD       | 后勤 - 常规→业务合作伙伴→客户→控制→定义和分配客户号码范围 <定义客户主数据的编号范围> | 客户主数据   | OVZC      | DEBITOR                  | 1        |
| SD       | 销售和分销→销售→销售凭证→销售凭证抬头→定义销售凭证的号码范围 | 销售订单     | VN01      | RV_BELEG                 | 1        |

### 读取函数:NUMBER_GET_NEXT

程序实例

```JS
REPORT zsnro_test.
DATA: NUMBER TYPE I.
CALL FUNCTION 'NUMBER_GET_NEXT'
EXPORTING
   nr_range_nr = '01'
   object = 'ZTEST_SNRO'
IMPORTING  
  NUMBER = NUMBER
EXCEPTIONS
  INTERVAL_NOT_FOUND = 1
  NUMBER_RANGE_NOT_INTERN = 2
  OBJECT_NOT_FOUND = 3
  QUANTITY_IS_0 = 4
  QUANTITY_IS_NOT_1 = 5
  INTERVAL_OVERFLOW = 6
  BUFFER_OVERFLOW = 7
  OTHERS = 8.
IF sy-subrc <> 0.
  MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
     WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.
Write :/ 'Next available number is: ', Number. 
```



### 其他信息

#### 相关表

- NRIV、TNRO

#### 函数组

- SNR0:  Online maint. of number ranges & groups

- SNR1:  Interface for number ranges and groups
- SNR2:  Maintenance of number range objects
- SNR3:  Number range checks, assignment & info
- SNR4:  Number ranges utility

#### TCode

| TCODE    | Desc                                 | 描述                         |
| -------- | ------------------------------------ | ---------------------------- |
| 5NZI     | Number range maintenance: RP_IRCERT  | 编号范围维护: RP_IRCERT      |
| ABNV     | Number range maintenance: FIAA-BELNR | 编号范围维护: FIAA-BELNR     |
| AO11     | Assign number range                  | 分配编号范围                 |
| AS08     | Number Ranges:Asset Number           | 号码范围：资产号码           |
| BDCP     | Number range maintenance: ALE_CP     | 编号范围维护: ALE_CP         |
| BG00     | Number Range Maintenance: BGMK_NR    | 编码范围维护：BGMK_NR        |
| BMVN     | Number Range Maintenance: DI_JOBID   | 编号范围维护： DI_JOBID      |
| BUCF     | BP Cust: Number Ranges               | BP 消费者：编号范围          |
| CFNA     | Maintain PRT number range: FHM_CRFH  | 维护 PRT 编号范围： FHM_CRFH |
| CMTCUS22 | Maintain number range for CM product | 维护 CM 产品的号码范围       |
| CMTCUS32 | Maintain number range for CM folder  | 维护 CM 文件夹的号码范围     |
| CMTCUS42 | Maintain number ranges for Baseline  | 维护起点的号码范围           |
| FBN1     | Accounting Document Number Ranges    | 科目凭证号码范围             |
| FNS1     | Collateral number range              | 附属编号区间                 |
| FOV0     | Rental agreement number range        | 租用协议编号范围             |
| FOW0     | Real Estate application number range | 不动产应用的数据范围         |
| IN20     | Object link number ranges            | 对象连接号码范围             |
| IP22     | Maintain number range: OBJK_NR       | 维护编号范围：OBJK_NR        |
| KEN2     | Maint. number ranges: CO-PA planning | 维护号吗范围: CO-PA 计划     |
| OGS9     | Generate ADP number ranges           | 生成 ADP 编号范围            |
| OHX3     | Maintain number ranges for 3PR       | 维护 3PR 的编号范围          |
| OIL5     | Equipment number ranges              | 设备编号范围                 |
| OION     | Order number ranges                  | 订单编号范围                 |
| OMH6     | Number Ranges for Purch. Documents   | 采购凭证的号码范围           |
| QCCN     | QM standard number ranges            | 质量管理标准码范围           |
| QS29     | Maintain characteristic number range | 维护特性编号范围             |
| QS39     | Maintain method number range         | 编号范围维护方式             |
| VB(1     | Rebate number ranges                 | 回扣号范围                   |
| VN07     | Maintain number range for shipments  | 维护装运的编号范围           |
| WC64     | Catalog code number ranges           | 类别代码编号范围             |
| WTNR     | w/tax certificate number range       |                              |

 
