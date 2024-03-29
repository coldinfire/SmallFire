---
title: " 报表开发<屏幕设置> "
date: 2018-06-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

## 选择屏幕 

### 屏幕触发事件

选择屏幕触发的是：AT SELECTION-SCREEN

对话屏幕触发的是：PAI

列表屏幕触发的是：AT USER-COMMAND

###  屏幕事件处理

ABAP 开发属于事件驱动开发，这句话也清晰的解释了 SAP 程序的必然结构。程序是按照以下的事件块的顺序去依次执行的。它的事件块的顺序是指定好的。

#### LOAD-OF-PROGRAM.

在加载类型 1、M、F 或 S 的程序后触发内部会话中的关联事件。

只为每个程序和内部会话运行相关的处理块一次又一次。

处理块 LOAD-OF-PROGRAM 对类型 1、M、F 或 S 的 ABAP 程序的功能与构造函数对 ABAP 对象中的类的功能大致相同。

#### INITIALIZATION.

程序初始化事件，该事件在程序屏幕未显示之前执行；对程序设置值及屏幕元素进行初始化；只运行一次的事件块。

#### AT SELECTION-SCREEN OUTPUT.

屏幕输出前事件块(PBO)，在选择屏幕显示之前就被调用；用于屏幕输出时的各屏幕元素值的管控（响应屏幕上的事件，用户回车或 F8 后也被调用），通过 Modify screen可以修改选择屏幕字段。

设置屏幕选择框不可输入和必输控制：

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

#### AT SELECTION-SCREEN ON ...

选择屏幕显示之后，用来响应回车，F1，F4等事件。

可选参数：

- `ON field`：检查具体输入字段（SELECTION-OPTIONS或PARAMETERS）是否完整或正确
- `ON VALUE-REQUEST FOR <field low/high>`：SELECT-OPTIONS 点击选择帮助F4键时触发该事件
- `ON HELP-REQUEST FOR <field low/high>`：SELECTION-OPTIONS 按选择帮助F1键时键发该事件
- `ON RADIOBUTTON GROUP <radio>`：单选按钮事件，必须进行整体输入检查
- `ON BLOCK <block>`：Block 的触发事件（控制框架中的屏幕元素值的输入）
- `ON EXIT-COMMAND`：用于 BACK、CANCEL、EXIT 等事件 

#### AT SELECTION-SCREEN

当处理选择屏幕时（在 PAI 结束时）处理事件。即屏幕操作后事件块，输入值的验证和检查发生在这里。

#### START-OF-SELECTION

该事件在单击按钮后触发。此处程序开始从表中选择值。

#### END-OF-SELECTION

该事件应用于所有数据处理完成，即 START-OF-SELECTION 相关执行事件执行完成。但输出屏幕还未显示之前，在实际的应用于一些执行结果的检验等。

### SELECT-SCREEN 语句解析

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

```ABAP
SELECTION-SCREEN BEGIN OF BLOCK blk_1 [WITH FRAME] [TITLE title] [NO INTERVALS].
  ......
SELECTION-SCREEN END OF BLOCK blk_1.
扩展语法：
  WITH FRAME：创建一个框架
  TITLE title：创建一个带标题的框架
  NO INTERVALS：所创建的框架中限制SELECT只有一个输入项
```

#### Other screen elements

```ABAP
SELECTION-SCREEN INCLUDE BLOCKS <block1>.  "调用已存在屏幕元素"
SELECTION-SCREEN ULINE.          "划出横线，必须用在BLOCK中才能生效"
SELECTION-SCREEN SKIP <n>.       "在BLOCK中产生换行<n>"
SELECTION-SCREEN POSITION <pos>. "在BLOCK中产生空格"
SELECTION-SCREEN PUSHBUTTON [/][pos](len) button_text 
                            USER-COMMAND fcode 
                            [VISIBLE LENGTH vlen] 
                            [MODIF ID modid] 
                            [ldb_additions].  
SELECTION-SCREEN BEGIN OF LINE.
  ......
SELECTION-SCREEN END OF LINE.    "将所生成的屏幕元素控制在一行"
```

