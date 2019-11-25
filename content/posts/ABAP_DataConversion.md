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

​	如果某个变量参照的数据所对应的Domain具有转换规则，在(Write,ALV,文本框显示)，最后结果会自动转换。

​	通过转换规则输入输出函数手动转换，转换公式。CONVERSION_EXIT_ALPHA_INPUT/OUTPUT(前面补齐0，去掉前导0).

```JS
** 添加前导零 **
CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
  EXPORTING
    input  = p_in  
  IMPORTING
    output = p_out. 
      
** 去除前导零 ** 
CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
  EXPORTING
    input  = p_in     
  IMPORTING
    output = p_out.  
    
去除前导0：SHIFT ITAB-FIELD LEFT DELETING LEADING '0'.
```

#### 显示内容

​	基本数据类型是QUAN,其小数位由字段关联的度量衡单位决定。

​    ALV显示时，如果是金额或数量时，需通过Fieldcat设置cfieldname、ctabname等才会正确显示。

#### 单位换算

​	UNIT_CONVERSION_SIMPLE

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

