---
title: " Dialog 表格控件使用 "
date: 2018-09-02
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Dialog
---

### 创建表格控件

#### Step1：创建 Structure

创建自定义 Structure：ZTC_EKKO ，包含需要显示在 Table Control 中的字段。

#### Step2：创建程序

使用事物码 SE80 (Object Navigator) -> Repository Browser -> Program；输入以 `SAPMZ_` 开头的程序名（dialog program）创建 Module Pool：SAPMZ_TCONTROL。

![Dialog Program](/images/ABAP/ABAP_Dialog_4.png)

勾选 With TOP INCL：

![Dialog Program](/images/ABAP/ABAP_Dialog_5.png)

根据默认的 include name 创建 Top include：

![Top include](/images/ABAP/ABAP_Dialog_6.png)

创建 Module Pool 成功后点击保存：

![Dialog](/images/ABAP/ABAP_Dialog_7.png)

#### Step3：TOP include 内容定义

TOP include 中定义的内容是在整个 Dialog 中的全局变量。

```ABAP
TABLES: ztc_ekko.
DATA: ok_code TYPE sy-ucomm.
DATA: mark TYPE c.
DATA: it_ekko TYPE STANDARD TABLE OF ztc_ekko INITIAL SIZE 0,
      wa_ekko TYPE ztc_ekko.
CONTROLS: tc100 TYPE tableview USING SCREEN 100.
```

#### Step4：创建 Screen 

右键创建一个屏幕，屏幕编号为 0100。

![Screen](/images/ABAP/ABAP_Dialog_8.png)

![Screen](/images/ABAP/ABAP_Dialog_9.png)

屏幕创建完成后在 Element List 页签中创建 OK_CODE：

![OK Code](/images/ABAP/ABAP_Dialog_10.png)

#### Step5：创建 Table Control

点击 Layout，弹出 Screen Painter 来编辑屏幕布局内容。选中 Table Control 按钮然后拖到屏幕中，然后输入对应的名称 TC100：

![Table Control](/images/ABAP/ABAP_Dialog_11.png)

点击黄色按钮 (Attribute Window) 设置表格控件的属性：

![Attribute Window](/images/ABAP/ABAP_Dialog_15.png)

#### Step6：Populate Table Control

点击橙色按钮（Dictionary/Program Fields Window）。

![Fields](/images/ABAP/ABAP_Dialog_12.png)

 在弹出的 Dict./Program Fields 屏幕上输入 ZTC_EKKO 并按下 "Get from Dict" 按钮。 选择所需的字段（全选），然后按 Enter。 

![Layout](/images/ABAP/ABAP_Dialog_13.png)

最后将它们拖到前面创建的表格控件上：

![Layout](/images/ABAP/ABAP_Dialog_14.png)

#### Step7：创建逻辑流

在屏幕 100 的流程逻辑中创建两个模块，一个用于从数据库中选择数据，另一个用于将所选字段移动到表格控件中。 还要插入两个循环语句来填充和检索表格控件的行。

```ABAP
PROCESS BEFORE OUTPUT.
* MODULE STATUS_0100.
  MODULE data_retrieval.
  LOOP AT it_ekko INTO wa_ekko WITH control TC100.
    MODULE populate_screen.
  ENDLOOP.
  
PROCESS AFTER INPUT.
* MODULE USER_COMMAND_0100.
  LOOP AT it_ekko.
    MODULE update_table.
  ENDLOOP.
```

PBO：

```ABAP
MODULE data_retrieval OUTPUT.
* select data from ekko table
  IF it_ekko is initail.
    SELECT ebeln bukrs bstyp bsart bsakz loekz statu aedat
      UP TO 10 ROWS
      FROM ekko
      INTO CORRESPONDING FIELDS OF TABLE it_ekko.
  ENDIF.
ENDMODULE. " data_retrieval OUTPUT "

MODULE populate_screen OUTPUT.
  DATA: ld_line TYPE i.
* Set which line of table is a top of displayed table control
  IF sy-stepl = 1.
    tc100-lines = tc100-top_line + sy-loopc - 1.
  ENDIF.
* Move fields from work area to scrren fields
  MOVE-CORRESPONDING wa_ekko TO ztc_ekko.
ENDMODULE. " populate_screen OUTPUT "
```

PAI：获取选择的 SAP dynpro 表控件和存储的行选择

```ABAP
*&---------------------------------------------------------------------*
*&      Module  update_table  INPUT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
MODULE update_table input.
 READ TABLE it_ekko INTO wa_ekko INDEX tc100-CURRENT_LINE.
 IF NOT mark IS INITIAL.
   wa_ekko-sel = 'X'.
   MODIFY it_ekko FROM wa_ekko INDEX tc100-CURRENT_LINE.
 endif.
ENDMODULE.                 " update_table  INPUT "
```

#### 操作单个abap dynpro 表控制字段属性

如果将以下 ABAP 放入 "populate_screen" PBO 模块（流逻辑中的表控制循环中的 PBO 模块），它将设置第 2 行的 EBELN 字段仅显示。 代码的第一位根据当前顶部可查看的表格控制行计算当前正在处理的行。 一旦到达所需的行，它就会执行 "LOOP AT SCREEN" 以找到正确的字段并设置其属性。

```ABAP
MODULE populate_screen OUTPUT.
    DATA: ld_line TYPE i.
*   Set which line of itab is at the top of the table control
    IF sy-stepl = 1.
      tc100-lines = tc100-top_line + sy-loopc - 1.
    ENDIF.
*   move fields from work area to scrren fields
    MOVE-CORRESPONDING wa_ekko TO ztc_ekko.
    ld_line =  sy-stepl + tc100-top_line - 1.
*   Changes individual field attributes of table control,
*   Sets EBELN field on 3rd row of TC to not be an input field!
    LOOP AT SCREEN.
      IF ld_line EQ 3.
        IF screen-name EQ 'ZTC_EKKO-EBELN'.
          screen-input = 0.
          MODIFY SCREEN.
        ENDIF.
      ENDIF.
    ENDLOOP.
  ENDMODULE.                 " populate_screen  OUTPUT "
```

