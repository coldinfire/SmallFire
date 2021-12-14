---
title: " SAP 外币金额汇率转换 "
date: 2021-10-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BAPI
  - abaputils

---

### SAP 金额/汇率处理逻辑

SAP 中对于金额和汇率字段的处理(一般是会计相关的：会计发票、销售发票、采购发票等)有点特殊，并不是说你在前台看到的数据是多少就在系统表中写多少。

**金额**：有些货币的金额因为有转换因子的存在，存入表之前 SAP 会先将金额除以转换因子再存入，这些金额在取出来的时候需要进行相应的转换。

- `BAPI_CURRENCY_CONV_TO_INTERNAL`：转换成数据库中内部存储金额
- `BAPI_CURRENCY_CONV_TO_EXTERNAL`：转换成外部实际金额

**汇率**：而有些汇率是会乘以一定的系数(一般也是100)，所以我们在通过汇率计算的时候需要除以一个数。系统提供了函数来读取金额和汇率的转换值。汇率分为：直接汇率(1外币=XX本位币)、间接汇率(XX外币=1本位币) 。

- 汇率存放在表 TCURR 中，另外 TCUR* 有关于汇率的其他数据
- 维护汇率的事物码：OB07、OB08
- 汇率转换的函数都在 LSCUNUXX 程序中

#### 处理场景

处理金额的时候需要乘以转换值：这个值可以通过`CURRENCY_CONVERTING_FACTOR`函数获得

处理汇率的时候需要除以转换值：这个值可以通过`READ_EXCHANGE_RATE`获得

#### 获取金额转换比率

```ABAP
DATA: lv_fact TYPE ISOC_FACTOR.
CALL FUNCTION 'CURRENCY_CONVERTING_FACTOR'
  EXPORTING
    currency          = 'JPY'
  IMPORTING
    factor            = lv_fact
  EXCEPTIONS
    too_many_decimals = 1
    OTHERS            = 2.
```

![Factor](/images/ABAP/ABAP_Amount_1.png)

#### 获取不同币种之间汇率比率

```ABAP
DATA:p_waers LIKE t001-waers .
DATA:l_fact TYPE i,
     rate  LIKE vbrp-kursk,.
CALL FUNCTION 'READ_EXCHANGE_RATE'
  EXPORTING
    date             = sy-datum  "汇率日期"
    foreign_currency = p_waers   "外币" 
    local_currency   = 'CNY'     "本币"
    type_of_rate     = 'M'       "类型"
  IMPORTING
    exchange_rete    = rate      "汇率"
    foreign_factor   = l_fact    "比率"
  EXCEPTIONS
    no_rate_found    = 1
    no_factors_found = 2
    no_spread_found  = 3
    derived_2_times  = 4
    overflow         = 5
    zero_rate        = 6
    OTHERS           = 7. 
```

#### 日元等特殊处理

1、Exporting 各个参数一定不能用常量，要用变量

2、碰到比较变态的货币，例如日元，它们是没有小数点的，系统内存储的和你看到的不同，可以使用 BAPI：`BAPI_CURRENCY_CONV_TO_INTERNAL` 将从系统取出的值转换为实际看到的值

```ABAP
REPORT ZTEST_CURRENCY_CONV.
PARAMETER: P_FC TYPE TCURC-WAERS DEFAULT 'JPY',
           P_TC TYPE TCURC-WAERS DEFAULT 'CNY',
           P_DATE TYPE SY-DATUM DEFAULT SY-DATUM,
           P_CURR TYPE BAPICURR-BAPICURR,
           P_KURST TYPE TCURR-KURST DEFAULT 'M'.
DATA: G_AMOUNT TYPE BSEG-WRBTR,
      GV_INTER LIKE BSEG-WRBTR,
      GV_FROM TYPE CHAR20,
      GV_TO   TYPE CHAR20.
"实际金额转换"
CALL FUNCTION 'BAPI_CURRENCY_CONV_TO_INTERNAL'
  EXPORTING
    currency             = P_FC
    amount_external      = P_CURR
    max_number_of_digits = 13
  IMPORTING
    amount_internal      = GV_INTER. 
"外币金额转换成本地金额"
CALL FUNCTION 'CONVERT_TO_LOCAL_CURRENCY'
  EXPORTING
    date             = P_DATE
    foreign_amount   = GV_INTER
    foreign_currency = P_FC
    local_currency   = P_TC
    type_of_rate     = P_KURST
  IMPORTING
    local_amount     = G_AMOUNT
  EXCEPTIONS
    no_rate_found    = 1
    OTHERS           = 2.
WRITE: / P_FC,'==》',P_TC.
WRITE: / P_CURR,'==》',G_AMOUNT.
```

