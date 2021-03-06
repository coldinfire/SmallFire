---
title: "报表开发<屏幕设置>"
date: 2018-06-19
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

## 选择屏幕 [引用链接](https://www.cnblogs.com/foxting/archive/2012/07/01/2572243.html)

### 屏幕触发事件

选择屏幕触发的是：AT SELECTION-SCREEN

对话屏幕触发的是：PAI

列表屏幕触发的是：AT USER-COMMAND

### SELECT-SCREEN 

SELECT-SCREEN 语句用于创建屏幕的框架结构，主要包括屏幕元素的创建、子屏幕的创建等。

#### 定义屏幕对象

```ABAP
SELECTION-SCREEN BEGIN OF SCREEN src_num.
  ...
SELECTION-SCREEN END OF SCREEN src_num.
```

该语法用于定义一个 INCLUDE SUREEN，可通过 CALL 方法在 Report 程序中引用：

`CALL SCREEN <num> STARTING AT [1 2] ENDING AT [1 2].`

参数可以将所定义屏幕窗体作为一个新的对话框窗体来引用，并指定其创建的具体大小及位置。

**注意：当从一个主屏幕中来调用其程序中的另一窗体时，必须使用CALL SELECTION-SCREEN方法**

#### 定义屏幕块

在屏幕中定义一个 BLOCK 块：

```JS
SELECTION-SCREEN BEGIN OF BLOCK blk_1 [WITH FRAME] [TITLE title] [NO INTERVALS].
  ......
SELECTION-SCREEN END OF BLOCK blk_1.
扩展语法：
  WITH FRAME：创建一个框架
  TITLE title：创建一个带标题的框架
  NO INTERVALS：所创建的框架中限制SELECT只有一个输入项
```

屏幕块中添加的其它元素：

```JS
SELECTION-SCREEN INCLUDE BLOCKS <block1>.  调用已存在屏幕元素
SELECTION-SCREEN ULINE.  划出横线，必须用在BLOCK中才能生效。
SELECTION-SCREEN SKIP <n>.  在BLOCK中产生换行<n>。
SELECTION-SCREEN POSITION <pos>. 在BLOCK中产生空格。
SELECTION-SCREEN BEGIN OF LINE.
  ......
SELECTION-SCREEN END OF LINE. 将所生成的屏幕元素控制在一行.
```

#### 单行添加

```JS
SELECTION-SCREEN BEGIN OF LINE.
  SELECTION-SCREEN COMMENT n(m) TEXT-001 FOR FIELD s_name. "文档信息"
  SELECTION-OPTION: s_name FOR itab-field. "设定类型"
  SELECTION-OPTION <seltab> FOR <f> 
    NO INTERVALS  "删除HIGH值"
    NO-EXTENSION  "限制选择为单行，不会出现多条按钮"
    OBLIGATORY    "只有前面一个框中出现勾勾，只针对LOW字段有效"
SELECTION-SCREEN END OF LINE.
```

### PARAMETERS 单值输入

PARAMETERS 参照数据字典具体字段或者自定义数据类型创建单值文本输入域以及单选/复选框等：

```JS
PARAMETERS: 
  <P_1> LIKE <field>      "文本域"
  TYPE AS CHECKBOX.       "复选框"
  RADIOBUTTON GROUP GRP1, "单选域"
  P2 RADIOBUTTON GROUP GRP1 DEFAULT 'X',  "默认选中X"
  P3 RADIOBUTTON GROUP GRP1. "GRP1单选组"
```

  可选参数列表：

- ​    `DEFAULT f`：定义默认值


- ​    `TYPE type`：参照某一类型对象定义PARAMETERS


- ​    `DECIMALS dec`：定义小数位，对输入参数自动格式化，该语法只对P类型有效(参数某一类型定义关键字TYPE)


- 
  ​    `LIKE g`：參照某一字典对象定义PARAMETERS


- ​    `MEMORY ID pi`：将PARAMETERS存储在SAP内存，参数名长度不能超过三位


- ​    `NO-DISPLAY`：将PARAMETERS设置为隐藏，不会的屏幕上输出


- ​    `LOWER CASE`：输入值中不允许输入小写字符，否则会自动转换为大写


- ​    `OBLIGATORY`：限制该PARAMETERS为必填，否则会提示输入


- ​    `AS CHECKBOX`：创建CHECKBOX对象


- ​    `RADIO BUTTON GROUP radi`：创建（RADIO）单选框


- ​    `VISIBLE LENGTH vlen`：定义显示长度


- ​    `USER-COMMAND fcode`：为创建对象分配对象名，fcode 会存储到 sscrfields-ucomm 字段中


