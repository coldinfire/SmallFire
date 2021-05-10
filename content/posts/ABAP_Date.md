---
title: "日期操作"
date: 2018-12-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 日期格式转换

#### 系统标准Function

```html
CONVERSION_EXIT_IDATE_OUTPUT
  INPUT:   20080203
  OUTPUT:  03FEB2008

CONVERT_DATE_TO_EXTERNAL
  INPUT:   20080203
  OUTPUT:  02/03/2008  "According to user's default setting.

CONVERT_DATE_TO_INTERNAL
  INPUT:   02/03/2008  "Should be same as the user's default setting
  OUPUT:   20080203
```

#### 自定义工具

```html
FUNCTION ZCONVERT_DATE_FORMAT.
*"----------------------------------------------------------------------
*"*"Local interface:
*"  IMPORTING
*"     REFERENCE(ZNAME) LIKE  USR01-BNAME
*"  CHANGING
*"     REFERENCE(ZDATE) TYPE  C
*"----------------------------------------------------------------------
DATA: date_format LIKE usr01-datfm,
      year(4) TYPE c,
      month(2) TYPE c,
      day(2) TYPE c.
  zdate = sy-datum.
  lv_year = zdate+0(4).
  lv_month = zdate+4(2).
  lv_day = zdate+6(2).
  SELECT SINGLE datfm 
    INTO lv_datfm FROM usr01
    UP TO 1 ROWS 
    WHERE bname = zname .
  IF sy-subrc = 0.
    CLEAR zdate.
    CASE lv_datfm.
      WHEN '1'.
        CONCATENATE lv_day lv_month lv_year INTO zdate SEPARATED BY '.'.
      WHEN '2'.
        CONCATENATE lv_month lv_day lv_year INTO zdate SEPARATED BY '/'.
      WHEN '3'.
        CONCATENATE lv_month lv_day lv_year INTO zdate SEPARATED BY '-'.
      WHEN '4'.
        CONCATENATE lv_year lv_month lv_day INTO zdate SEPARATED BY '.'.
      WHEN '5'.
        CONCATENATE lv_year lv_month lv_day INTO zdate SEPARATED BY '/'.
      WHEN '6'.
        CONCATENATE lv_year lv_month lv_day INTO zdate SEPARATED BY '-'.
    ENDCASE.
  ELSE.
    CLEAR zdate.
    CONCATENATE lv_month lv_day lv_year INTO zdate SEPARATED BY '.'.
  ENDIF.
ENDFUNCTION.
```

### 常用的日期函数

#### 日期有效性检查 

```html
CALL FUNCTION 'DATE_CHECK_PLAUSIBILITY'
  EXPORTING
    date                            = sy-datum
  EXCEPTIONS
   PLAUSIBILITY_CHECK_FAILED       = 1
   OTHERS                          = 2
           .
IF sy-subrc <> 0.
  MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
         WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.
```

#### 根据日期计算另一个日期

输入一个日期，输入间隔的天、月、年，输入运算符，函数返回计算出的日期。

```html
CALL FUNCTION 'RP_CALC_DATE_IN_INTERVAL'
  EXPORTING
    DATE            = SY-DATUM
    DAYS            = 1
    MONTHS          = 0
    SIGNUM          = '+'
    YEARS           = 0
  IMPORTING
    CALC_DATE       = DATE_RESULT.
```

#### 查询两个日期间的日间间隔

分别输入开始日期和结束日期，函数返回两个日期间隔的天数、月数、和年数。

```html
CALL FUNCTION 'FIMA_DAYS_AND_MONTHS_AND_YEARS'
    EXPORTING
      I_DATE_FROM     = '20080101'
*     I_KEY_DAY_FROM  =
      I_DATE_TO       = '20090508'
*     I_KEY_DAY_TO    =
*     I_FLG_SEPARATE  = ' '
   IMPORTING
     E_DAYS           = DAYS
     E_MONTHS         = MONTHS
     E_YEARS          = YEARS .
```

分别输入开始日期、开始时间、结束日期、结束时间，函数返回两个日期间隔秒数。

```ABAP
DATA: diff_second TYPE int4.
CALL FUNCTION 'SALP_SM_CALC_TIME_DIFFERENCE'
  EXPORTING
    date_1  = '20080801'
    time_1  = '17:22:12'
    date_2  = '20080802'
    time_2  = '18:22:12'
  IMPORTING
    seconds = diff_second.   "86,400"
```

#### 获取日期所在的周数

```html
DATA WEEK LIKE SCAL-WEEK.
CALL FUNCTION 'DATE_GET_WEEK'
  EXPORTING
    date               = sy-datum
  IMPORTING
    WEEK               = WEEK
  EXCEPTIONS
    DATE_INVALID       = 1
    OTHERS             = 2
            .
IF sy-subrc <> 0.
  MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
         WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.
```

