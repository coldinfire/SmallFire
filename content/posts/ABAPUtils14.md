---
title: "ABAP混合算术运算"
date: 2019-08-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap
  - utils

---

### 使用

在ABAP程序中将数值与表达式分别存放，通过表达式计算对应的结果。



```
DATA: JS_PROCESSOR TYPE REF TO CL_JAVA_SCRIPT.
"计算表达式
JS_PROCESSOR = CL_JAVA_SCRIPT=>CREATE( ).
CONCATENATE
  'var string = ' FORMULA ';'
  'function Set_String()                          '
  '  { string = eval(string);                     '
  '  }                                            '
  'Set_String();                                  '
  'string;                                        '
  INTO HSLM_SOURCE SEPARATED BY CL_ABAP_CHAR_UTILITIES=>CR_LF.
HSLM_VALUE = JS_PROCESSOR->EVALUATE( HSLM_SOURCE )
```

.



#### 程序内使用

​	程序内只是取出配置的值，该值存储在表TVARVC中。如果有前导零问题，需要调用Function添加前导零。
