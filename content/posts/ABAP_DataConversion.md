---
title: "数据输入输出转换"
date: 2018-06-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 输入输出转换

如果某个变量参照的数据所对应的Domain具有转换规则，在(Write,ALV,文本框显示)，最后结果会自动转换。

通过转换规则输入输出函数手动转换。

#### 去除前导零

- 去除前导0：`SHIFT ITAB-FIELD LEFT DELETING LEADING '0'.`

- ```js
  DEFINE conversion_output.
    CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
      EXPORTING
        input  = &1
      IMPORTING
        output = &1.  
  END-OF-DEFINITION.
  ```

添加前导零

```JS
DEFINE conversion_input.
  CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
    EXPORTING
      input  = &1  
    IMPORTING
      output = &1. 
END-OF-DEFINITION.
```

#### 日期转换

```html
DEFINE date_conversion.
  call function 'CONVERT_DATE_TO_EXTERNAL'
    exporting
      date_internal            = &1
    importing
      date_external            = &2
    exceptions
      date_internal_is_invalid = 1
      others                   = 2.
END-OF-DEFINITION.
```

#### 单位换算

UNIT_CONVERSION_SIMPLE

### 货币转换因子

```JS
OB07、OB08：维护各币种之间的汇率
CURRENCY_CONVERTING_FACTOR：输入币种，可以得到相应的转换比率。SE16中看到数据的经过转换后存入，取
出时应做转换
BAPI_CURRENCY_CONV_TO_INTERNAL：转换为数据库中内部存储金额
BAPI_CURRENCY_CONV_TO_EXTERNAL：转换成外部的实际金额
CONVERT_TO_LOCAL_CURRENCY：自动将最近时间多的汇率作为转换的汇率
CONVERT_TO_FOREIGN_CURRENCY：将外币转换为本位币
CONVERT_TO_LOCAL_CURRENCY：将本位币转换为其他外币
```

