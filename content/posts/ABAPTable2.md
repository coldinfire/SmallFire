---
title: "报表开发<屏幕设置>"
date: 2018-06-19T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - 报表开发

---



## 选择屏幕 [引用链接](https://www.cnblogs.com/foxting/archive/2012/07/01/2572243.html)

### 触发

​	选择屏幕触发的是：AT SELECTION-SCREEN

​	对话屏幕触发的是：PAI

​	列表屏幕触发的是：AT USER-COMMAND

### SELECT-SCREEN 

  SELECT-SCREEN语句用于创建屏幕的框架结构,主要包括屏幕元素的创建、子屏幕的创建等。

**定义屏幕对象：**
    

```js
SELECTION-SCREEN BEGIN OF SCREEN src.
      .......
SELECTION-SCREEN END OF SCREEN src.
该语法用于定义一个INCLUDE SUREEN，可通过CALL方法在Report程序中引用。
CALL SCREEN <num> STARTING AT [1 2] ENDING AT [1 2].
参数可以将所定义屏幕窗体作为一个新的对话框窗体来引用，并指定期创建的具体大小及位置。
```

​	**注意：当从一个主屏幕中来调用其程序中的另一窗体时，必须使用CALL SELECTION-SCREEN方法.**

**定义屏幕块：**
    

```JS
SELECTION-SCREEN BEGIN OF BLOCK block.
  ......
SELECTION-SCREEN END OF BLOCK.
该语法在屏幕中定义一个BLOCK，其扩展语法包括：
   ...WITH FRAME：创建一个框架
   ...TITLE title：创建一个带标题的框架。
   ...NO INTERVALS：所创建的框架中限制SELECT只有一个输入项。 
```

**其他元素：**

```JS
SELECTION-SCREEN INCLUDE BLOCKS <block1>.  调用已存在屏幕元素
SELECTION-SCREEN ULINE.  划出横线，必须用在BLOCK中才能生效。
SELECTION-SCREEN SKIP <n>.  在BLOCK中产生换行<n>。
SELECTION-SCREEN POSITION <pos>. 在BLOCK中产生空格。
SELECTION-SCREEN BEGIN OF LINE.
  ......
SELECTION-SCREEN END OF LINE. 将所生成的屏幕元素控制在一行.
```

**单行添加：**

```JS
SELECTION-SCREEN BEGIN OF LINE.
  SELECTION-SCREEN COMMENT n(m) TEXT-001 FOR FIELD S_name."文档信息
  SELECTION-OPTION: S_name FOR itab-field.     "设定类型
  SELECTION-OPTION <seltab> FOR <f> 
    NO INTERVALS : 删除HIGH值
	NO-EXTENSION : 限制选择为单行，不会出现多条按钮
	OBLIGATORY   : 只有前面一个框中出现勾勾，只针对LOW字段有效
SELECTION-SCREEN END OF LINE.
```



### PARAMETERS 单值输入

  PARAMETERS 参照数据字典具体字段或者自定义数据类型创建单值文本输入域以及单选/复选框等：

```JS
PARAMETERS: <P_1> LIKE <field>    "文本域
            TYPE AS CHECKBOX.       "复选框
            RADIOBUTTON GROUP GRP1, "单选域 
            P2 RADIOBUTTON GROUP GRP1 DEFAULT 'X',  "默认选中 ”X“
            P3 RADIOBUTTON GROUP GRP1. "GRP1单选组
```

  默认值：
    

​    ...`DEFAULT f：`定义默认值。

​    ...`TYPE type：`参照某一类型对象定义PARAMETERS。

​    ...`DECIMALS dec：`定义小数位，对输入参数自动格式化，该语法只对P类型有效(参数某一类型定义关键字TYPE)。


​    ...`LIKE g：`參照某一字典对象定义PARAMETERS。

​    ...`MEMORY ID pi：`将PARAMETERS存储在SAP内存，参数名长度不能超过三位。

​    ...`NO-DISPLAY：`将PARAMETERS设置为隐藏，不会的屏幕上输出。

​    ...`LOWER CASE：`输入值中不允许输入小写字符，否则会自动转换为大写。

​    ...`OBLIGATORY：`限制该PARAMETERS为必填，否则会提示输入。

​    ...`AS CHECKBOX：`创建CHECKBOX对象。

​    ...`RADIO BUTTON GROUP radi：`创建（RADIO）单选框。

