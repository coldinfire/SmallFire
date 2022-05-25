---
title: " BDC 屏幕录制 "
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

### 使用 BDC 的基本流程

#### Step1：获取源数据

一般情况下，在进行传输之前要把外部的数据源(Txt,Excel 等)放入内部表中。

- 从系统内部获取
- 从系统外部获取

#### Step2：生成BDC程序框架

通过事物码 SHDB 录制工具录制事务，生成程序框架。

- 可以把保存号的程序导出到本地文件
- 让系统自动生成代码

#### Step3：数据转换

把要输入的数据转换为 BDCDATA 的格式。循环内表，把内表字段赋值给 BDCDATA 作为输入数据集

- 如有需要，可以调整修改已生成的BDC程序代码

#### Step4：执行 BDC

*Method 1：Call Transaction* 直接调用 BDC 进行数据批量导入。

- 优点：方便快捷，程序处理方便
- 缺点：日志管理能力差，需要自己建立透明表来维护数据

CALL TRANSACTION 语法：

```ABAP
CALL TRANSACTION <TCode> USING <BDCDATA>
                         MODE  <CTUMODE>
                         UPDATE <CPUDATE>
                         MESSAGES INTO <MESSTAB>.
```

- TCode：需要执行的事务代码

- BDCDATA：BDC 的传入数据

- MODE：BDC 执行时的显示模式

  | Mode | Description                                                  |
  | :--- | :----------------------------------------------------------- |
  | A    | (默认)显示所有输入屏幕，如果在 bdc_tab 中包含该屏幕的功能码，则会出现小窗口显示这个功能码 |
  | E    | 只有在出现错误时才显示屏幕，用户可以修正数据，修正后程序可以继续处理 |
  | N    | 不显示屏幕的静默模式。如果到达被调用事务的断点，则系统处理终止 |
  | P    | 不显示屏幕的调试模式。如果到达被调用事务的断点，则系统自动转到ABAP调试器，主要用于调试过程 |

- UPDATE：更新模式

  | Mode | Description                                                  |
  | :--- | :----------------------------------------------------------- |
  | S    | Synchronously (这种方式比较适 合于数据一致性要求比较高，多个不同事务码的连续处理。) |
  | A    | Asynchronously（这种 方式比较适合于用一个事务码大量更新指定数据，比如维护主数据等。） |
  | L    | 数据更新在主程序所在的进程中完成，主程序必定等到被调用事务完成才继续执行。） |

- MESSAGES：用于存放 BDC 执行过程中的消息

*Method2：BDC_INSERT* 不直接运行，而是将 BDC 程序生成 Session，间接运行的一种方法。

- 优点：通过Tcode - SM35可以进行运行管理和日志管理，方便查错。
- 缺点：方法相对来说比较繁琐。

使用步骤：

- 在程序中调用 Function 'BDC_INSERT' 把 BDCDATA 生成 Session。

- 程序 RSBDCSUB 是执行 Session 的专用程序，要建立相应的 VARIANT，供 JOB 中使用。

- 建立 BATCH JOB 来执行 RSBDCSUB，从而实现 Session 自动执行的目的；不使用 REBDCSUNB 和 JOB，每次手工在 SM35 中执行 Session 也可以。

### 录制，更新 BDC

使用事务码 SHDB/SM35 录制 BDC，SE35：Upoload BDC。

#### 创建 BDC

输入事物码 SHDB 创建 New Recording，也可以查看创建过的 BDC 记录。

![SHDB](/images/ABAP/BDC1.png)

输入需要创建的 Recording Name 和需要进行录制的 TCode 然后执行。程序会自动跳到输入的 TCode 界面。

![Create Recording](/images/ABAP/BDC2.png)

根据实际业务需求进行对应的录制，完成后保存或则返回结束录屏。录制中不要使用键盘操作，尽量使用鼠标点击。

![Record Result](/images/ABAP/BDC4.png)