#### 屏幕元素单行添加

```ABAP
SELECTION-SCREEN BEGIN OF LINE.
  SELECTION-SCREEN COMMENT n(m) TEXT-001 FOR FIELD s_name. "文档信息"
  SELECTION-OPTION: s_name FOR itab-field. "设定类型"
  SELECTION-OPTION <seltab> FOR <f> 
    NO INTERVALS  "删除HIGH值"
    NO-EXTENSION  "限制选择为单行，不会出现多条按钮"
    OBLIGATORY    "只有前面一个框中出现勾勾，只针对LOW字段有效"
SELECTION-SCREEN END OF LINE.
```

### PARAMETERS 单值输入框

PARAMETERS 参照数据字典具体字段或者自定义数据类型创建单值文本输入域以及单选/复选框等：

```ABAP
PARAMETERS: 
  p_id(30) TYPE c.
  p_id LIKE dbtab-element.   "文本域"
  p_id AS CHECKBOX.            "复选框"
  p_id RADIOBUTTON GROUP GRP1. "单选域"
  p1 RADIOBUTTON GROUP GRP1 DEFAULT 'X',  "默认选中X"
  p2 RADIOBUTTON GROUP GRP2. "GRP2 单选组"
  p_id LIKE dbtab-element AS LISTBOX. "下拉框定义"
```

  可选参数列表：

- `DEFAULT f`：定义默认值


- `TYPE type`：参照某一类型对象定义 PARAMETERS


- `DECIMALS dec`：定义小数位，对输入参数自动格式化，该语法只对P类型有效(参数某一类型定义关键字TYPE)


- `LIKE g`：參照某一字典对象定义 PARAMETERS


- `MEMORY ID pi`：将 PARAMETERS 存储在 SAP 内存，参数名长度不能超过三位


- `NO-DISPLAY`：将 PARAMETERS 设置为隐藏，不会的屏幕上输出
- `MODIFY ID mx`：Modify ID 使屏幕分组，SCREEN-GROUP1 = 'mx'


- `LOWER CASE`：输入值中不允许输入小写字符，否则会自动转换为大写


- `OBLIGATORY`：限制该 PARAMETERS 为必填，否则会提示输入


- `AS CHECKBOX`：创建 CHECKBOX 对象


- `RADIO BUTTON GROUP radi`：创建（RADIO）单选框


- `VISIBLE LENGTH vlen`：定义显示长度


- `USER-COMMAND fcode`：为创建对象分配对象名，fcode 会存储到 sscrfields-ucomm 字段中


- `AS LISTBOX VISIBLE LENGTH vlen`：创建一个下拉框，并指定长度


#### 下拉框实现

定义一个下拉框对象，其可视数据长度一般比输出数据长度大2用于放置下拉图标。

`PARAMETERS: p_lang LIKE itab-field AS LISTBOX [VISIBLE LENGTH 22] [USER-COMMAND onli].`

通过函数：`VRM_SET_VALUES` 为下拉框初始化列表项。

```ABAP
DATA:INIT. "该变量用于记录下拉列表数值是否初始化，否则每次屏幕初始化都会重新加载重复数据"
AT SELECTION-SCREEN OUTPUT.
PERFORM SETLIST.
```

子程序用于加载下拉框的数据：

