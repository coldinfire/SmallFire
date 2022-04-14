---
title: " 日期操作 "
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

- `CONVERSION_EXIT_IDATE_OUTPUT`：INPUT (20080203)；OUTPUT (03FEB2008)
- `CONVERT_DATE_TO_EXTERNAL`：INPUT (20080203)；OUTPUT (02/03/2008) 输出结果参照用户默认设置的格式
- `CONVERT_DATE_TO_INTERNAL`：INPUT (02/03/2008) 格式应与用户的默认设置相同；OUPUT (20080203)

#### 自定义工具

```ABAP
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
  SELECT SINGLE datfm INTO lv_datfm FROM usr01 UP TO 1 ROWS 
    WHERE bname = zname.
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

### F4 Help for Month

```ABAP
INCLUDE rmcs0f0m.
SELECT-OPTIONS: s_month FOR s001-spmon NO-EXTENSION NO INTERVALS OBLIGATORY.
AT SELECTION-SCREEN ON VALUE-REQUEST FOR s_month-low.
   PERFORM monat_f4 USING s_month-low.
   
FORM monat_f4 USING month.
  DATA lv_month TYPE isellist-month.
  FIELD-SYMBOLS <fs_field> TYPE any.
  lv_month = sy-datum+0(6).
 
  CALL FUNCTION 'POPUP_TO_SELECT_MONTH'
    EXPORTING
      actual_month               = lv_month
*     FACTORY_CALENDAR           = ' '
*     HOLIDAY_CALENDAR           = ' '
*     LANGUAGE                   = SY-LANGU
*     START_COLUMN               = 8
*     START_ROW                  = 5
    IMPORTING
      selected_month             = lv_month
*     RETURN_CODE                =
    EXCEPTIONS
      factory_calendar_not_found = 1
      holiday_calendar_not_found = 2
      month_not_found            = 3
      OTHERS                     = 4.
  IF sy-subrc = 0.
    CHECK lv_month <> '000000'.
    ASSIGN month TO <fs_field>.
    IF <fs_field> IS ASSIGNED.
      <fs_field> = lv_month.
      UNASSIGN <fs_field>.
    ENDIF.
  ENDIF.
  
ENDFORM.
```

### 常用的日期函数

#### 日期有效性检查 

```ABAP
CALL FUNCTION 'DATE_CHECK_PLAUSIBILITY'
  EXPORTING
    DATE                        = sy-datum
  EXCEPTIONS
    PLAUSIBILITY_CHECK_FAILED   = 1
    OTHERS                      = 2.
IF sy-subrc <> 0.
  MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
     WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.
```

#### 根据日期计算另一个日期

返回指定月以前的日期

```ABAP
DATA DATE LIKE SCAL-DATE.
CALL FUNCTION ‘CCM_GO_BACK_MONTHS’ 
  EXPORTING 
    CURRDATE = sy-datum 
    BACKMONTHS = 6 
  IMPORTING 
    NEWDATE = DATE . 
```

输入一个日期，输入间隔的天、月、年，输入运算符，函数返回计算出的日期。

```ABAP
CALL FUNCTION 'RP_CALC_DATE_IN_INTERVAL'
  EXPORTING
    DATE            = SY-DATUM
    DAYS            = 1
    MONTHS          = 0
    SIGNUM          = '+' "+，-"
    YEARS           = 0
  IMPORTING
    CALC_DATE       = DATE_RESULT.
```

#### 查询两个日期间的日间间隔

返回两个日期之间的年数、月数、天数

```ABAP
CALL FUNCTION 'FIMA_DAYS_AND_MONTHS_AND_YEARS'
  EXPORTING 
    I_DATE_FROM = '20180202'
* I_KEY_DAY_FROM = 
    I_DATE_TO   = '20180919'
* I_KEY_DAY_TO  = 
* I_FLG_SEPARATE = ’ ’ 
  IMPORTING 
    E_DAYS   = E_DAYS    "229:相差天数"
    E_MONTHS = E_MONTHS  "8:相差月数" 
    E_YEARS  = E_YEARS . "1:相差年数"
```

两个日期作差，即是两个日期相减，包括当天时间。

```ABAP
DATA: datediff TYPE p,
      timediff TYPE p,
      earliest TYPE c.
CALL FUNCTION 'SD_DATETIME_DIFFERENCE'
  EXPORTING
    date1            = '20140101'
    time1            = '240000'
    date2            = '20140101'
    time2            = '083000'
  IMPORTING
    datediff         = datediff     "返回日期差：0 "
    timediff         = timediff     "返回时间差：16"
    earliest         = earliest     "返回时间正负：2，1-负 0-相等 2-正"
  EXCEPTIONS
    invalid_datetime = 1
    OTHERS           = 2.
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

```ABAP
DATA week LIKE scal-week.
CALL FUNCTION 'DATE_GET_WEEK'
  EXPORTING
    date               = sy-datum
  IMPORTING
    week               = week
  EXCEPTIONS
    date_invalid       = 1
    others             = 2.
IF sy-subrc <> 0.
  MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
         WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.
```

#### 获得某周的第一天日期 

```ABAP
DATA DATE LIKE SCAL-DATE. 
CALL FUNCTION ‘WEEK_GET_FIRST_DAY’ 
  EXPORTING 
    WEEK = '201848'
  IMPORTING 
    DATE = DATE.  "2018-11-26"
```

