---
title: " ABAP 混合算术运算 "
date: 2019-08-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 使用

在ABAP程序中将数值与表达式分别存放，通过表达式计算对应的结果。

#### 一、调用BAPI：EVAL_FORMULA

```ABAP
TYPES:BEGIN OF TY_VAL,
      OPERAND TYPE C,
      VALUE TYPE STRING,
      END OF TY_VAL.
TYPES:BEGIN OF TY_FRM,
      NO TYPE I,
      FORMULA TYPE CHAR50,
      END OF TY_FRM.
DATA:IT_VAL TYPE TABLE OF TY_VAL,
     WA_VAL TYPE TY_VAL.
DATA:IT_FORMULA TYPE TABLE OF TY_FRM,
     WA_FRM TYPE TY_FRM.
DATA:WF_STRING TYPE CHAR255,
     WF_LEN TYPE I,
     WF_FORMULA TYPE CHAR50.
DATA: WF_RETCODE      LIKE SY-SUBRC,
      WF_FUNCNAME(30) TYPE C,
      WF_MESSAGE(70)  TYPE C,
      WF_POS          TYPE I,
      WF_C            TYPE P LENGTH 15 DECIMALS 2.
      
WA_VAL-OPERAND = 'A'.
WA_VAL-VALUE = '1'.
APPEND WA_VAL TO IT_VAL.
WA_VAL-OPERAND = 'B'.
WA_VAL-VALUE = '2'.
APPEND WA_VAL TO IT_VAL.
WA_VAL-OPERAND = 'C'.
WA_VAL-VALUE = '3'.
APPEND WA_VAL TO IT_VAL.
WA_FRM-NO = 1.
WA_FRM-FORMULA = 'A*B+C'.
APPEND WA_FRM TO IT_FORMULA.
WA_FRM-NO = 2.
WA_FRM-FORMULA = 'C*(A-B)'.
APPEND WA_FRM TO IT_FORMULA.
LOOP AT IT_FORMULA INTO WA_FRM.
  WF_STRING = WA_FRM-FORMULA.
  WF_FORMULA = WA_FRM-FORMULA.
  REPLACE ALL OCCURRENCES OF REGEX '[^[:alpha:]]' IN WF_STRING WITH ` `.
  CONDENSE WF_STRING NO-GAPS.
  WF_LEN = STRLEN( WF_STRING ).
  DO WF_LEN TIMES.
    SY-INDEX = SY-INDEX - 1.
    READ TABLE IT_VAL INTO WA_VAL WITH KEY OPERAND = WF_STRING+SY-INDEX(1).
    IF SY-SUBRC = 0.
      REPLACE ALL OCCURRENCES OF WF_STRING+SY-INDEX(1)
                              IN WF_FORMULA
                              WITH WA_VAL-VALUE.
    ENDIF.
  ENDDO.
  CALL FUNCTION 'CHECK_FORMULA'
    EXPORTING
      FORMULA  = WF_FORMULA
    IMPORTING
      SUBRC    = WF_RETCODE
      FUNCNAME = WF_FUNCNAME
      MESSAGE  = WF_MESSAGE
      POS      = WF_POS.
  IF WF_RETCODE IS INITIAL.
    CALL FUNCTION 'EVAL_FORMULA'
      EXPORTING
        FORMULA = WF_FORMULA
      IMPORTING
        VALUE   = WF_C
      EXCEPTIONS
        OTHERS  = 1.
    IF SY-SUBRC = 0.
      WRITE: / WA_FRM-FORMULA,'-', WF_C.
    ELSE.
      WRITE: / 'Error'..
    ENDIF.
  ELSE.
    WRITE: / WF_FUNCNAME, WF_MESSAGE, WF_POS.
  ENDIF.
ENDLOOP.
```

**替换表达式的运算单位**

- `REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:]]' IN WF_STRING WITH ‘ ’` .
- `REPLACE ALL OCCURRENCES OF REGEX '[^[:alpha:]]' IN WF_STRING WITH ‘ ’` .
- ``：两个表示空格

**将字符串切分保存到内表**

*    `SPLIT  WF_STRING  AT SPACE INTO TABLE table_name.`

#### 二、通过直接调用表达式完成计算

```ABAP
DATA: MESSAGE TYPE STRING,
      SOURCE TYPE STRING,
      RETURN_VALUE TYPE P LENGTH 15 DECIMALS 2,
      TXT_VAR1 TYPE CHAR40,
      TXT_VAR2 TYPE CHAR40,
      TXT_VAR3 TYPE CHAR40 VALUE '987979.234-'.

CALL FUNCTION 'CLOI_PUT_SIGN_IN_FRONT'
  CHANGING
    VALUE  = TXT_VAR3.

MESSAGE = '(-351422999.55+-59211228.95+-1297670.94+-3135583.53+-35337844.40)*100/(613848716.07+0.00+-1552672.73)'.

DATA: JS_PROCESSOR TYPE REF TO CL_JAVA_SCRIPT.
  JS_PROCESSOR = CL_JAVA_SCRIPT=>CREATE( ).
	CONCATENATE
      'var string = ' MESSAGE ';'
      'function Set_String()                          '
      '  { string = eval(string);                     '
      '  }                                            '
      'Set_String();                                  '
      'string;                                        '
  	INTO SOURCE SEPARATED BY
    CL_ABAP_CHAR_UTILITIES=>CR_LF.
RETURN_VALUE = JS_PROCESSOR->EVALUATE( SOURCE ).
WRITE: RETURN_VALUE.
```

#### 实际使用

可以将以上两种方法进行结合，计算出表达式的结果。