#### 创建程序

 选择录制好的 BDC，创建程序，放到本地。记录中的所有信息都保存在程序中。也可以通过导出 BDC Record，然后在其他 Client 导入 BDC Record 后创建程序。

![BDC Program](/images/ABAP/BDC3.png)

#### 修改生成的标准 BDC

对系统自动生成的程序进行修改，达到实际业务的需求。   	

```ABAP
PARAMETERS: dataset(132) LOWER CASE DEFAULT 'Program_name'.
" BDC attribute define "
DATA:bdcdata LIKE BDCDATA OCCURS 0 WITH HEADER LINE.    "保存录屏过程中的变量及常量数据"
DATA:messtab LIKE BDCMSGCOLL OCCURS 0 WITH HEADER LINE. "记录BDC执行返回数据" 
DATA:e_msg type bapi_msg,
     lv_msg type t100-text.
DATA:ctu_params LIKE CTU_PARAMS.   "调事务代码时带的一些参数，是否前台执行，报错停止等"
CLEAR ctu_params.
ctu_params-dismode  = 'N'.
ctu_params-updmode  = 'S'.
ctu_params-racommit = 'X'.
"循环调用 BDC"
LOOP AT <DYN_TABLE> ASSIGNING <DYN_WA>.
  CLEAR :bdcdata,messtab.
  REFRESH: bdcdata,messtab.
  PERFORM BDC_DYNPRO USING  'SAPLCOKO1'        '0110'.
  PERFORM BDC_FIELD  USING: 'BDC_CURSOR'       'CAUFVD-AUFNR',
                            'BDC_OKCODE'       '/00',
                            'CAUFVD-AUFNR'     lv_aufnr,
                            'R62CLORD-FLG_OVIEW' 'X'.
  PERFORM BDC_DYNPRO USING  'SAPLCOKO1'        '0115'.
  PERFORM BDC_FIELD  USING: 'BDC_OKCODE'       '=BU',
                            'BDC_CURSOR'       'CAUFVD-GSTRS',
                            'CAUFVD-GLTRS'     lv_endda,
                            'CAUFVD-GLUZS'     '23:40',
                            'CAUFVD-GSTRS'     lv_begda,
                            'CAUFVD-GSUZS'     '23:01',
                            'CAUFVD-TERKZ'     '3' .
  CALL TRANSACTION 'CO02' USING bdcdata
                          OPTIONS FROM ctu_params
                          MESSAGES INTO messtab.
  LOOP AT messtab.
    CLEAR lv_msg.
    CALL FUNCTION 'MESSAGE_TEXT_BUILD'
      EXPORTING
        msgid  = messtab-msgid
        msgnr  = messtab-msgnr
        msgv1  = messtab-msgv1
        msgv2  = messtab-msgv2
        msgv3  = messtab-msgv3
        msgv4  = messtab-msgv4
      IMPORTING
        message_text_output = lv_msg.
    CONCATENATE e_msg lv_msg INTO e_msg.
    CLEAR: lv_msg,messtab.
  ENDLOOP.
ENDLOOP.
```

- BDC_OKCODE  '=BU' 表示单击保存按钮
- BDC_OKCODE  '/00' 表示回车

#### 两个固定表

Batch inputdata of single transaction：输入数据表

- `DATA:bdcdata LIKE BDCDATA OCCURS 0 WITH HEADER LINE.`

Messages of call transaction：返回信息

- `DATA:messtab LIKE BDCMSGCOLL OCCURS 0 WITH HEADER LINE.`

#### 两个固定 FORM

*BDC_DYNPRO*：指定 bdc_dynpro 的实参，告知系统 dialog 程序名称以及 Screen number。

```ABAP
*----------------------------------------------------------------*
*        Start new screen                                        *
*----------------------------------------------------------------*
FORM bdc_dynpro USING PROGRAM dynpro.
  CLEAR bdcdata.
  bdcdata-program  = program.
  bdcdata-dynpro   = dynpro.
  bdcdata-dynbegin = 'X'.
  APPEND bdcdata.
ENDFORM.
```

