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