​    ...`VISIBLE LENGTH vlen：`定义显示长度。

​    ...`USER-COMMAND ucom：`为创建对象分配对象名，该值保存在内表中可供其它对象操作。

​    ...`AS LISTBOX VISIBLE LENGTH vlen：`创建一个下拉框，并指定长度。

**下拉框实现：**

定义一个下拉框对象，其可视数据长度一般比输出数据长度大2用于放置下拉图标.

​	PARAMETERS: P_LANG LIKE itab-field AS LISTBOX VISIBLE LENGTH 22 USER-COMMAND onli DEFAULT 'LH'.

通过函数：VRM_SET_VALUES为下拉框初始化列表项

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
    CALL FUNCTION 'VRM_SET_VALUES' "调用函数对下拉框对象传递数据
      EXPORTING
        ID = 'P_LANG' "下拉框对象名
        VALUES = MYVALUE[]  "下拉框中加载的数据
      EXCEPTIONS
        ID_ILLEGAL_NAME = 1
        OTHERS = 2.
  ENDIF.
 INIT = 'X'. "记录初始化状态
ENDFORM.
```

### SELECT-OPTIONS 输入域

SELECT-OPTIONS 参照数据库字段来建立输入域，命名不能超过8位，最大输入长度为18位：

 ` SELECT-OPTIONS： <S_1> FOR <dbtab-ele>....`   

内表结构：
    

```JS
SIGN：I , E
OPTION: EQ,NE,CP（模糊查询）,NP,GE,LT,LE,GT
LOW : 范围较小值
HIGH: 范围较大值
```

  默认值设定：
    

​	...`DEFAULT g：`定义单一默认值。

​    ...`DEFAULT g...OPTION  xxx ... SIGN s：`定义含判断条件的单一默认值。

​    ...`DEFAULT g TO h：`定义默认值的取值范围。

​    ...`DEFAULT g TO h ... OPTION op ... SIGN s：`设置默认值的聚会范围及判断条件。

​    ...`MEMORY ID pid：`将SELECT-OPTIONS分配参数名并存储在SAP内存，参数名长度不能超过三位。

​    ...`NO-DISPLAY：`将SELECT-OPTIONS设置为隐藏，不会在屏幕上输出。

​    ...`LOWER CASE：`输入值中不允许输入小写字符，否则会自动转换为大写。

​    ...`OBLIGATORY：`限制该SELECT-OPTIONS为必须输入的项目，执行中系统会提示。

​    ...`NO-EXTENSION：`限制该SELECT-OPTIONS只能输入一行数据，输入多行按钮（上图最右边按钮）被隐藏。

​    ...`VISIBLE LENGTH vlen：`定义所显示数据的长度。

###  GOTO-->Text Elements   (TCode:SE32)

   前台界面显示的为PARAMETERS和SELECTION-OPTION定义的字段，不便于理解需。提供某一字段的完整名称以方便用户理解。

​    GOTO -->Translation：可进行多语言显示的维护

​	包含字段：
​    

​	Selection Texts：定义已存在并且激活的屏幕元素的名称。
​    

​		Text Symbols：实现自定义文本及符号,该文本使用对象为SELECTION-SCREEN，以三位字符表示(TEXT-001)。
​        

​	   图标符号:可以在Text Symbols通过@符号来进行引用，如"@01@",可通过程序RSTXICON查看所有的图标

###  屏幕事件处理

​	INITIALIZATION. "程序初始化事件，該事件在程序屏幕未顯示之前執行。對程序設置值及屏幕元素進行初始化設置

​    START-OF-SELECTION事件：该事件在单击按钮后触发

​    END-OF-SELECTION事件：该事件应用于所有数据处理完成，即START-OF-SELECTION相关执行事件执行完成。但输出屏幕还未显示之前，在实际的应用于一些执行结果的检验等。

​    AT SELECTION-SCREEN：选择屏幕显示之后，用来响应回车，F8，F1，F4等事件。

```JS
...ON <field>：检查具体输入字段(SELECTION-OPTIONS或PARAMETERS)是否完整或正确。
...ON VALUE-REQUEST FOR <field low/high>：SELECT-OPTIONS按选择帮助<F4>键时触发该事件。
...ON HELP-REQUEST FOR <field low/high>：SELECTION-OPTIONS按选择帮助<F1>键时键发该事件。
...ON RADIOBUTTON GROUP <radio>：单选按钮事件，必须进行整体输入检查。
...ON BLOCK <block>：框架的触发事件（控制框架中的屏幕元素值的输入）。
...OUTPUT：用于屏幕输出时的各屏幕元素值的管控（PBO处理，在选择屏幕显示之前就被调用；
		   响应屏幕上的事件，用户回车或F8后也被调用；通过modify screen可以修改选择屏幕字段）。
