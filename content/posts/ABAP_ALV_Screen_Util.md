---
title: " ALV 屏幕其它功能设置 "
date: 2018-06-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

###  屏幕内创建按钮

`  SELECTION-SCREEN: PUSHBUTTON [/n] <pos(len)> <name> USER-COMMAND <ucom> [MODIF ID <key>].
`   

`/n` :按钮初始时距离屏幕左边的位置

`<pos(len)>`：PUSHBUTTON按钮在屏幕生成的位置与长度。

`<name>`：PUSHBUTTON按钮的名称，给按钮赋值时要用到名字。    

`<ucom>`：必须指定的字符代码，当用户在选择屏幕上触发按钮时，`<ucom>` 被输入到词典对象字段：SSCRFIELDS-UCOMM中，必须显式使用语句TABLES引用SSCRFIELDS。

实例：

```ABAP
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

#### 示例代码

```ABAP
TABLES SSCRFIELDS. "引用词典对象"
TYPE-POOLS ICON.   "Program Icon Library"
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

```ABAP
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

