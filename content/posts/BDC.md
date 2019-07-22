---
title: "BDC屏幕录制"
date: 2018-09-26T19:15:42+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BDC

---





## 定义 Batch data communication

  BDC：SAP常用的一种数据传输方法。用于一些数据量大，但对速度要求不高的数据传输.  

使用步骤：

- 把外部的数据源(Txt,Excel 等)读进 internal table 或者用 do enddo 循环。
- 在循环里，把用 SHDB 记录的步骤重复执行 N 次，将数据读入到一个叫做 “BDCData” 的 Internal Table。
- 将BDCData表中数据通过 call transaction ‘XXXX’ using bdc…… 这个命令来真正的 commit 动作或者 call function 'BDC_Insert' 再建立一个 session。并把执行的结果返回给 messtab 这个 Internal Table。

两种通用写法：

- <1> Call Transaction:直接调用BDC进行数据批量导入。
- 优点：方便快捷，程序处理方便
  
- 缺点：日志管理能力差，需要自己建立透明表来维护数据。
  
- <2> BDC Insert:不直接运行，而是将BDC程序生产Session，间接运行的一种方法。
  - 优点：通过TcodeSM35可以进行运行管理和日志管理，方便查错。
  - 缺点：方法相对来说比较繁琐。

功能

- 在程序中调用Function 'BDC_INSERT'把BDCDATA生成Session.

- 程序RSBDCSUB是执行Session的专用程序，要建立相应的VARIANT,供JOB中使用
         

- 建立BATCH JOB来执行RSBDCSUB，从而实现Session自动执行的目的；不使用REBDCSUNB和JOB，每次手工在SM35中执行Session也可以。

### 基本流程

- <1> 通过事务录制工具录制事务，生成BDC程序框架
- <2> 如有需要，可以调整修改已生成的BDC程序代码

- <3> 该程序可通过批量输入或则调用事务进行数据传输，填充BDC表作为输入数据集

### 录制，更新

使用SHDB/SM35事务录制BDC，SE35 Upoload BDC

- <1>SHDB；输入NAME,T-Code,然后执行，最后用保存或则返回来结束录屏。录制中不要乱动键盘,尽量使用鼠标点击。

![SHDB](/images/ABAP/BDC1.png)

![SHDB](/images/ABAP/BDC2.png)

- <2> 选择记录，创建程序，放到本地。记录的所有东西都保存在程序中了

![SHDB](/images/ABAP/BDC3.png)

- <3> 处理程序达到想要的目的   	

```JS
start-of-selection.
perform open_dataset using dataset.
perform open_group.
do.
read dataset dataset into record.
if sy-subrc <> 0. exit. endif.
perform bdc_dynpro      using 'SAPMM07R' '0560'.
perform bdc_field       using 'BDC_CURSOR'
                              'RM07M-RSNUM'.
perform bdc_field       using 'BDC_OKCODE'
                              '/00'.
perform bdc_field       using 'RM07M-RSNUM'
                              record-RSNUM_001.
perform bdc_field       using 'XFULL'
                              record-XFULL_002.
perform bdc_dynpro      using 'SAPMM07R' '0521'.
perform bdc_field       using 'BDC_CURSOR'
                              'RESB-ERFMG(01)'.
perform bdc_field       using 'BDC_OKCODE'
                              '/00'.
perform bdc_field       using 'RESB-ERFMG(01)'
                              record-ERFMG_01_003.
perform bdc_field       using 'DKACB-FMORE'
                              record-FMORE_004.
perform bdc_dynpro      using 'SAPLKACB' '0002'.
perform bdc_field       using 'BDC_CURSOR'
                              'COBL-KOSTL'.
perform bdc_field       using 'BDC_OKCODE'
                              '=ENTE'.
perform bdc_dynpro      using 'SAPMM07R' '0510'.
perform bdc_field       using 'BDC_CURSOR'
                              'RESB-ERFMG'.
perform bdc_field       using 'BDC_OKCODE'
                              '=BU'.
perform bdc_field       using 'RESB-CHARG'
                              record-CHARG_005.
perform bdc_field       using 'RESB-ERFMG'
                              record-ERFMG_006.
perform bdc_field       using 'RESB-BDTER'
                              record-BDTER_007.
perform bdc_field       using 'RESB-XWAOK'
                              record-XWAOK_008.
perform bdc_field       using 'DKACB-FMORE'
                              record-FMORE_009.
perform bdc_dynpro      using 'SAPLKACB' '0002'.
perform bdc_field       using 'BDC_CURSOR'
                              'COBL-KOSTL'.
perform bdc_field       using 'BDC_OKCODE'
                              '=ENTE'.
perform bdc_transaction using 'MB22'.
enddo.
perform close_group.
perform close_dataset using dataset.
```

#### 程序主要逻辑

open dataset. "读取外部数据源

do. “循环

​    perform 填充 BDCDATA 子程序.

​    perform bdc_transcation.

endo.

Close dataset.  "关闭读取

### 固定声明

#### 有两个固定表

*       Batch inputdata of single transaction：输入数据表
*       data: abc like bdcdata occurs 0 with header line.
*       Messages of call transaction：返回信息
*       data: def like bdcmsgcoll occurs 0 with header line.

#### 两个固定Form

尽量不要对这两个form中的内容进行修改。

- bdc_dynapro

  ```JS
  *----------------------------------------------------------------------*
  *        Start new screen                                              *
  *----------------------------------------------------------------------*
  FORM BDC_DYNPRO USING PROGRAM DYNPRO.
    CLEAR BDCDATA.
    BDCDATA-PROGRAM  = PROGRAM.
    BDCDATA-DYNPRO   = DYNPRO.
    BDCDATA-DYNBEGIN = 'X'.
    APPEND BDCDATA.
  ENDFORM.
  ```

- bdc_field

  ```JS
  *----------------------------------------------------------------------*
  *        Insert field                                                  *
  *----------------------------------------------------------------------*
  FORM BDC_FIELD USING FNAM FVAL.
  IF FVAL <> NODATA.
    CLEAR BDCDATA.
    BDCDATA-FNAM = FNAM.
    BDCDATA-FVAL = FVAL.
    APPEND BDCDATA.
  ENDIF.
  ENDFORM.
  ```

  

(1) 跳转类的：开头定义的地方加两个变量
​        

```JS
DATA:BDCDATA LIKE BDCDATA  OCCURS 0 WITH HEADER LINE. 存录屏过程中的变量及常量等。
DATA:MESSTAB LIKE BDCMSGCOLL OCCURS 0 WITH HEADER LINE. 
DATA:GS_CTU_PARAMS TYPE CTU_PARAMS. 调事务代码时带的一些参数，是否前台执行，报错停止等。
从程序中选一些dynpro和field的BDC行，不需要的字段或则屏幕，可以直接删除对应的代码。
 CLEAR bdcdata[].
  gs_ctu_params-updmode = 'S'.
  gs_ctu_params-dismode = 'E'.
  gs_ctu_params-defsize = ''."设置窗口非默认大小
  "调用BDC执行 T-code COOIS 显示订单抬头
 CALL TRANSACTION 'COOIS' USING bdcdata OPTIONS FROM gs_ctu_params.
```

(2)执行类的录屏和上面同样的方法生成程序。然后选择需要的代码段。不需要的可以注释，或者删除。