- ​    `AS LISTBOX VISIBLE LENGTH vlen`：创建一个下拉框，并指定长度


#### 下拉框实现

定义一个下拉框对象，其可视数据长度一般比输出数据长度大2用于放置下拉图标.

PARAMETERS: p_lang LIKE itab-field AS LISTBOX VISIBLE LENGTH 22 USER-COMMAND onli DEFAULT 'LH'.

通过函数：`VRM_SET_VALUES` 为下拉框初始化列表项

该变量用于记录下拉列表数值是否初始化，否则每次屏幕初始化都会重新加载重复数据

```JS
DATA:INIT.
AT SELECTION-SCREEN OUTPUT.
PERFORM SETLIST.
```

子程序用于加载下拉框的数据：

```JS
FORM SETLIST.
  TYPE-POOLS VRM. 
  DATA MYVALUE TYPE VRM_VALUES WITH HEADER LINE.
  对内表加载值
  MYVALUE-KEY = 'CHINESE'. MYVALUE-TEXT = '中国'.    
  APPEND MYVALUE.
  MYVALUE-KEY = 'AMERICAN'. MYVALUE-TEXT = '美国'.         
  APPEND MYVALUE.
  MYVALUE-KEY = 'ENGLISH'. MYVALUE-TEXT = '英国'. 
  APPEND MYVALUE.
  MYVALUE-KEY = 'FRENCH'. MYVALUE-TEXT = '法国'.   
  APPEND MYVALUE.
  IF INIT IS INITIAL.
    CALL FUNCTION 'VRM_SET_VALUES' "调用函数对下拉框对象传递数据"
      EXPORTING
        ID = 'P_LANG'       "下拉框对象名"
        VALUES = MYVALUE[]  "下拉框中加载的数据"
      EXCEPTIONS
        ID_ILLEGAL_NAME = 1
        OTHERS = 2.
  ENDIF.
 INIT = 'X'. "记录初始化状态"
ENDFORM.
```

### SELECT-OPTIONS 输入域

SELECT-OPTIONS 参照数据库字段来建立输入域，命名不能超过8位，最大输入长度为18位：

 ` SELECT-OPTIONS： <S_1> FOR <dbtab-ele> option`   

内表结构：

```JS
SIGN：I , E
OPTION: EQ,NE,CP（模糊查询）,NP,GE,LT,LE,GT
LOW : 范围较小值
HIGH: 范围较大值
```

  参数可选属性列表：

- ​	`DEFAULT g`：定义单一默认值


- ​    `DEFAULT g...OPTION  xxx ... SIGN s`：定义含判断条件的单一默认值


- ​    `DEFAULT g TO h`：定义默认值的取值范围


- ​    `DEFAULT g TO h ... OPTION op ... SIGN s`：设置默认值的聚会范围及判断条件


- ​    `MEMORY ID pid`：将SELECT-OPTIONS分配参数名并存储在SAP内存，参数名长度不能超过三位


- ​    `NO-DISPLAY`：将SELECT-OPTIONS设置为隐藏，不会在屏幕上输出


- ​    `LOWER CASE`：输入值中不允许输入小写字符，否则会自动转换为大写


- ​    `OBLIGATORY`：限制该SELECT-OPTIONS为必须输入的项目，执行中系统会提示


- ​    `NO-EXTENSION`：限制该SELECT-OPTIONS只能输入一行数据，输入多行按钮（上图最右边按钮）被隐藏


- ​    `VISIBLE LENGTH vlen`：定义所显示数据的长度


###  维护选择字段屏幕描述：GOTO-->Text Elements 

可以使用 TCode:SE32 进行维护。

前台界面显示的为 PARAMETERS 和 SELECTION-OPTION 定义的字段，不便于理解需。提供某一字段的完整名称以方便用户理解。

包含字段：

- Selection Texts：定义已存在并且激活的屏幕元素的名称


- Text Symbols：实现自定义文本及符号,该文本使用对象为 SELECTION-SCREEN，以三位字符表示(TEXT-001)


- 图标符号：可以在 Text Symbols 通过@符号来进行引用，如"@01@",可通过程序RSTXICON查看所有的图标


#### 可进行多语言显示的维护：GOTO -->Translation

###  屏幕事件处理

#### INITIALIZATION

程序初始化事件，该事件在程序屏幕未显示之前执行。对程序设置值及屏幕元素进行初始化

#### START-OF-SELECTION

该事件在单击按钮后触发

END-OF-SELECTION

该事件应用于所有数据处理完成，即 START-OF-SELECTION 相关执行事件执行完成。但输出屏幕还未显示之前，在实际的应用于一些执行结果的检验等。

