---
title: "报表开发<常用工具>"
date: 2018-07-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

## 单元格中的数据被修改后，将ALV单元格中的数据立即刷新到ABAP对应的内表中

方法一：通过对REUSE_ALV_GRID_DISPLAY函数参数i_grid_settings-edt_cll_cb进行设置

```JS
i_grid_settings-edt_cll_cb  = 'X' .
CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
EXPORTING i_grid_settings = i_grid_settings
```

方法二：通过函数参数I_CALLBACK_USER_COMMAND指定的回调Form的参数slis_selfield进行设置

```JS
FORM user_command USING ucomm LIKE sy-ucommselfield selfield TYPE slis_selfield.
    selfield-refresh = 'X'.
  CASE ucomm.
    WHEN 'UPDATE'.
    PERFORM frm_update.
  ENDCASE.
ENDFORM. 
```



## 自定义按钮使用后刷新ALV

```jS
form frm_user_command using r_ucomm     like sy-ucomm
                       rs_selfield type slis_selfield.
 case r_ucomm.
  when 'PROFIT'.
    perform frm_cycle_count_profit.
  when others.
 endcase.
   rs_selfield-refresh = 'X'.
endform.                    "frm_user_command
```

## Search help 的创建

`PARAMETERS p_cur TYPE XXX VALUE CHECK .`

`PROCESS ON VALUE-REQUEST.`

` AT SELECTION-SCREEN ON VALUE-REQUEST.`

在屏幕的 ON VALUE---REQUEST 事件里可以通过下面几个函数来创建搜索帮助：

-   F4IF_FIELD_VALUE_REQUEST ： 函数的作用是在运行时，可以动态的为某个屏幕字段指定Search Help ，这个被引用的 Help 来自某个表（或结构）字段上绑定的 Help

-  F4IF_INT_TABLE_VALUE_REQUEST : 在程序运行时， 将某个内表动态的用作 Search help 的数据来源,即使用该函数可以将某个内表转换为 Search help ，可实现联动效果

- TR_F4_HELP ：简单实现 Search Help ，数据来源于内表

```js
DATA:BEGIN OF it_matf4 OCCURS 0 ,
      zucode LIKE zecs_mat-zimport_code,
      g_code LIKE zecs_mat-g_code,
      g_name LIKE zecs_mat-g_name,
      zstate LIKE zecs_hd-zstate,
      zby LIKE zecs_hd-zby,
     END OF it_matf4.
     
DATA: it_return LIKE ddshretval OCCURS 0 WITH HEADER LINE.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR S_TIMSTP-LOW.
  SELECT *
    INTO CORRESPONDING FIELDS OF TABLE IT_MATF4
    FROM <Table>
    WHERE <Var> = <Option>.
    
  CALL FUNCTION 'F4IF_INT_TABLE_VALUE_REQUEST'   "F4输出函数
    EXPORTING
*   DDIC_STRUCTURE         = ' '
     RETFIELD               = 'ZUCODE'   "field of internal table
     VALUE_ORG              = 'S'
     CALLBACK_PROGRAM = SY-REPID
     CALLBACK_FORM = 'FRM_F4CALLBACK'
    TABLES
      VALUE_TAB              = IT_MATF4
*   FIELD_TAB              =
     RETURN_TAB             = IT_RETURN
            .
  WRITE IT_RETURN-FIELDVAL TO S_TIMSTP-LOW.
  REFRESH IT_MATF4.
  
*&---------------------------------------------------------------------*
*&      Form  FRM_F4CALLBACK
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->RECORD_TAB   text
*      -->SHLP         text
*      -->CALLCONTROL  text
*----------------------------------------------------------------------*
FORM frm_f4callback TABLES record_tab 
					STRUCTURE seahlpres
					CHANGING shlp TYPE shlp_descr
      				  callcontrol LIKE ddshf4ctrl.
  DATA: wa_selopt TYPE ddshselopt.
  DATA:lt_value TYPE TABLE OF ddshfprop,
        ls_value TYPE ddshfprop.
  lt_value[] = shlp-fieldprop[].
  READ TABLE lt_value INTO ls_value WITH KEY fieldname = 'F0004'.
  IF sy-subrc = 0.
    ls_value-defaultval = '''4'''.
    MODIFY lt_value FROM ls_value INDEX sy-tabix.
  ENDIF.
  shlp-fieldprop[] = lt_value[].
ENDFORM.                    "FRM_F4CALLBACK
```

## 双击字段功能

```JS
FORM frm_user_command USING r_ucomm LIKE sy-ucomm
							rs_selfield TYPE slis_selfield.
                            
  IF r_ucomm = '&IC1'.
  	myindex = rs_selfield-tabindex.
    READ it_table INDEX myindex.
    SUBMIT z_pro AND RETURN WITH p_param1 = it_table-param1
							WITH p_param2 = it_table-param2.
    "如果是TCode - CALL Transation
	"传入输入参数值并调用其他TCode
 	SET PARAMETER ID 'ANR' FIELD ls_upload-aufnr.
  	CALL TRANSACTION 'CO03' AND SKIP FIRST SCREEN."跳过第一屏屏幕
   ENDIF.

ENDFORM.
```