```ABAP
FORM SETLIST.
  TYPE-POOLS VRM. 
  DATA MYVALUE TYPE VRM_VALUES WITH HEADER LINE.
  "对内表赋值"
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

SELECT-OPTIONS 参照数据库字段来建立输入域，命名不能超过 8 位，最大输入长度为 18 位：

- ` SELECT-OPTIONS： s_xxx FOR dbtab-element [options].` 

输入域的内表结构

- SIGN：I  (包括定义的值/范围)、E (不包括定义的值/范围)

- OPTION：EQ、NE、CP(模糊查询)、NP、GT、GE、LT、LE

- LOW：范围较小值

- HIGH：范围较大值

参数可选属性列表：

- `DEFAULT g`：定义单一默认值


- `DEFAULT g...OPTION  xxx ... SIGN s`：定义含判断条件的单一默认值


- `DEFAULT g TO h`：定义默认值的取值范围


- `DEFAULT g TO h ... OPTION op ... SIGN s`：设置默认值的聚会范围及判断条件


- `MEMORY ID pid`：将 SELECT-OPTIONS 分配参数名并存储在 SAP 内存，参数名长度不能超过三位


- `NO-DISPLAY`：将 SELECT-OPTIONS 设置为隐藏，不会在屏幕上输出


- `LOWER CASE`：输入值中不允许输入小写字符，否则会自动转换为大写


- `OBLIGATORY`：限制 SELECT-OPTIONS 为必须输入的项目，执行中系统会提示
- `NO INTERVALS`：限制 SELECT-OPTIONS 只能输入单值，类似 Parameter 


-  `NO-EXTENSION`：限制 SELECT-OPTIONS 只能输入一行数据，输入多行按钮（上图最右边按钮）被隐藏


-  `VISIBLE LENGTH vlen`：定义所显示数据的长度


###  维护选择字段屏幕描述：GOTO-->Text Elements 

可以使用事物码 SE32 进行维护。前台界面显示的为 PARAMETERS 和 SELECTION-OPTION 定义的字段，不便于理解需。提供某一字段的完整名称以方便用户理解。

包含字段：

- Selection Texts：定义已存在并且激活的屏幕元素的名称。


- Text Symbols：实现自定义文本及符号,该文本使用对象为 SELECTION-SCREEN，以三位字符表示(TEXT-001)。


- 图标符号：可以在 Text Symbols 通过@符号来进行引用，如"@01@"，可通过程序 `RSTXICON` 查看所有的图标。

**多语言显示的维护**：GOTO -->Translation

### 屏幕常用控制逻辑

#### 设置组内屏幕的显示和隐藏

```ABAP
REPORT  ZTEST_MODIFID.

PARAMETERS show_all AS CHECKBOX USER-COMMAND all. 
SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME. 
PARAMETERS: p1 TYPE c LENGTH 10, 
            p2 TYPE c LENGTH 10, 
            p3 TYPE c LENGTH 10. 
SELECTION-SCREEN END OF BLOCK b1. 
SELECTION-SCREEN BEGIN OF BLOCK b2 WITH FRAME. 
PARAMETERS: p4 TYPE c LENGTH 10 MODIF ID bl2, 
            p5 TYPE c LENGTH 10 MODIF ID bl2, 
            p6 TYPE c LENGTH 10 MODIF ID bl2. 
SELECTION-SCREEN END OF BLOCK b2. 

AT SELECTION-SCREEN OUTPUT.    "当show_all值改变时会触发此事件"
  LOOP AT SCREEN. 
    IF show_all <> 'X' AND screen-group1 = 'BL2'. 
       screen-active = '0'.    "设置组内屏幕的显示和隐藏"
    ENDIF. 
    MODIFY SCREEN.
  ENDLOOP. 
```

#### 多个复选框，实现单选框功能

```ABAP
DATA: vcomm TYPE sscrfields-ucomm.
SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 1(32) text-007 FOR FIELD p_all.
PARAMETERS p_all AS CHECKBOX USER-COMMAND CB1.
SELECTION-SCREEN END OF LINE.

SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 1(32) text-005 FOR FIELD p_active.
PARAMETERS p_active AS CHECKBOX DEFAULT 'X' USER-COMMAND CB2.
SELECTION-SCREEN END OF LINE.

AT SELECTION-SCREEN.
  vcomm = sscrfields-ucomm.
AT SELECTION-SCREEN OUTPUT.
  CASE vcomm.
    WHEN 'CB1'.
      p_active = ''.
    WHEN 'CB2'.
      p_all = ''.
  ENDCASE.
```



#### 参考链接

- [https://www.cnblogs.com/foxting/archive/2012/07/01/2572243.html](https://www.cnblogs.com/foxting/archive/2012/07/01/2572243.html)