...ON EXIT-COMMAND：用于"BACK","CANCEL","EXIT"等事件。
```

**设置屏幕选择框不可输入**

```JS
AT SELECTION-SCREEN OUTPUT.
  LOOP AT SCREEN.
	IF screen-name = 'X_NORM' OR screen-name = 'X_PARK'.
	  screen-input = 0.
	  MODIFY SCREEN.
	ENDIF.
  ENDLOOP.
      
REQUIRED：控制比属性，使用后会忽略OBLIGATORY.0:不必输 1:必输，系统自动校验 2:不必输，但是会提示必输标志
INPUT：控制屏幕元素的可输入性
ACTIVE：控制屏幕的可见性 1:可见  2:不可见
```



###  屏幕内创建按钮

`  SELECTION-SCREEN: PUSHBUTTON [/n] <pos(len)> <name> USER-COMMAND <ucom> [MODIF ID <key>].
`   

​	 [/n] :按钮初始时距离屏幕左边的位置

​	 `<pos(len)>：`PUSHBUTTON按钮在屏幕生成的位置与长度。
​    

​	`<name>：`PUSHBUTTON按钮的名称，给按钮赋值时要用到名字。
​    

​    `<ucom>：`必须指定的字符代码，当用户在选择屏幕上触发按钮时，`<ucom>`被输入到词典对象字段：SSCRFIELDS-UCOMM中，必须显式使用语句TABLES引用SSCRFIELDS。



​	**实例：**  

```JS
TABLES SSCRFIELDS."引用词典对象
  INCLUDE:<icon>.  "按钮中加入图标必须调用该类型库,图标请参考T-CODE：ICON
  SELECTION-SCREEN PUSHBUTTON /1(20) PUBU1 USER-COMMAND ABCD.
  SELECTION-SCREEN SKIP."换行
  SELECTION-SCREEN PUSHBUTTON /10(25) PUBU2 USER-COMMAND ABCE. "位置从10开始
  AT SELECTION-SCREEN OUTPUT.
    MOVE 'CALL NEXT SCREEN' TO PUBU1. "给PUBU1按钮赋值描述
    WRITE ICON_OKAY AS ICON TO PUBU2. "给PUBU2按钮添加图标，并且在给按钮赋值之前，否则将会把文字替换。
    CONCATENATE PUBU2 'My Second Button' INTO PUBU2 SEPARATED BY SPACE. "给第二个按钮添加赋值描述
  AT SELECTION-SCREEN.
   IF SSCRFIELDS-UCOMM = 'ABCD'.
       PERFORM xxxx.  "调用子程序
   ENDIF.
```

###  在工具栏上新增一个功能按钮

​    `SELECTION-SCREEN FUNCTION KEY n.
`

​      该按钮的定义保存在系统结构体SSCRFIELDS中，n为一个整数序数最大至5。当n等于1时，其按钮描述保存在字段SSCRFIELDS-FUNCTXT_01中，其按钮对象命名为"FC01",保存在字段SSCRFIELDS-UCOMM中。

​	**实例：**
​     

```jsp
TYPE-POOLS ICON. "Program Icon Library
  TABLES SSCRFIELDS.
  DATA functxt TYPE SMP_DYNTXT. "SMP_DYNTXT(菜单制作器:动态文本的程序接口)
  PARAMETERS: p_carrid TYPE s_carr_id,
              p_cityfr TYPE s_from_cit.
  SELECTION-SCREEN: FUNCTION KEY 1,
                    FUNCTION KEY 2.
  INITIALIZATION. "屏幕初始化
    functxt-icon_id   = icon_ws_plane.  "文本字段中的图标（替换显示，别名） 
    functxt-quickinfo = 'Preselected Carrier'.  "菜单制作器：信息文本 (4.0)，滑鼠移去过去显示的信息TIP
    functxt-icon_text = 'LH'.  "菜单制作器：图标文本 (4.0)，菜单名称
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



