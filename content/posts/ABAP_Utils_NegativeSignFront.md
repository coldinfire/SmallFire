---
title: " 负号前置 "
date: 2018-08-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

SAP 系统中，很多种情况下负号都是在数字后面，如果在显示或计算数据时需要将负号放到数字前面。

### 调用系统BAPI

可以使用 function module：`CLOI_PUT_SIGN_IN_FRONT`

```ABAP
CALL FUNCTION 'CLOI_PUT_SIGN_IN_FRONT'
  CHANGING
    VALUE  = Value.
```

![负号前置](/images/ABAP/MinusTop.png)

### 自定义实现

定义函数：`CONVERSION_EXIT_Z001_OUTPUT.`

作用：
* 将金额类型等数字类型，负号实现前置
* 可以保留千分位
* 适用于多个这样的字段修改需求

调用方式：
* 在对应的 alv 设置 fieldcat 时，针对设置金额等数字类型的字段添加代码：`fcat-edit_mask = '==Z001'.`

```ABAP
FUNCTION conversion_exit_z001_output.
*"-------------------------------------------------------
*"  IMPORTING
*"     REFERENCE(INPUT)
*"  EXPORTING
*"     REFERENCE(OUTPUT)
*"--------------------------------------------------------
DATA: output1(20),
      output2(20),
      outnum(16) TYPE p DECIMALS 3.
IF input IS NOT INITIAL .
   outnum = input.
   IF input > 0.
     WRITE outnum TO output1.
   ELSE.
     outnum = outnum * ( -1 ).
     WRITE outnum TO output1.
     CONCATENATE '-' output1 INTO output1.
   ENDIF.
ELSE.
   CLEAR output1.
ENDIF.
 CONDENSE output1 NO-GAPS.
 WRITE output1 TO output2 RIGHT-JUSTIFIED.
 output = output2.
 CLEAR: output2.
ENDFUNCTION.
```