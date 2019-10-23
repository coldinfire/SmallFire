---
title: "ABAP 科学计数法问题"
date: 2019-10-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap
  - utils
---

#### 科学计数法转换数字

​	ABAP 函数 `QSS0_FLTP_TO_CHAR_CONVERSION`，能将 SAP 系统内的科学计数法显示的数字转换成一般数字。

```JS
DATA: l_result TYPE qsollwerte,
       l_value TYPE char16.
       
CALL FUNCTION 'QSS0_FLTP_TO_CHAR_CONVERSION'
  EXPORTING
    i_number_of_digits  = 2
    i_fltp_value        = l_result
  IMPORTING
    e_char_field        = l_value
           .
  CONDENSE l_value.
```



#### 数字千分位的转换处理

```jsp
DATA: p_value type p,
       l_value TYPE char16.

CALL FUNCTION 'HRCM_STRING_TO_AMOUNT_CONVERT'
  EXPORTING
    string            = l_value
    decimal_separator  = '.'
  IMPORTING
    betrg                     = p_value
  EXCEPTIONS
    convert_error             = 1
    OTHERS                    = 2
           .
IF sy-subrc = 1.
   CALL FUNCTION 'HRCM_STRING_TO_AMOUNT_CONVERT'
     EXPORTING
       string            = l_value
       decimal_separator = ','
     IMPORTING
       betrg             = p_value
     EXCEPTIONS
       convert_error     = 1
       OTHERS            = 2.
ENDIF.


```



既要有千分位又要把符号提前的情况:

```JS
*&---------------------------------------------------------------------*
*&      Form  NUMTOSTR
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->VALUE      text
*      -->(ZNUM)     text
*      -->VALUE      text
*      -->(ZSTR)     text
*----------------------------------------------------------------------*
FORM  numtostr CHANGING  p_is_result_amount
                         ``p_is_result_amount_h
                         ``p_is_result_total
                         ``p_is_result_total_h.
  ``DATA : zclen TYPE i,
  ``n TYPE i,
  ``zcstr(30) TYPE c,
  ``zcstr2(30) TYPE c,
  ``zctemp(3) TYPE c,
  ``zflag(1) TYPE c VALUE ``'.'``,
  ``zflag2 TYPE i VALUE 0,
  ``zcdec(30) TYPE c, "记录小数部分.
  ``znum(30),
  ``znum_h(30),
  ``ztotal(30),
  ``ztotal_h(30).
  ``"zstr = ``''``.
  ``znum = is_result-amount.
  ``znum_h = is_result-amount_h.
  ``ztotal = is_result-total.
  ``ztotal_h = is_result-total_h.
  ``CLEAR: is_result-amount,is_result-amount_h,is_result-total,is_result-total_h.
*---------------------------------------------------------------------------------------------*1
  ``IF znum <> 0.
    ``IF znum <= -1000.
      ``zflag2 = 1.
      ``znum = znum * ( -1 ).
    ``ENDIF.
    ``IF znum >= 1000.
      ``zcstr = znum.
* 压缩字符串，去除前面的空格。
      ``CONDENSE zcstr NO-GAPS.
* 分离整数与小数，好单独处理整数。
      ``SPLIT zcstr AT zflag INTO zcstr zcdec.
      ``zclen = ``strlen``( zcstr ).
* 在循环中从右面在每三位的前面加上一个逗号。
      ``WHILE zclen > 3.
        ``n = zclen - 3.
        ``zctemp = zcstr+n(3).
        ``IF NOT zcstr2 IS INITIAL.
          ``CONCATENATE zctemp zcstr2 INTO zcstr2 SEPARATED BY ``','``.
        ``ELSE.
          ``zcstr2 = zctemp.
        ``ENDIF.
        ``zclen = zclen - 3.
      ``ENDWHILE.
* 将不剩下的不足三位数加到前面
      ``CONCATENATE zcstr+0(zclen) zcstr2 INTO zcstr2 SEPARATED BY ``','``.
      ``IF zflag2 = 1.
        ``CONCATENATE ``'-'` `zcstr2 INTO zcstr2.
      ``ENDIF.
      ``CLEAR zcstr.
* 将处理过的整数与小数连接起来。
      ``IF ``strlen``( zcdec ) > 1.
        ``CONCATENATE zcstr2 zcdec INTO zcstr SEPARATED BY zflag.
      ``ELSE.
        ``CONCATENATE zcdec ``'00'` `INTO zcdec.
        ``CONCATENATE zcstr2 zcdec  INTO zcstr SEPARATED BY zflag.
      ``ENDIF.
* 将值返回
      ``is_result-amount = zcstr.
    ``ELSE.
      ``is_result-amount = znum.
    ``ENDIF.
  ``ENDIF.
  ``CLEAR: zflag2,zcstr,zcdec,zclen,zctemp,zcstr2,n.
ENDFORM
```



