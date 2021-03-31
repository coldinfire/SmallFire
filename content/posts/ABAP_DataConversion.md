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

#### 货币汇率转换

- OB07、OB08：维护各币种之间的汇率，保存之后存储在表 **TCURR** 中

转换函数列表

| Function                       | Desc                                                         |
| :----------------------------- | :----------------------------------------------------------- |
| CURRENCY_CONVERTING_FACTOR     | 输入币种，可以得到相应的转换比率;SE16中看到数据的经过转换后存入，取出时应做转换 |
| BAPI_CURRENCY_CONV_TO_INTERNAL | 转换成数据库中内部存储金额                                   |
| BAPI_CURRENCY_CONV_TO_EXTERNAL | 转换成外部的实际金额                                         |
| CONVERT_TO_LOCAL_CURRENCY      | 自动将最近时间段的汇率作为转换的汇率，也可指定汇率           |
| CONVERT_TO_FOREIGN_CURRENCY    | 将外币转换为本位币                                           |
| CONVERT_TO_LOCAL_CURRENCY      | 将本位币转换为其他外币                                       |

CONVERT_TO_LOCAL_CURRENCY 实例

- 注意：保持 FOREIGN_AMOUNT 和 LOCAL_AMOUNT 的参考类型一致

```ABAP
DATA: from_curr TYPE TCURC-WAERS VALUE 'USD',
      to_curr TYPE TCURC-WAERS VALUE 'HKD', 
      rate TYPE bapi1093_0.
DATA: po_netpr TYPE p DECIMALS 7,
      po_netpr_local TYPE p DECIMALS 7.
CALL FUNCTION 'BAPI_EXCHANGERATE_GETDETAIL'
    EXPORTING
      rate_type  = 'M'
      from_curr  = from_curr
      to_currncy = to_curr
      date       = sy-datum
    IMPORTING
      exch_rate  = rate.
      
CALL FUNCTION 'CONVERT_TO_LOCAL_CURRENCY'
    EXPORTING
      DATE             = sy-datum
      FOREIGN_AMOUNT   = po_netpr
      FOREIGN_CURRENCY = f_curr
      LOCAL_CURRENCY   = l_curr
      RATE             = rate-exch_rate
*      TYPE_OF_RATE            = 'M'
*      READ_TCURR              = 'X'
    IMPORTING
      LOCAL_AMOUNT     = po_netpr_local
    EXCEPTIONS
      NO_RATE_FOUND    = 1
      OTHERS           = 2.
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

基本单位的转换函数

- **UNIT_CONVERSION_SIMPLE** ：1min = 60s

物料单位的转换函数

- **MD_CONVERT_MATERIAL_UNIT** - 计量单位之间转换
- **MATERIAL_UNIT_CONVERSION** - 每基本单位等于多少计量单位