#### AT SELECTION-SCREEN

选择屏幕显示之后，用来响应回车，F1，F4等事件。

可选参数：

- `ON field`：检查具体输入字段（SELECTION-OPTIONS或PARAMETERS）是否完整或正确
- `ON VALUE-REQUEST FOR <field low/high>`：SELECT-OPTIONS 点击选择帮助F4键时触发该事件
- `ON HELP-REQUEST FOR <field low/high>`：SELECTION-OPTIONS 按选择帮助F1键时键发该事件
- `ON RADIOBUTTON GROUP <radio>`：单选按钮事件，必须进行整体输入检查
- `ON BLOCK <block>`：框架的触发事件（控制框架中的屏幕元素值的输入）
- `OUTPUT`：用于屏幕输出时的各屏幕元素值的管控（PBO处理，在选择屏幕显示之前就被调用；响应屏幕上的事件，用户回车或F8后也被调用；通过modify screen可以修改选择屏幕字段）
- `ON EXIT-COMMAND`：用于 BACK、CANCEL、EXIT 等事件

**设置屏幕选择框不可输入和必输控制**

```ABAP
AT SELECTION-SCREEN OUTPUT.
  LOOP AT SCREEN.
	IF screen-name = 'X_NORM' OR screen-name = 'X_PARK'.
	  screen-input = 0.
	  MODIFY SCREEN.
	ENDIF.
    IF screen-name = 'X_NORM' OR screen-name = 'X_PARK'.
      screen-required = 2.
      MODIFY SCREEN.
    ENDIF.
    ...
  ENDLOOP.
```

- REQUIRED：控制必输属性，使用后会忽略OBLIGATORY。
  - 0：不必输 
  - 1：必输，系统自动校验 
  - 2：不必输，但是会提示必输标志
- INPUT：控制屏幕元素的可输入性
- ACTIVE：控制屏幕的可见性 (0:不可见  1:不可见) 

###  屏幕内创建按钮

`  SELECTION-SCREEN: PUSHBUTTON [/n] <pos(len)> <name> USER-COMMAND <ucom> [MODIF ID <key>].
`   

`/n` :按钮初始时距离屏幕左边的位置

`<pos(len)>`：PUSHBUTTON按钮在屏幕生成的位置与长度。

`<name>`：PUSHBUTTON按钮的名称，给按钮赋值时要用到名字。    

`<ucom>`：必须指定的字符代码，当用户在选择屏幕上触发按钮时，`<ucom>` 被输入到词典对象字段：SSCRFIELDS-UCOMM中，必须显式使用语句TABLES引用SSCRFIELDS。

实例：

```JS
TABLES SSCRFIELDS. "引用词典对象"
INCLUDE:<icon>.  "按钮中加入图标必须调用该类型库,图标请参考T-CODE：ICON"
SELECTION-SCREEN PUSHBUTTON /1(20) PUBU1 USER-COMMAND ABCD.
SELECTION-SCREEN SKIP."换行"
SELECTION-SCREEN PUSHBUTTON /10(25) PUBU2 USER-COMMAND ABCE. "位置从10开始"
AT SELECTION-SCREEN OUTPUT.
  MOVE 'CALL NEXT SCREEN' TO PUBU1. "给PUBU1按钮赋值描述"
  WRITE ICON_OKAY AS ICON TO PUBU2. "给PUBU2按钮添加图标，并且在给按钮赋值之前，否则将会把文字替换。"
  CONCATENATE PUBU2 'My Second Button' INTO PUBU2 SEPARATED BY SPACE. "给第二个按钮添加赋值描述"
AT SELECTION-SCREEN.
 IF SSCRFIELDS-UCOMM = 'ABCD'.
   PERFORM xxxx.  "调用子程序"
 ENDIF.
```

###  在工具栏上新增一个功能按钮

`SELECTION-SCREEN FUNCTION KEY n.`

该按钮的定义保存在系统结构体 SSCRFIELDS 中，n 为一个整数序数最大至 5。

当 n=1 时，其按钮描述保存在字段 SSCRFIELDS-FUNCTXT_01 中，其按钮对象命名为"FC01"，保存在字段SSCRFIELDS-UCOMM 中。

**实例：**

