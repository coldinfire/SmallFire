---
title: "报表开发<常用工具>"
date: 2018-09-21T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - 报表开发

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