*BDC_FIELD*：指定 bdc_field 的实参，告知系统把光标放在哪个字段。

```ABAP
*----------------------------------------------------------------*
*        Insert field                                            *
*----------------------------------------------------------------*
FORM bdc_field USING fnam fval.
  IF fval <> ' ' OR fnam fval IS NOT INITIAL.
    CLEAR bdcdata.
    bdcdata-fnam = fnam.
    bdcdata-fval = fval.
    CONDENSE bdcdata-fval.
    APPEND bdcdata.
  ENDIF.
ENDFORM.
```

| Field name | Type | Length | Description       |
| :--------- | :--- | :----- | :---------------- |
| PROGRAM    | CHAR | 8      | Module pool       |
| DYNPRO     | NUMC | 4      | Dynpro number     |
| DYNBEGIN   | CHAR | 1      | Starting a dynpro |
| FNAM       | CHAR | 35     | Field name        |
| FVAL       | CHAR | 80     | Field value       |

### BDC中按顺序使用的功能模块

三个功能模块依次使用，使 BDC 编程成功进行数据传输。

- BDC_OPEN_GROUP：打开批量输入会话会话


- BDC_INSERT：将事务传输到 session


- BDC_CLOSE_GROUP：关闭批处理输入会话

### BDC 执行类型

#### Call Transaction Method

定义变量时需要添加两个变量。

```ABAP
DATA:bdcdata TYPE TABLE OF BDCDATA WITH HEADER LINE.    "保存录屏过程中的变量及常量数据"
DATA:messtab TYPE TABLE OF BDCMSGCOLL WITH HEADER LINE. "记录BDC执行返回数据" 
DATA:ctu_params LIKE CTU_PARAMS.   "调事务代码时带的一些参数，是否前台执行，报错停止等"
" 从程序中选一些dynpro和field的BDC行，不需要的字段或则屏幕，可以直接删除对应的代码。"
CLEAR ctu_params.
ctu_params-updmode = 'S'.
ctu_params-dismode = 'E'.
ctu_params-defsize = ''. "设置窗口非默认大小"
"调用BDC执行 T-code COOIS 显示订单抬头"
CALL TRANSACTION 'COOIS' USING bdcdata
                         OPTIONS  FROM ctu_params
                         MESSAGES INTO messtab.
```

`OPTIONS FROM [opt]`：参考ABAP字典结构类型 `CTU_PARAMS` 的结构数据对象来传递参数。

- DISMODE：显示模式，其值对应于前面介绍的 MODE 参数；
- UPDMODE：更新模式，其值对应于前面介绍的 UPDATE 参数；
- CATTMODE：CATT 模式，有三个值：
  - " "：非 CATT 模式；
  - N：CATT 模式但不对每个屏幕进行控制；
  - A：CATT 模式并对每个屏幕进行控制；
- DEFSIZE：是否使用屏幕的定义大小，有两个值：
  - " "：不使用屏幕定义大小，即显示给用户的屏幕跟普通运行时一样，根据用户窗口大小而自动扩展到全屏；
  - "X"：使用屏幕定义大小，即只用源程序中固定的屏幕大小，无论用户窗口如何；
- RACOMMIT：CALL TRANSACTION USING... is not completed by COMMIT
- NOBINPT：调用事务码时，系统字段 sy-binpt 的值，有两个值：
  - " "：在被调用事务执行时，系统字段 sy-binpt 的值为"X"；
  - "X"：在被调用事务执行时，系统字段 sy-binpt 的值为" "；
- NOBIEND：调用事务码完成后，系统字段 sy-binpt 的值，有两个值：
  - ""：在被调用事务执行后，系统字段 sy-binpt 的值为"X"；
  - "X"：在被调用事务执行后，系统字段 sy-binpt 的值为" "。

#### 执行类的

录屏和上面同样的方法生成程序。然后选择需要的代码段。不需要的可以注释，或者删除。直接执行 BDC。

