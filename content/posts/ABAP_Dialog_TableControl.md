---
atitle: " Dialog 表格控件 "
date: 2018-09-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Dialog

---



### Dynpro

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