```html
TABLES SSCRFIELDS. "引用词典对象"
TYPE-POOLS ICON. "Program Icon Library"
DATA functxt TYPE SMP_DYNTXT. "SMP_DYNTXT(菜单制作器:动态文本的程序接口)"
PARAMETERS: p_carrid TYPE s_carr_id,
            p_cityfr TYPE s_from_cit.
SELECTION-SCREEN: FUNCTION KEY 1,FUNCTION KEY 2.
INITIALIZATION. "屏幕初始化"
  functxt-icon_id   = '@49@'.  "文本字段中的图标（替换显示，别名）"
  functxt-quickinfo = 'Download Template'. "菜单制作器：信息文本 (4.0)，滑鼠移去过去显示的信息TIP"
  functxt-icon_text ='Download Template'.  "菜单制作器：图标文本 (4.0)，菜单名称"
  sscrfields-functxt_01 = functxt.
  functxt-icon_text = 'UA'.
  sscrfields-functxt_02 = functxt.
AT SELECTION-SCREEN.
  CASE SSCRFIELDS-UCOMM.
    WHEN 'FC01'.
      p_carrid = 'LH'.
      p_cityfr = 'Frankfurt'.
    WHEN 'FC02'.
      p_carrid = 'UA'.
      p_cityfr = 'Chicago'.
  ENDCASE.
```

### 屏幕单选框分组显示界面内容

通过Group将GUI需要显示的界面进行分组，并在 `AT SELECTION-SCREEN OUTPUT.`事件中对每一个单选项选择进行判断，并激活对应的界面。

```JS
SELECTION-SCREEN BEGIN OF BLOCK BLK1 WITH FRAME TITLE TEXT-008.
PARAMETERS: P_R1 TYPE FLAG RADIOBUTTON GROUP C1 DEFAULT 'X' USER-COMMAND US1,
            P_R2 TYPE FLAG RADIOBUTTON GROUP C1,
            P_R3 TYPE FLAG RADIOBUTTON GROUP C1.
SELECTION-SCREEN END OF BLOCK BLK1.

SELECTION-SCREEN BEGIN OF BLOCK BLK2 WITH FRAME TITLE TEXT-007.
PARAMETERS: P_PEDTR TYPE PLAF-PEDTR MODIF ID PR1 DEFAULT SY-DATUM.
PARAMETERS: P_WERKS TYPE T001W-WERKS MODIF ID PR1 MEMORY ID WRK.
PARAMETERS: P_TIME  TYPE MSEG-MENGE VISIBLE LENGTH 6 DEFAULT '22.5' MODIF ID PR1.
SELECT-OPTIONS: S_AUFNR FOR AFKO-AUFNR MODIF ID PR1  .
SELECTION-SCREEN END OF BLOCK BLK2.

SELECTION-SCREEN BEGIN OF BLOCK BLK3 WITH FRAME TITLE TEXT-008.
PARAMETERS: P_WERK TYPE T001W-WERKS  MODIF ID PR2 MEMORY ID WRK.
SELECT-OPTIONS: S_MATNR FOR MARA-MATNR  MODIF ID PR2 ,
                S_DRAWN FOR MARA-ZZ_DRAWING_NO MODIF ID PR2 ,
                S_ZMC FOR ZPP_MOLDMACTON-Z_MC MODIF ID PR2,
                S_ZTONS FOR ZPP_MOLDMACTON-ZTONS MODIF ID PR2 ,
                S_WSHOP FOR ZPP_MOLDMACTON-ZWKSHOP MODIF ID PR2,
                S_SDATE FOR ZPP_MOLDSCHE-SDATE MODIF ID PR2 NO-EXTENSION NO INTERVALS,
                S_SDAT2 FOR ZPP_MOLDSCHE-SDATE MODIF ID PR2 DEFAULT SY-DATUM.
SELECTION-SCREEN END OF BLOCK BLK3.

AT SELECTION-SCREEN OUTPUT.
  LOOP AT SCREEN.
    IF P_R1 = 'X'.
      IF SCREEN-GROUP1 = 'PR2'.
        SCREEN-ACTIVE = 0.
      ELSE.
        SCREEN-ACTIVE = 1.
      ENDIF.
    ELSEIF P_R2 = 'X' OR P_R3 = 'X'.
      IF SCREEN-GROUP1 = 'PR1'.
        SCREEN-ACTIVE = 0.
      ELSE.
        SCREEN-ACTIVE = 1.
        IF P_R3 = 'X'.
          IF  SCREEN-NAME CS 'S_SDATE'.
            SCREEN-ACTIVE = 0.
          ENDIF.
        ELSEIF P_R2 = 'X'.
          IF  SCREEN-NAME CS 'S_SDAT2'.
            SCREEN-ACTIVE = 0.
          ENDIF.
        ENDIF.
      ENDIF.
    ENDIF.
    MODIFY SCREEN.
  ENDLOOP.
```

