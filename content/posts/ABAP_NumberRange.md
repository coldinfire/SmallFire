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



### TCode

 - SNRO : (**S**AP **N**umber **R**ange **O**bject)
 - OYSM

#### 创建Number Range

​	1. 输入Tcode:SNRO 点击Create 

​	![Create](/images/ABAP/ABAP_NumberRange.png)

​	2. 输入描述，数字长队对应的Domain和Warning 百分比，保存

​	![Create and save](/images/ABAP/ABAP_NumberRange2.png)

​	3. 点击Number ranges，弹出显示，修改，创建intervals界面

​	![Maintain Number Range Intevals](/images/ABAP/ABAP_NumberRange3.png)

​	4. 点击Insert Interval 维护值并保存，完成创建和激活

​	![Maintain Number Range Intevals](/images/ABAP/ABAP_NumberRange4.png)

#### 读取函数

- NUMBER_GET_NEXT

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
    OTHERS = 8
  .
  IF sy-subrc <> 0.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
       WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
  
  Write :/ 'Next available number is: ', Number. 
  ```

  

相关表

- NRIV、TNRO


函数组

- SNR0:  Online maint. of number ranges & groups

- SNR1:  Interface for number ranges and groups
- SNR2:  Maintenance of number range objects
- SNR3:  Number range checks, assignment & info
- SNR4:  Number ranges utility

### 使用

如有跳号现象，可以禁用对象的 Buffer 试试



5NZI： Number range maintenance: RP_IRCERT 

ABNV： Number range maint: FIAA-BELNR 

AO11： Assign number range 
 分配编号范围 

AS08： Number Ranges:Asset Number 
 号码范围：资产号码 

BDCP： Number range maintenance: ALE_CP 
 编号范围维护: ALE_CP 

BG00： Number Range Maintenance: BGMK_NR 
 编码范围维护：BGMK_NR 

BMVN： Number Range Maintenance: DI_JOBID 
 编号范围维护： DI_JOBID 

BUCF： BP Cust: Number Ranges 
 BP 消费者：编号范围 

CFNA： Maintain PRT number range: FHM_CRFH 
 维护 PRT 编号范围： FHM_CRFH 

CMTCUS22： Maintain number range for CM product 
 维护 CM 产品的号码范围 

CMTCUS32： Maintain number range for CM folder 
 维护 CM 文件夹的号码范围 

CMTCUS42： Maintain number ranges for Baseline 
 维护起点的号码范围 

FBN1： Accounting Document Number Ranges 
 科目凭证号码范围 

FNS1： Collateral number range 
 附属编号区间 

FOV0： Rental agreement number range 
 租用协议编号范围 

FOW0： Real Estate application number range 
 不动产应用的数据范围 

IN20： Object link number ranges 
 对象连接号码范围 

IP22： Maintain number range: OBJK_NR 
 维护编号范围：OBJK_NR 

KEN2： Maint. number ranges: CO-PA planning 
 维护号吗范围: CO-PA 计划 

OGS9： Generate ADP number ranges 
 生成 ADP 编号范围 

OHX3： Maintain number ranges for 3PR 
 维护 3PR 的编号范围 

OIL5： Equipment number ranges 
 设备编号范围 

OION： Order number ranges 
 订单编号范围 

OMH6： Number Ranges for Purch. Documents 
 采购凭证的号码范围 

QCCN： QM standard number ranges 
 质量管理标准码范围 

QS29： Maintain characteristic number range 
 维护特性编号范围 

QS39： Maintain method number range 
 编号范围维护方式 

VB(1： Rebate number ranges 
 回扣号范围 

VN07： Maintain number range for shipments 
 维护装运的编号范围 

WC64： Catalog code number ranges 
 类别代码编号范围 

WTNR： w/tax certificate number range 