#### 计算数学表达式的方法

```JS
FORM cacule_value  USING    p_wf_formula TYPE string
                            p_source  TYPE string
                            p_js_processor TYPE REF TO cl_java_script
                   CHANGING p_value .
  DATA:molecule TYPE string,
     denominator TYPE string,
     denominator_source TYPE string,
     denominator_value  TYPE rr_szntr,
     l_result TYPE qsollwerte,
     l_value TYPE char16.

  IF p_wf_formula CS '/'.
    CLEAR:molecule,denominator.
    SPLIT p_wf_formula AT '/' INTO molecule denominator.
    CONCATENATE
    'var string = ' denominator ';'
    'function Set_String()                          '
    '  { string = eval(string);                     '
    '  }                                            '
    'Set_String();                                  '
    'string;                                        '
      INTO denominator_source SEPARATED BY cl_abap_char_utilities=>cr_lf.
    denominator_value = js_processor->evaluate( denominator_source ).
    IF denominator_value = 0.
      p_value = 0.
    ELSE.
      CONCATENATE
      'var string = ' p_wf_formula ';'
      'function Set_String()                          '
      '  { string = eval(string);                     '
      '  }                                            '
      'Set_String();                                  '
      'string;                                        '
        INTO p_source SEPARATED BY cl_abap_char_utilities=>cr_lf.
      l_result = js_processor->evaluate( p_source ).
*      p_value = js_processor->evaluate( p_source ).

      CALL FUNCTION 'QSS0_FLTP_TO_CHAR_CONVERSION'
        EXPORTING
          i_number_of_digits             = 2
          i_fltp_value                   = l_result
*         I_VALUE_NOT_INITIAL_FLAG       = 'X'
*         I_SCREEN_FIELDLENGTH           = 16
       IMPORTING
         e_char_field                   = l_value
                .
      CONDENSE l_value.

      CALL FUNCTION 'HRCM_STRING_TO_AMOUNT_CONVERT'
        EXPORTING
          string                    = l_value
         decimal_separator         = '.'
*         THOUSANDS_SEPARATOR       =
*         WAERS                     = ' '
       IMPORTING
         betrg                     = p_value
       EXCEPTIONS
         convert_error             = 1
         OTHERS                    = 2
                .
      IF sy-subrc = 1.
        CALL FUNCTION 'HRCM_STRING_TO_AMOUNT_CONVERT'
          EXPORTING
            string            = l_value
            decimal_separator = ','
          IMPORTING
            betrg             = p_value
          EXCEPTIONS
            convert_error     = 1
            OTHERS            = 2.
      ENDIF.

    ENDIF.
  ELSE.
    CONCATENATE
      'var string = ' p_wf_formula ';'
      'function Set_String()                          '
      '  { string = eval(string);                     '
      '  }                                            '
      'Set_String();                                  '
      'string;                                        '
        INTO p_source SEPARATED BY cl_abap_char_utilities=>cr_lf.
    p_value = js_processor->evaluate( p_source ).
  ENDIF.
ENDFORM.                    " CACULE_VALUE
```