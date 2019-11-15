---
title: "BDC屏幕录制"
date: 2018-09-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BDC

---





## 定义BDC (Batch Data Communication)

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

- 获取源数据：一般情况下，在进行传输之前要把数据放入内表
  - 从系统内部获取
  - 从系统外部获取

-  通过Tcode:**SHDB**录制工具录制事务，生成BDC程序框架

  - 可以把保存号的程序导出到本地文件
  - 让系统自动生成代码

- 数据转换：把要输入的数据转换为BDCDATA的格式。循环内表，把内表字段赋值给BDCDATA

-  执行：该程序可通过批量输入或则调用事务进行数据传输，填充BDC表作为输入数据集

  - 如有需要，可以调整修改已生成的BDC程序代码

  - CALL TRANSACTION

    
  
    ```JS
    CALL TRANSACTION <TCode> USING <BDCDATA>
    				         MODE <CTUMODE>
    				         UPDATE <CPUDATE>
    				         MESSAGES INTO <MESSTAB>.
                    
    TCode:相应的事务代码
    BDCDATA:需要的BDC传入数据
    MODE:A-Show all dynpros
    	 E-Show dynpro on error only
         N-Do not display dynpro
    UPDATE:S-Synchronously
    	   A-Asynchronously
    	   L-Local
    MESSAGE:用于存放消息
    ```
  

### 录制，更新

使用SHDB/SM35事务录制BDC，SE35 Upoload BDC

<1>SHDB：查看创建过的BDC信息，创建 New Recording

![SHDB](/images/ABAP/BDC1.png)

- 输入Record Name,和需要进行BDC录制的TCode,然后执行，程序会自动跳到输入的TCode界面。

  ![SHDB](/images/ABAP/BDC2.png)

- 根据需求进行对应的录制，完成后保存或则返回结束录屏。录制中不要乱动键盘,尽量使用鼠标点击。

  ![SHDB](/images/ABAP/BDC4.png)

<2> 选择记录，创建程序，放到本地。记录的所有东西都保存在程序中了

![SHDB](/images/ABAP/BDC3.png)

<3> 也可以通过导出BDC Record,然后在其他Client导入BDC,再创建程序。

<4> 对系统自动生成的程序进行修改，达到想要的目的   	

```js
** bdc attribute define
DATA:GT_BDCDATA        TYPE TABLE OF BDCDATA,  "执行的参数传递表
     GS_BDCDATA        TYPE BDCDATA,
     GT_MSG            TYPE TABLE OF BDCMSGCOLL,"返回执行结果
     GS_MSG            TYPE BDCMSGCOLL.
DATA LS_CTUPARAMS LIKE CTU_PARAMS.   "ctuparams LIKE ctu_params
  CLEAR LS_CTUPARAMS.
  LS_CTUPARAMS-DISMODE  = 'N'.
  LS_CTUPARAMS-UPDMODE  = 'S'.
  LS_CTUPARAMS-RACOMMIT = 'X'.
  
perform open_group.
LOOP AT <DYN_TABLE> ASSIGNING <DYN_WA>.
	CLEAR GT_BDCDATA.
    PERFORM FRM_BDC_DYNPRO USING  'SAPLCOKO1'        '0110'.
    PERFORM FRM_BDC_FIELD  USING: 'BDC_CURSOR'       'CAUFVD-AUFNR',
                                  'BDC_OKCODE'       '/00',
                                  'CAUFVD-AUFNR'     L_AUFNR,
                                  'R62CLORD-FLG_OVIEW' 'X'.

    PERFORM FRM_BDC_DYNPRO USING  'SAPLCOKO1'        '0115'.
    PERFORM FRM_BDC_FIELD  USING: 'BDC_OKCODE'       '=BU',
                                  'BDC_CURSOR'       'CAUFVD-GSTRS',
                                  'CAUFVD-GLTRS'     L_ENDDA_S,
                                  'CAUFVD-GLUZS'     '23:40',
                                  'CAUFVD-GSTRS'     L_BEGDA_S,
                                  'CAUFVD-GSUZS'     '23:01',
                                  'CAUFVD-TERKZ'     '3' .

    CLEAR GT_MSG.
    CALL TRANSACTION 'CO02' USING GT_BDCDATA
                   OPTIONS FROM LS_CTUPARAMS
                   MESSAGES INTO GT_MSG.

ENDLOOP.

perform close_group.
perform close_dataset using dataset.
```

- /SAPLSETB 0230 X . 表示数据表选择窗口

- /1BCDWB/DB 1000 X. 表示数据查询窗口

- /1BCDWB/DB 0101 X. 表示数据录入窗口

- BDC_OKCODE = 'SAVE' 表示单击‘保存’按钮

- SPFLI-CARRID NG 表示窗口字段 'SPFLI-CARRID' 值为 'NG'

#### 有两个固定表

*       Batch inputdata of single transaction：输入数据表`data: BDC_DATA like bdcdata occurs 0 with header line.`
*       Messages of call transaction：返回信息`data: MESSAGE like bdcmsgcoll occurs 0 with header line.`

#### 两个固定Form

尽量不要对这两个form中的内容进行修改。

- bdc_dynpro:指定bdc_dynpro的实参，告知系统dialog程序名称；以及Screen number

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

- bdc_field：指定bdc_field的实参，告知系统把光标放在哪个字段

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
 CALL TRANSACTION 'COOIS' USING bdcdata 
 						OPTIONS FROM gs_ctu_params
                        MESSAGE INTO MESSTAB.
```

(2)执行类的录屏和上面同样的方法生成程序。然后选择需要的代码段。不需要的可以注释，或者删除。

