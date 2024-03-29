---
title: " ABAP Json 转换 "
date: 2021-03-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

## ABAP 对象和 JSON 格式的转换工具

### 创建工具类

SE24：创建类,输入描述并选择类型

![工具类创建](/images/ABAP/ABAP_JSON0.png)

点击保存

![工具类创建](/images/ABAP/ABAP_JSON1.png)

### 定义属性值

![Attributes](/images/ABAP/ABAP_JSON3.png)

![Attributes](/images/ABAP/ABAP_JSON4.png)

![Attributes](/images/ABAP/ABAP_JSON5.png)

### 需要创建的方法

- [方法详细代码列表](https://coldinfire.github.io/2021/ABAP_JSON_Methods/)

![包含方法](/images/ABAP/ABAP_JSON2.png)

### 定义Types

![Types Define](/images/ABAP/ABAP_JSON6.png)

### Section 定义

#### Public section

```ABAP
class ZCL_FDT_JSON definition
  public
  final
  create public .

*"* public components of class ZCL_FDT_JSON
*"* do not include other source files here!!!
public section.

  types:
    BEGIN OF FDT_S_AMOUNT,
          NUMBER TYPE FDT_NUMBER_DECFLOAT,
          CURRENCY TYPE FDT_CURRENCY,
          END OF FDT_S_AMOUNT .
  types:
    BEGIN OF FDT_S_QUANTITY,
        NUMBER TYPE FDT_NUMBER_DECFLOAT,
        UNIT TYPE FDT_UNIT,
        END OF FDT_S_QUANTITY .
  types:
    BEGIN OF FDT_S_TIMEPOINT,
        DATE TYPE DATS,
        TIME TYPE TIMS,
        TIMESTAMP TYPE FDT_TIMESTAMP,
        OFFSET_TIME TYPE CHAR3,
        OFFSET_SIGN TYPE TZNUTCSIGN,
        TYPE TYPE FDT_TIMEPOINT_TYPE,
        END OF FDT_S_TIMEPOINT .

  class-methods DATA_TO_JSON
    importing
      !IA_DATA type ANY
    returning
      value(RV_JSON) type STRING .
  class-methods JSON_TO_DATA
    importing
      !IV_JSON type STRING
    changing
      !CA_DATA type ANY .
  interface IF_FDT_TYPES load .
  class-methods TO_STING
    importing
      !IS_AMOUNT type IF_FDT_TYPES=>ELEMENT_AMOUNT
    returning
      value(RV_STRING) type IF_FDT_TYPES=>ELEMENT_TEXT .
  class-methods QUANTITY_TO_STING
    importing
      !IS_QUANTITY type IF_FDT_TYPES=>ELEMENT_QUANTITY
    returning
      value(RV_STRING) type IF_FDT_TYPES=>ELEMENT_TEXT .
  class-methods CONVERT_TIMEPOINT_TO_TEXT
    importing
      !IS_TIMEPOINT type FDT_S_TIMEPOINT
    returning
      value(RV_TEXT) type IF_FDT_TYPES=>ELEMENT_TEXT .
  class-methods GET_CURRENCY_DECIMALS
    importing
      !IV_CURRENCY type SYCURR
    returning
      value(RV_DECIMALS) type CURRDEC .
  class-methods _ENSURE_DIGITS
    changing
      !CHV_OFFSET_MINUTES type CHAR3 optional
      !CHV_OFFSET_TIME type CHAR5 optional
      !CHV_YEAR type IF_FDT_TYPES=>ELEMENT_TEXT optional
      !CHV_MONTH type IF_FDT_TYPES=>ELEMENT_TEXT optional
      !CHV_WEEK type IF_FDT_TYPES=>ELEMENT_TEXT optional
      !CHV_DAY type IF_FDT_TYPES=>ELEMENT_TEXT optional
      !CHV_HOUR type IF_FDT_TYPES=>ELEMENT_TEXT optional
      !CHV_MINUTE type IF_FDT_TYPES=>ELEMENT_TEXT optional
      !CHV_SECOND type IF_FDT_TYPES=>ELEMENT_TEXT optional .
  class-methods IS_VALID
    importing
      !IS_TIMEPOINT type FDT_S_TIMEPOINT
    returning
      value(RV_IS_VALID) type IF_FDT_TYPES=>ELEMENT_BOOLEAN .
  class-methods IS_NULL
    importing
      !IS_TIMEPOINT type FDT_S_TIMEPOINT
    returning
      value(RV_IS_NULL) type IF_FDT_TYPES=>ELEMENT_BOOLEAN .
  class-methods _GET_TIMEZONE
    importing
      !IV_SIGN type C
      !IV_MINUTES type C
    returning
      value(RV_TIMEZONE) type TZNZONE .
```

#### Protected Section

```ABAP
*"* protected components of class ZCL_FDT_JSON
*"* do not include other source files here!!!
PROTECTED SECTION.
  CLASS-DATA:GC_ABAP_DECIMALS_SEPARATOR TYPE C VALUE '.'.
  CLASS-DATA:GC_DECIMALS_DEFAULT TYPE I VALUE '3'.
  CLASS-DATA GC_ELEMENT_AMOUNT TYPE CHAR200 VALUE '\INTERFACE=IF_FDT_TYPES\TYPE=ELEMENT_AMOUNT'. "#EC NOTEXT       " .
  CLASS-DATA GC_ELEMENT_QUANTITY TYPE CHAR200 VALUE '\INTERFACE=IF_FDT_TYPES\TYPE=ELEMENT_QUANTITY'. "#EC NOTEXT       " .
  CLASS-DATA GC_ELEMENT_BOOLEAN TYPE CHAR200 VALUE '\INTERFACE=IF_FDT_TYPES\TYPE=ELEMENT_BOOLEAN'. "#EC NOTEXT       " .
  CLASS-DATA GC_ELEMENT_NUMBER TYPE CHAR200 VALUE '\INTERFACE=IF_FDT_TYPES\TYPE=ELEMENT_NUMBER'. "#EC NOTEXT       " .
  CLASS-DATA GC_ELEMENT_TIMEPOINT TYPE CHAR200 VALUE '\INTERFACE=IF_FDT_TYPES\TYPE=ELEMENT_TIMEPOINT'. "#EC NOTEXT       " .
  CLASS-DATA GC_ELEMENT_TEXT TYPE CHAR200 VALUE '\INTERFACE=IF_FDT_TYPES\TYPE=ELEMENT_TEXT'. "#EC NOTEXT       " .
  CLASS-DATA GC_ABAP_BOOL TYPE CHAR200 VALUE '\TYPE-POOL=ABAP\TYPE=ABAP_BOOL'. "#EC NOTEXT
  CLASS-DATA GC_FDT_BOOLEAN TYPE CHAR200 VALUE '\TYPE=FDT_BOOLEAN'. "#EC NOTEXT
  CLASS-DATA GC_FDT_AMOUNT TYPE CHAR200 VALUE '\TYPE=FDT_S_AMOUNT'. "#EC NOTEXT
  CLASS-DATA GC_FDT_QUANTITY TYPE CHAR200 VALUE '\TYPE=FDT_S_QUANTITY'. "#EC NOTEXT
  CLASS-DATA GC_FDT_TIMEPOINT TYPE CHAR200 VALUE '\TYPE=FDT_S_TIMEPOINT'. "#EC NOTEXT
  CLASS-DATA GC_FDT_NUMBER TYPE CHAR200 VALUE '\TYPE=FDT_NUMBER_DECFLOAT'. "#EC NOTEXT
```

#### Private Section

```ABAP
*"* private components of class ZCL_FDT_JSON
*"* do not include other source files here!!!
PRIVATE SECTION.

  TYPES:
    BEGIN OF S_CURR_DEC,
         CURR TYPE IF_FDT_TYPES=>ELEMENT_CURRENCY,
         DEC  TYPE I,
      END OF S_CURR_DEC .
  TYPES:
    TH_CURR_DEC TYPE HASHED TABLE OF S_CURR_DEC WITH UNIQUE KEY CURR .

  CLASS-DATA GTH_CURR_DEC TYPE TH_CURR_DEC .

  CONSTANTS MC_UTC_M000 TYPE TZNZONE VALUE 'UTC'." ##NO_TEXT. "#EC N
  CONSTANTS MC_UTC_M060 TYPE TZNZONE VALUE 'UTC-1'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_M120 TYPE TZNZONE VALUE 'UTC-2'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_M180 TYPE TZNZONE VALUE 'UTC-3'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_M210 TYPE TZNZONE VALUE 'UTC-33'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_M240 TYPE TZNZONE VALUE 'UTC-4'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_M270 TYPE TZNZONE VALUE 'UTC-43'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_M300 TYPE TZNZONE VALUE 'UTC-5'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_M360 TYPE TZNZONE VALUE 'UTC-6'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_M420 TYPE TZNZONE VALUE 'UTC-7'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_M480 TYPE TZNZONE VALUE 'UTC-8'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_M510 TYPE TZNZONE VALUE 'UTC-83'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_M540 TYPE TZNZONE VALUE 'UTC-9'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_M600 TYPE TZNZONE VALUE 'UTC-10'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_M660 TYPE TZNZONE VALUE 'UTC-11'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_M720 TYPE TZNZONE VALUE 'UTC-12'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P000 TYPE TZNZONE VALUE 'UTC'." ##NO_TEXT. "#EC N
  CONSTANTS MC_UTC_P060 TYPE TZNZONE VALUE 'UTC+1'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_P120 TYPE TZNZONE VALUE 'UTC+2'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_P180 TYPE TZNZONE VALUE 'UTC+3'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_P210 TYPE TZNZONE VALUE 'UTC+33'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P240 TYPE TZNZONE VALUE 'UTC+4'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_P270 TYPE TZNZONE VALUE 'UTC+43'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P300 TYPE TZNZONE VALUE 'UTC+5'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_P330 TYPE TZNZONE VALUE 'UTC+53'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P345 TYPE TZNZONE VALUE 'UTC+45'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P360 TYPE TZNZONE VALUE 'UTC+6'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_P390 TYPE TZNZONE VALUE 'UTC+63'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P420 TYPE TZNZONE VALUE 'UTC+7'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_P480 TYPE TZNZONE VALUE 'UTC+8'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_P540 TYPE TZNZONE VALUE 'UTC+9'." ##NO_TEXT. "#EC
  CONSTANTS MC_UTC_P570 TYPE TZNZONE VALUE 'UTC+93'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P600 TYPE TZNZONE VALUE 'UTC+10'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P660 TYPE TZNZONE VALUE 'UTC+11'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P720 TYPE TZNZONE VALUE 'UTC+12'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P780 TYPE TZNZONE VALUE 'UTC+13'." ##NO_TEXT.                                   "#E
  CONSTANTS MC_UTC_P840 TYPE TZNZONE VALUE 'UTC+14'." ##NO_TEXT.                                   "#E

  CLASS-METHODS BRF_STRUC_TO_JSON
    IMPORTING
      !IA_DATA TYPE ANY
      !IV_TYPE TYPE CHAR200
    RETURNING
      VALUE(RV_JSON) TYPE STRING .
  CLASS-METHODS CONVERT_TOKEN
    IMPORTING
      !IA_DATA TYPE ANY
    CHANGING
      !CV_TOKEN TYPE ANY .
  CLASS-METHODS DATA_TO_JSON_INTERNAL
    IMPORTING
      !IA_DATA TYPE ANY
      !IO_DESCR TYPE REF TO CL_ABAP_TYPEDESCR OPTIONAL
    RETURNING
      VALUE(RV_JSON) TYPE STRING .
  CLASS-METHODS GET_STRING
    EXPORTING
      !EV_STRING TYPE STRING
    CHANGING
      !CV_JSON TYPE STRING .
  CLASS-METHODS JSON_TO_BRF_STRUC
    CHANGING
      !CV_JSON TYPE STRING
      !CA_DATA TYPE ANY .
  CLASS-METHODS JSON_TO_DATA_INTERNAL
    IMPORTING
      !IV_IS_TABLINE TYPE ABAP_BOOL DEFAULT ABAP_FALSE
    EXPORTING
      !EV_IS_FILLED TYPE ABAP_BOOL
    CHANGING
      !CV_JSON TYPE STRING
      !CA_DATA TYPE ANY .
```

