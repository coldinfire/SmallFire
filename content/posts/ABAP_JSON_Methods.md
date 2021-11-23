---
title: " ABAP&Json转换Methods "
date: 2021-03-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 方法列表

#### DATA_TO_JSON

![Method parameters](/images/ABAP/ABAP_JSON7.png)

```ABAP
METHOD DATA_TO_JSON.
  DATA:LO_DESCR TYPE REF TO CL_ABAP_TYPEDESCR.
  LO_DESCR = CL_ABAP_TYPEDESCR=>DESCRIBE_BY_DATA( IA_DATA ).
  RV_JSON = DATA_TO_JSON_INTERNAL( IA_DATA = IA_DATA IO_DESCR = LO_DESCR ).
  "Test if root is an single element, if yes root object { ... } needed"
  IF LO_DESCR->KIND EQ CL_ABAP_TYPEDESCR=>KIND_ELEM.
    CONCATENATE '{"$ROOT":' RV_JSON '}' INTO RV_JSON.
  ENDIF.
ENDMETHOD.
```

#### JSON_TO_DATA

![Method parameters](/images/ABAP/ABAP_JSON8.png)

```ABAP
METHOD JSON_TO_DATA.
  DATA:LV_JSON TYPE STRING.
  LV_JSON = IV_JSON.
  JSON_TO_DATA_INTERNAL(
    CHANGING
      CV_JSON = LV_JSON
      CA_DATA = CA_DATA ).
ENDMETHOD.
```

#### TO_STRING

![Method parameters](/images/ABAP/ABAP_JSON9.png)

```ABAP
METHOD TO_STING.
* number is not rounded, maximum precision is preserved: 123.456 JPY -> 123.456 JPY
* trailing zero decimals are cut off: 123,456000 JPY -> 123,456 JPY
* number of currency decimals is ensured: 123 EUR -> 123.00 EUR
* for negative amounts the negative sign is rendered before the number
  DEFINE RETURN_STRING.
    " negative sign shall be in front of number "
    IF LV_IS_NEGATIVE EQ ABAP_TRUE.
      CONCATENATE '-' LV_STRING INTO LV_STRING.
    ENDIF.
    CONCATENATE LV_STRING IS_AMOUNT-CURRENCY INTO RV_STRING
      SEPARATED BY SPACE.
    CONDENSE RV_STRING.
    RETURN.
  END-OF-DEFINITION.
* check if this is a negative number
  DATA: LV_IS_NEGATIVE TYPE BOOLE_D VALUE ABAP_FALSE.
  IF IS_AMOUNT-NUMBER LT 0.
    LV_IS_NEGATIVE = ABAP_TRUE.
  ENDIF.
* move to local, preserving precision
  DATA: LV_STRING TYPE STRING.
  MOVE IS_AMOUNT-NUMBER TO LV_STRING.
* throw away sign
  REPLACE ALL OCCURRENCES OF '+' IN LV_STRING WITH SPACE.
  REPLACE ALL OCCURRENCES OF '-' IN LV_STRING WITH SPACE.
  CONDENSE LV_STRING.
* cut off trailing zero decimals
  IF LV_STRING CA '.'.
    SHIFT LV_STRING RIGHT DELETING TRAILING '0'.
  ENDIF.
* cut off decimal separator if there are no decimals anymore
  IF LV_STRING CA '.'.
    SHIFT LV_STRING RIGHT DELETING TRAILING '.'.
  ENDIF.
  CONDENSE LV_STRING.
* ensure number of decimals according to currency
* get decimals from currency
  DATA: LV_TCURX_DECIMALS TYPE I.
  LV_TCURX_DECIMALS = GET_CURRENCY_DECIMALS( IV_CURRENCY = IS_AMOUNT-CURRENCY ).
  IF LV_TCURX_DECIMALS EQ 0.
    " no decimals to ensure ... "
    RETURN_STRING.
  ENDIF.
* split into integer and decimals part
  DATA: LV_INTEGER_PART TYPE STRING,
        LV_DECIMAL_PART TYPE STRING.
  SPLIT LV_STRING AT GC_ABAP_DECIMALS_SEPARATOR
   INTO LV_INTEGER_PART LV_DECIMAL_PART.
  CONDENSE: LV_INTEGER_PART,LV_DECIMAL_PART.
* get number of decimals
  DATA: LV_DECIMALS_COUNT TYPE I.
  LV_DECIMALS_COUNT = STRLEN( LV_DECIMAL_PART ).
  IF LV_DECIMALS_COUNT GE LV_TCURX_DECIMALS.
   "there are already enough decimals"
    RETURN_STRING.
  ENDIF.
* ensure decimals, add trailing zeros
  DATA: LV_TIMES TYPE I.
  LV_TIMES = LV_TCURX_DECIMALS - LV_DECIMALS_COUNT.
  DO LV_TIMES TIMES.
    CONCATENATE LV_DECIMAL_PART '0' INTO LV_DECIMAL_PART.
  ENDDO.
* build number
  CONCATENATE LV_INTEGER_PART
              GC_ABAP_DECIMALS_SEPARATOR
              LV_DECIMAL_PART
         INTO LV_STRING.
  RETURN_STRING.
ENDMETHOD.
```

#### QUANTITY_TO_STING

![Method parameters](/images/ABAP/ABAP_JSON10.png)

```ABAP
method QUANTITY_TO_STING.
* number is not rounded, maximum precision is preserved: 123.456789 KG -> 123.456789 KG
* trailing zero decimals are cut off: 123.45000 KG -> 123.45 KG
* number of T006-DECAN decimals is ensured: 123 D10  -> 123.000 D10
* accept initial/invalid unit, set decimals to 3
* NO user-dependent formatting: no thousands separator, decimals separator is ABAP default '.'
* for negative quantites the negative sign is rendered before the number
  DEFINE RETURN_STRING.
    " negative sign shall be in front of number "
    IF LV_IS_NEGATIVE EQ ABAP_TRUE.
      CONCATENATE '-' LV_STRING INTO LV_STRING.
    ENDIF.
    CONCATENATE LV_STRING IS_QUANTITY-UNIT INTO RV_STRING
      SEPARATED BY SPACE.
    CONDENSE RV_STRING.
    RETURN.
  END-OF-DEFINITION.
* check if this is a negative number
  DATA: LV_IS_NEGATIVE TYPE BOOLE_D VALUE ABAP_FALSE.
  IF IS_QUANTITY-NUMBER LT 0.
    LV_IS_NEGATIVE = ABAP_TRUE.
  ENDIF.
* move to local
  DATA: LV_STRING TYPE STRING.
  MOVE IS_QUANTITY-NUMBER TO LV_STRING.
* throw away sign
  REPLACE ALL OCCURRENCES OF '+' IN LV_STRING WITH SPACE.
  REPLACE ALL OCCURRENCES OF '-' IN LV_STRING WITH SPACE.
  CONDENSE LV_STRING.
* cut off trailing zero decimals
  IF LV_STRING CA GC_ABAP_DECIMALS_SEPARATOR.
    SHIFT LV_STRING RIGHT DELETING TRAILING '0'.
  ENDIF.
* cut off decimal separator if there are no decimals anymore
  IF LV_STRING CA GC_ABAP_DECIMALS_SEPARATOR.
    SHIFT LV_STRING RIGHT DELETING TRAILING GC_ABAP_DECIMALS_SEPARATOR.
  ENDIF.
  CONDENSE LV_STRING.
* ensure number of decimals according to unit
* get decimals from unit
  DATA: LV_DECAN TYPE T006-DECAN.
  IF IS_QUANTITY-UNIT IS NOT INITIAL.
    CALL FUNCTION 'UNIT_PARAMETERS_GET'
      EXPORTING
        unit           = IS_QUANTITY-UNIT
      IMPORTING
        decan          = LV_DECAN
      EXCEPTIONS
        unit_not_found = 1.
    IF sy-subrc = 1.
      "unit invalid, default is 3"
      LV_DECAN = gc_decimals_default.
    ENDIF.
  ELSE.
    "default is 3"
    lv_decan = gc_decimals_default.
  ENDIF.
  IF lv_decan EQ 0.
    "no decimals to ensure ..."
    return_string.
  ENDIF.
* split into integer and decimals part
  DATA: lv_integer_part TYPE string,
        lv_decimal_part TYPE string.
  SPLIT lv_string AT gc_abap_decimals_separator
   INTO lv_integer_part lv_decimal_part.
  CONDENSE: lv_integer_part,
            lv_decimal_part.
* get number of decimals
  DATA: lv_decimals_count TYPE i.
  lv_decimals_count = strlen( lv_decimal_part ).
  IF lv_decimals_count GE lv_decan.
    "there are already enough decimals"
    return_string.
  ENDIF.
* ensure decimals, add trailing zeros
  DATA: lv_times TYPE i.
  lv_times = lv_decan - lv_decimals_count.
  DO lv_times TIMES.
    CONCATENATE lv_decimal_part '0' INTO lv_decimal_part.
  ENDDO.
* build number
  CONCATENATE lv_integer_part
              gc_abap_decimals_separator
              lv_decimal_part
         INTO lv_string.
  return_string.
endmethod.
```

#### CONVERT_TIMEPOINT_TO_TEXT

![Method parameters](/images/ABAP/ABAP_JSON11.png)

```ABAP
METHOD CONVERT_TIMEPOINT_TO_TEXT.
  CONSTANTS MC_DATE_NULL TYPE DATS VALUE '00010101'.         "#NO_TEXT"
  CONSTANTS MC_TIME_ZERO TYPE TIMS VALUE '000000'.           "#NO_TEXT"
  CONSTANTS MC_TIMESTAMP_NULL TYPE IF_FDT_TYPES=>TIMESTAMP VALUE '00010101000000'."#NO_TEXT"
  CONSTANTS MC_DST_FIRST_YEAR TYPE TZNYEARACT VALUE '1916'.  "#NO_TEXT #EC NOT"
  CONSTANTS MC_EMPTY_TIMEZONE TYPE TIMEZONE VALUE '      '.  "#NO_TEXT #EC NOT"
  CONSTANTS MC_OFFSET_MINUTES_INITIAL TYPE CHAR3 VALUE '000'."#NO_TEXT #EC"
  CONSTANTS MC_OFFSET_SIGN_MINUS TYPE TZNUTCSIGN VALUE '-'.  "#NO_TEXT #EC NOT"
  CONSTANTS MC_OFFSET_SIGN_PLUS TYPE TZNUTCSIGN VALUE '+'.   "#NO_TEXT #EC NOTE"
  CONSTANTS MC_OFFSET_TIME_INITIAL TYPE CHAR5 VALUE '00:00'. "#NO_TEXT #EC NO"
  CONSTANTS MC_SEPARATOR_ISO_DATE TYPE C VALUE '-'.          "#NO_TEXT #EC"
  CONSTANTS MC_SEPARATOR_ISO_INTERVAL TYPE C VALUE '/'.      "#NO_TEXT"
  CONSTANTS MC_SEPARATOR_ISO_TIME TYPE C VALUE ':'.          "#NO_TEXT"
  CONSTANTS MC_INDICATOR_TIME TYPE C VALUE 'T'.              "#NO_TEXT"
  CONSTANTS MC_INDICATOR_UTC TYPE C VALUE 'Z'.               "#NO_TEXT"
  DATA: LS_MESSAGE TYPE IF_FDT_TYPES=>S_MESSAGE,
        LT_MESSAGE TYPE IF_FDT_TYPES=>T_MESSAGE.
* Prepare return value
  CLEAR RV_TEXT.
* Move to local FDT timepoint
  DATA: LS_TIMEPOINT_LOCAL TYPE FDT_S_TIMEPOINT.
* Move to local FDT timepoint
  CASE IS_TIMEPOINT-TYPE.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATE OR SPACE.
      LS_TIMEPOINT_LOCAL-TYPE = IF_FDT_CONSTANTS=>GC_TP_DATE.
      IF IS_TIMEPOINT-DATE IS INITIAL OR IS_TIMEPOINT-DATE EQ SPACE.
        LS_TIMEPOINT_LOCAL-DATE = MC_DATE_NULL.
      ELSE.
        LS_TIMEPOINT_LOCAL-DATE = IS_TIMEPOINT-DATE.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_TIME.
      LS_TIMEPOINT_LOCAL-TYPE = IF_FDT_CONSTANTS=>GC_TP_TIME.
      IF IS_TIMEPOINT-TIME IS INITIAL OR IS_TIMEPOINT-TIME EQ SPACE.
        LS_TIMEPOINT_LOCAL-TIME = MC_TIME_ZERO.
      ELSE.
        LS_TIMEPOINT_LOCAL-TIME = IS_TIMEPOINT-TIME.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATETIME.
      LS_TIMEPOINT_LOCAL-TYPE = IF_FDT_CONSTANTS=>GC_TP_DATETIME.
      IF IS_TIMEPOINT-DATE IS INITIAL OR IS_TIMEPOINT-DATE EQ SPACE.
        LS_TIMEPOINT_LOCAL-DATE = MC_DATE_NULL.
      ELSE.
        LS_TIMEPOINT_LOCAL-DATE = IS_TIMEPOINT-DATE.
      ENDIF.
      IF IS_TIMEPOINT-TIME IS INITIAL OR IS_TIMEPOINT-TIME EQ SPACE.
        LS_TIMEPOINT_LOCAL-TIME = MC_TIME_ZERO.
      ELSE.
        LS_TIMEPOINT_LOCAL-TIME = IS_TIMEPOINT-TIME.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_TIMESTAMP_UTC.
      LS_TIMEPOINT_LOCAL-TYPE = IF_FDT_CONSTANTS=>GC_TP_TIMESTAMP_UTC.
      IF IS_TIMEPOINT-TIMESTAMP IS INITIAL OR IS_TIMEPOINT-TIMESTAMP EQ SPACE.
        LS_TIMEPOINT_LOCAL-TIMESTAMP = MC_TIMESTAMP_NULL.
      ELSE.
        LS_TIMEPOINT_LOCAL-TIMESTAMP = IS_TIMEPOINT-TIMESTAMP.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATETIME_OFFSET_UTC.
      LS_TIMEPOINT_LOCAL-TYPE = IF_FDT_CONSTANTS=>GC_TP_DATETIME_OFFSET_UTC.
      IF IS_TIMEPOINT-DATE IS INITIAL OR IS_TIMEPOINT-DATE EQ SPACE.
        LS_TIMEPOINT_LOCAL-DATE = MC_DATE_NULL.
      ELSE.
        LS_TIMEPOINT_LOCAL-DATE = IS_TIMEPOINT-DATE.
      ENDIF.
      IF IS_TIMEPOINT-TIME IS INITIAL OR IS_TIMEPOINT-TIME EQ SPACE.
        LS_TIMEPOINT_LOCAL-TIME = MC_TIME_ZERO.
      ELSE.
        LS_TIMEPOINT_LOCAL-TIME = IS_TIMEPOINT-TIME.
      ENDIF.
      IF IS_TIMEPOINT-OFFSET_SIGN IS INITIAL OR IS_TIMEPOINT-OFFSET_SIGN EQ SPACE.
        LS_TIMEPOINT_LOCAL-OFFSET_SIGN = MC_OFFSET_SIGN_PLUS.
      ELSE.
        LS_TIMEPOINT_LOCAL-OFFSET_SIGN = IS_TIMEPOINT-OFFSET_SIGN.
      ENDIF.
      LS_TIMEPOINT_LOCAL-OFFSET_TIME = IS_TIMEPOINT-OFFSET_TIME.
      _ENSURE_DIGITS( CHANGING CHV_OFFSET_MINUTES = LS_TIMEPOINT_LOCAL-OFFSET_TIME ).
  ENDCASE.
* Check condition: Timepoint is valid
  DATA: LV_IS_VALID TYPE IF_FDT_TYPES=>ELEMENT_BOOLEAN.
  LV_IS_VALID = IS_VALID( LS_TIMEPOINT_LOCAL ).
* Timepoint is invalid -> Raise Exception
  IF LV_IS_VALID EQ ABAP_FALSE.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
       WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4
       INTO LS_MESSAGE-TEXT.
    MOVE-CORRESPONDING SYST TO LS_MESSAGE.
    LS_MESSAGE-SOURCE = CX_FDT=>GET_SOURCE_NAME( ).
    APPEND LS_MESSAGE TO LT_MESSAGE.
    CLEAR LS_MESSAGE.
    MESSAGE E052(FDT_SERVICE) INTO LS_MESSAGE-TEXT.
    MOVE-CORRESPONDING SYST TO LS_MESSAGE.
    LS_MESSAGE-SOURCE = CX_FDT=>GET_SOURCE_NAME( ).
    APPEND LS_MESSAGE TO LT_MESSAGE.

    RAISE EXCEPTION TYPE CX_FDT_CONVERSION
      EXPORTING
        MT_MESSAGE = LT_MESSAGE.
  ENDIF.
** This is an AP system
*  IF cl_fdt_environment=>ap_is_on( ) EQ abap_true.
**   cast FDT to /ISDT/
*    DATA: ls_timepoint TYPE /isdt/s_timepoint.
*    cast_fdt_to_isdt ls_timepoint_local ls_timepoint.
**   call /ISDT/ service
*    DATA: lv_timepoint_iso TYPE /isdt/datetime_iso.
*    /isdt/cl_udtm=>convert_tp_to_iso( EXPORTING is_timepoint     = ls_timepoint
*                                      IMPORTING ev_timepoint_iso = lv_timepoint_iso ).
*    rv_text = lv_timepoint_iso.
*    RETURN.
*  ENDIF.
* This is not an AP system
  DATA: LV_DATE_ISO  TYPE  IF_FDT_TYPES=>ELEMENT_TEXT,
        LV_TIME_ISO  TYPE  IF_FDT_TYPES=>ELEMENT_TEXT,
        LS_TP_DATE   TYPE  FDT_S_TIMEPOINT,
        LS_TP_TIME   TYPE  FDT_S_TIMEPOINT.
  CLEAR RV_TEXT.
  CASE LS_TIMEPOINT_LOCAL-TYPE.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATE.
      "YYYY-MM-DD"
      CONCATENATE LS_TIMEPOINT_LOCAL-DATE+0(4)  MC_SEPARATOR_ISO_DATE
                  LS_TIMEPOINT_LOCAL-DATE+4(2)  MC_SEPARATOR_ISO_DATE
                  LS_TIMEPOINT_LOCAL-DATE+6(2)
             INTO RV_TEXT.
    WHEN IF_FDT_CONSTANTS=>GC_TP_TIME.
      "Thh:mm:ss "to be compatible with NW710"
      CONCATENATE MC_INDICATOR_TIME  "see ISO8601
                  LS_TIMEPOINT_LOCAL-TIME+0(2)  MC_SEPARATOR_ISO_TIME
                  LS_TIMEPOINT_LOCAL-TIME+2(2)  MC_SEPARATOR_ISO_TIME
                  LS_TIMEPOINT_LOCAL-TIME+4(2)
             INTO RV_TEXT.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATETIME.
      "Convert both parts individually and concatenate them
      "YYYY-MM-DDThh:mm:ss
      LS_TP_DATE-TYPE = IF_FDT_CONSTANTS=>GC_TP_DATE.
      LS_TP_DATE-DATE = LS_TIMEPOINT_LOCAL-DATE.
      LV_DATE_ISO     = CONVERT_TIMEPOINT_TO_TEXT( LS_TP_DATE ).
      LS_TP_TIME-TYPE = IF_FDT_CONSTANTS=>GC_TP_TIME.
      LS_TP_TIME-TIME = LS_TIMEPOINT_LOCAL-TIME.
      LV_TIME_ISO     = CONVERT_TIMEPOINT_TO_TEXT( LS_TP_TIME ).
      CONCATENATE LV_DATE_ISO+0(10) LV_TIME_ISO+0(9)
             INTO RV_TEXT.
    WHEN IF_FDT_CONSTANTS=>GC_TP_TIMESTAMP_UTC.
      "YYYY-MM-DDThh:mm:ssZ"
      LS_TP_DATE-TYPE = IF_FDT_CONSTANTS=>GC_TP_DATE.
      LS_TP_TIME-TYPE = IF_FDT_CONSTANTS=>GC_TP_TIME.
      CONVERT TIME STAMP LS_TIMEPOINT_LOCAL-TIMESTAMP
              TIME ZONE  MC_EMPTY_TIMEZONE
              INTO DATE  LS_TP_DATE-DATE
                   TIME  LS_TP_TIME-TIME.
      LV_DATE_ISO     = CONVERT_TIMEPOINT_TO_TEXT( LS_TP_DATE ).
      LV_TIME_ISO     = CONVERT_TIMEPOINT_TO_TEXT( LS_TP_TIME ).
      CONCATENATE LV_DATE_ISO+0(10)
                  LV_TIME_ISO+0(9)
                  MC_INDICATOR_UTC
             INTO RV_TEXT.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATETIME_OFFSET_UTC.
      "YYYY-MM-DDThh:mm:ss[+|-]hh:mm"
      LS_TP_DATE-TYPE = IF_FDT_CONSTANTS=>GC_TP_DATE.
      LS_TP_DATE-DATE = LS_TIMEPOINT_LOCAL-DATE.
      LV_DATE_ISO     = CONVERT_TIMEPOINT_TO_TEXT( LS_TP_DATE ).
      LS_TP_TIME-TYPE = IF_FDT_CONSTANTS=>GC_TP_TIME.
      LS_TP_TIME-TIME = LS_TIMEPOINT_LOCAL-TIME.
      LV_TIME_ISO     = CONVERT_TIMEPOINT_TO_TEXT( LS_TP_TIME ).
      "prepare offset"
      DATA: LV_OFFSET_HOURS_I   TYPE I VALUE '00',
            LV_OFFSET_MINUTES_I TYPE I VALUE '00',
            LV_OFFSET_HOURS     TYPE C LENGTH 2,
            LV_OFFSET_MINUTES   TYPE C LENGTH 2,
            LV_OFFSET_TIME      TYPE C LENGTH 5.

      IF LS_TIMEPOINT_LOCAL-OFFSET_TIME IS INITIAL OR
         LS_TIMEPOINT_LOCAL-OFFSET_TIME EQ MC_OFFSET_MINUTES_INITIAL.
        LV_OFFSET_TIME = MC_OFFSET_TIME_INITIAL.
      ELSE.
        LV_OFFSET_HOURS_I = LS_TIMEPOINT_LOCAL-OFFSET_TIME DIV 60.
        LV_OFFSET_HOURS = LV_OFFSET_HOURS_I.

        LV_OFFSET_MINUTES_I = LS_TIMEPOINT_LOCAL-OFFSET_TIME MOD 60.
        LV_OFFSET_MINUTES = LV_OFFSET_MINUTES_I.

        CONCATENATE LV_OFFSET_HOURS
                    MC_SEPARATOR_ISO_TIME
                    LV_OFFSET_MINUTES
               INTO LV_OFFSET_TIME.
        _ENSURE_DIGITS( CHANGING CHV_OFFSET_TIME = LV_OFFSET_TIME ).

      ENDIF.
      CONCATENATE LV_DATE_ISO+0(10)
                  LV_TIME_ISO+0(9)
                  LS_TIMEPOINT_LOCAL-OFFSET_SIGN
                  LV_OFFSET_TIME
             INTO RV_TEXT.
  ENDCASE.
ENDMETHOD.
```

#### GET_CURRENCY_DECIMALS

![Method parameters](/images/ABAP/ABAP_JSON12.png)

```ABAP
METHOD GET_CURRENCY_DECIMALS.
* iv_relative not supported, deprecated.
  DATA: LS_TCURX    TYPE TCURX,
        LS_CURR_DEC TYPE S_CURR_DEC.
  FIELD-SYMBOLS: <LS_CURR_DEC> TYPE S_CURR_DEC.
* always return the default
  IF IV_CURRENCY IS INITIAL.
    RV_DECIMALS = GC_DECIMALS_DEFAULT.
    RETURN.
  ENDIF.
* lookup buffer
  READ TABLE GTH_CURR_DEC WITH TABLE KEY CURR = IV_CURRENCY
    ASSIGNING <LS_CURR_DEC>.
  IF <LS_CURR_DEC> IS ASSIGNED.
    RV_DECIMALS = <LS_CURR_DEC>-DEC.
    RETURN.
  ENDIF.
* select from TCURX; TCURX contains only currencies with decimals other than 2
* if not found there rv_decimals must be 2
  SELECT SINGLE * FROM TCURX INTO LS_TCURX WHERE CURRKEY = IV_CURRENCY.
  IF SY-SUBRC = 0.
    RV_DECIMALS = LS_TCURX-CURRDEC.
  ELSE.
    RV_DECIMALS = GC_DECIMALS_DEFAULT.
  ENDIF.
* write buffer
  LS_CURR_DEC-CURR = IV_CURRENCY.
  LS_CURR_DEC-DEC  = RV_DECIMALS.
  INSERT LS_CURR_DEC INTO TABLE GTH_CURR_DEC.
ENDMETHOD.
```

#### _ENSURE_DIGITS

![Method parameters](/images/ABAP/ABAP_JSON13.png)

```ABAP
METHOD _ENSURE_DIGITS.
  CONSTANTS MC_OFFSET_MINUTES_INITIAL TYPE CHAR3 VALUE '000'. "#NO_TEXT"
  CONSTANTS MC_SEPARATOR_ISO_TIME TYPE C VALUE ':'.           "#NO_TEXT"
  DEFINE ENSURE.
    IF CHV_&1 IS SUPPLIED.
      "ensure that &1 has two digits"
      IF STRLEN( CHV_&1 ) EQ 0.
        CHV_&1 = '00'.
      ELSEIF STRLEN( CHV_&1 ) EQ 1.
        IF CHV_&1 IS INITIAL.
          CHV_&1 = '0'.
        ENDIF.
        CONCATENATE '0' CHV_&1 INTO CHV_&1.
      ELSEIF STRLEN( CHV_&1 ) EQ 2.
        IF CHV_&1 IS INITIAL.
          CHV_&1 = '00'.
        ENDIF.
        LV_CHAR2 = CHV_&1.
        CHV_&1 = LV_CHAR2+0(2).
      ENDIF.
      "ensure that every digit is not SPACE but at least 0"
      CLEAR LV_CHAR2.
      LV_CHAR2 = CHV_&1.
      IF LV_CHAR2+0(1) EQ SPACE.
        LV_CHAR2+0(1) = '0'.
      ENDIF.
      IF LV_CHAR2+1(1) EQ SPACE.
        LV_CHAR2+1(1) = '0'.
      ENDIF.
      CHV_&1 = LV_CHAR2+0(2).
    ENDIF.
  END-OF-DEFINITION.
  
  IF CHV_OFFSET_MINUTES IS SUPPLIED.
    "ensure that offset_minutes has three digits"
    IF STRLEN( CHV_OFFSET_MINUTES ) EQ 0.
      CHV_OFFSET_MINUTES = MC_OFFSET_MINUTES_INITIAL.
    ELSEIF STRLEN( CHV_OFFSET_MINUTES ) EQ 1.
      CONCATENATE '00' CHV_OFFSET_MINUTES INTO CHV_OFFSET_MINUTES.
    ELSEIF STRLEN( CHV_OFFSET_MINUTES ) EQ 2.
      CONCATENATE '0' CHV_OFFSET_MINUTES INTO CHV_OFFSET_MINUTES.
    ENDIF.
    "ensure that every digit is not SPACE but at least 0"
    IF CHV_OFFSET_MINUTES+0(1) EQ SPACE.
      CHV_OFFSET_MINUTES+0(1) = '0'.
    ENDIF.
    IF CHV_OFFSET_MINUTES+1(1) EQ SPACE.
      CHV_OFFSET_MINUTES+1(1) = '0'.
    ENDIF.
    IF CHV_OFFSET_MINUTES+2(1) EQ SPACE.
      CHV_OFFSET_MINUTES+2(1) = '0'.
    ENDIF.
  ENDIF.

  IF CHV_OFFSET_TIME IS SUPPLIED.
    DATA: LV_OFFSET_TIME_HOURS   TYPE C LENGTH 2,
          LV_OFFSET_TIME_MINUTES TYPE C LENGTH 2.
    SPLIT CHV_OFFSET_TIME
       AT MC_SEPARATOR_ISO_TIME
     INTO LV_OFFSET_TIME_HOURS LV_OFFSET_TIME_MINUTES.
    "ensure that offset_time_hours has two digits"
    IF STRLEN( LV_OFFSET_TIME_HOURS ) EQ 0.
      LV_OFFSET_TIME_HOURS = '00'.
    ELSEIF STRLEN( LV_OFFSET_TIME_HOURS ) EQ 1.
      CONCATENATE '0' LV_OFFSET_TIME_HOURS
             INTO LV_OFFSET_TIME_HOURS.
    ENDIF.
    "ensure that every digit is not SPACE but at least 0"
    IF LV_OFFSET_TIME_HOURS+0(1) EQ SPACE.
      LV_OFFSET_TIME_HOURS+0(1) = '0'.
    ENDIF.
    IF LV_OFFSET_TIME_HOURS+1(1) EQ SPACE.
      LV_OFFSET_TIME_HOURS+1(1) = '0'.
    ENDIF.
    "ensure that offset_time_minutes has two digits"
    IF STRLEN( LV_OFFSET_TIME_MINUTES ) EQ 0.
      LV_OFFSET_TIME_MINUTES = '00'.
    ELSEIF STRLEN( LV_OFFSET_TIME_MINUTES ) EQ 1.
      CONCATENATE '0' LV_OFFSET_TIME_MINUTES
             INTO LV_OFFSET_TIME_MINUTES.
    ENDIF.
    "ensure that every digit is not SPACE but at least 0"
    IF LV_OFFSET_TIME_MINUTES+0(1) EQ SPACE.
      LV_OFFSET_TIME_MINUTES+0(1) = '0'.
    ENDIF.
    IF LV_OFFSET_TIME_MINUTES+1(1) EQ SPACE.
      LV_OFFSET_TIME_MINUTES+1(1) = '0'.
    ENDIF.
    CLEAR CHV_OFFSET_TIME.
    CONCATENATE LV_OFFSET_TIME_HOURS
                MC_SEPARATOR_ISO_TIME
                LV_OFFSET_TIME_MINUTES
           INTO CHV_OFFSET_TIME.
  ENDIF.
  
  IF CHV_YEAR IS SUPPLIED.
    "ensure that year has four digits"
    IF STRLEN( CHV_YEAR ) EQ 0.
      CHV_YEAR = '0000'.
    ELSEIF STRLEN( CHV_YEAR ) EQ 1.
      CONCATENATE '000' CHV_YEAR INTO CHV_YEAR.
    ELSEIF STRLEN( CHV_YEAR ) EQ 2.
      CONCATENATE '00' CHV_YEAR INTO CHV_YEAR.
    ELSEIF STRLEN( CHV_YEAR ) EQ 3.
      CONCATENATE '0' CHV_YEAR INTO CHV_YEAR.
    ENDIF.
    "ensure that every digit is not SPACE but at least 0"
    DATA: LV_CHAR4 TYPE C LENGTH 4.
    LV_CHAR4 = CHV_YEAR.
    IF LV_CHAR4+0(1) EQ SPACE.
      LV_CHAR4+0(1) = '0'.
    ENDIF.
    IF LV_CHAR4+1(1) EQ SPACE.
      LV_CHAR4+1(1) = '0'.
    ENDIF.
    IF LV_CHAR4+2(1) EQ SPACE.
      LV_CHAR4+2(1) = '0'.
    ENDIF.
    IF LV_CHAR4+3(1) EQ SPACE.
      LV_CHAR4+3(1) = '0'.
    ENDIF.
    CHV_YEAR = LV_CHAR4.
  ENDIF.
  
  DATA: LV_CHAR2 TYPE C LENGTH 2.
  ENSURE MONTH.
  ENSURE WEEK.
  ENSURE DAY.
  ENSURE HOUR.
  ENSURE MINUTE.
  ENSURE SECOND.
ENDMETHOD.
```

#### IS_VALID

![Method parameters](/images/ABAP/ABAP_JSON14.png)

```ABAP
METHOD IS_VALID.
  CONSTANTS MC_DATE_NULL TYPE DATS VALUE '00010101'.          "#NO_TEXT"
  CONSTANTS MC_EMPTY_TIMEZONE TYPE TIMEZONE VALUE '      '.   "#NO_TEXT"
  CONSTANTS MC_OFFSET_SIGN_PLUS TYPE TZNUTCSIGN VALUE '+'.    "#NO_TEXT"
  CONSTANTS MC_OFFSET_SIGN_MINUS TYPE TZNUTCSIGN VALUE '-'.   "#NO_TEXT"
  constants MC_OFFSET_MINUTES_INITIAL type CHAR3 value '000'. "#NO_TEXT"
  DATA: LS_MESSAGE TYPE IF_FDT_TYPES=>S_MESSAGE,
        LT_MESSAGE TYPE IF_FDT_TYPES=>T_MESSAGE.
* NULL Timepoint is valid
  DATA: LV_IS_NULL TYPE IF_FDT_TYPES=>ELEMENT_BOOLEAN.
  LV_IS_NULL = IS_NULL( IS_TIMEPOINT ).
  IF LV_IS_NULL EQ ABAP_TRUE.
    RV_IS_VALID = ABAP_TRUE.
    RETURN.
  ENDIF.
* switch from Julian to Gregorian calendar
* Thu, 04. October 1582 is followed by Fri, 15. October 1582
*  -> days between 05.Oct.1582 and 14.Oct.1582 do not exist
  IF IS_TIMEPOINT-TYPE EQ IF_FDT_CONSTANTS=>GC_TP_DATE.
    IF IS_TIMEPOINT-DATE BETWEEN '15821005' AND '15821014'.
      RV_IS_VALID = ABAP_FALSE.
      RETURN.
    ENDIF.
  ENDIF.
* determine validity
  DATA: LS_TP_DATE TYPE FDT_S_TIMEPOINT.
  DATA: LS_TP_TIME TYPE FDT_S_TIMEPOINT.
* default: timepoint is valid
  RV_IS_VALID = ABAP_TRUE.
* check validity according to timepoint type
  CASE IS_TIMEPOINT-TYPE.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATE.
      IF IS_TIMEPOINT-DATE EQ MC_DATE_NULL.
        RV_IS_VALID = ABAP_TRUE.
        RETURN.
      ENDIF.
      "move date to number, if number is 0 date is invalid."
      DATA: LV_DATE_I TYPE I.
      LV_DATE_I = IS_TIMEPOINT-DATE.
      IF LV_DATE_I IS INITIAL .
        RV_IS_VALID = ABAP_FALSE.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_TIME.
      "FDT does not handle time 24:00:00"
      IF IS_TIMEPOINT-TIME EQ '240000' .
        DATA: LV_MSGV1 TYPE SY-MSGV1.
        WRITE IS_TIMEPOINT-TIME TO LV_MSGV1.
        MESSAGE E050(FDT_SERVICE) WITH LV_MSGV1 INTO LS_MESSAGE-TEXT.
        RV_IS_VALID = ABAP_FALSE.
        RETURN.
      ENDIF.
      "Convert to integer value and back to time."
      "If the result is identical to the input it is valid."
      DATA: LV_TIME_I TYPE I,
            LV_TIME   TYPE TIMS.
      LV_TIME_I = IS_TIMEPOINT-TIME.
      LV_TIME   = LV_TIME_I.
      IF LV_TIME = IS_TIMEPOINT-TIME.
        RV_IS_VALID = ABAP_TRUE.
      ELSE.
        RV_IS_VALID = ABAP_FALSE.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATETIME.
      LS_TP_DATE-TYPE = IF_FDT_CONSTANTS=>GC_TP_DATE.
      LS_TP_DATE-DATE = IS_TIMEPOINT-DATE.
      LS_TP_TIME-TYPE = IF_FDT_CONSTANTS=>GC_TP_TIME.
      LS_TP_TIME-TIME = IS_TIMEPOINT-TIME.
      IF IS_VALID( LS_TP_DATE ) EQ ABAP_FALSE OR
         IS_VALID( LS_TP_TIME ) EQ ABAP_FALSE.
        RV_IS_VALID = ABAP_FALSE.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_TIMESTAMP_UTC.
      LS_TP_DATE-TYPE = IF_FDT_CONSTANTS=>GC_TP_DATE.
      LS_TP_TIME-TYPE = IF_FDT_CONSTANTS=>GC_TP_TIME.
      CONVERT TIME STAMP IS_TIMEPOINT-TIMESTAMP TIME ZONE MC_EMPTY_TIMEZONE
               INTO DATE LS_TP_DATE-DATE TIME LS_TP_TIME-TIME.
      IF SY-SUBRC EQ 12.
        RV_IS_VALID = ABAP_FALSE.
        RETURN.
      ENDIF.
      IF IS_VALID( LS_TP_DATE ) EQ ABAP_FALSE .
        RV_IS_VALID = ABAP_FALSE.
        RETURN.
      ENDIF.
      IF IS_VALID( LS_TP_TIME ) EQ ABAP_FALSE .
        RV_IS_VALID = ABAP_FALSE.
        RETURN.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATETIME_OFFSET_UTC.
      LS_TP_DATE-TYPE = IF_FDT_CONSTANTS=>GC_TP_DATE.
      LS_TP_DATE-DATE = IS_TIMEPOINT-DATE.
      LS_TP_TIME-TYPE = IF_FDT_CONSTANTS=>GC_TP_TIME.
      LS_TP_TIME-TIME = IS_TIMEPOINT-TIME.
      IF IS_VALID( LS_TP_DATE ) EQ ABAP_FALSE .
        RV_IS_VALID = ABAP_FALSE.
        RETURN.
      ENDIF.
      IF IS_VALID( LS_TP_TIME ) EQ ABAP_FALSE .
        RV_IS_VALID = ABAP_FALSE.
        RETURN.
      ENDIF.
* CHECK IS_TIMEPOINT
      RV_IS_VALID = ABAP_TRUE.
* check offset sign
      IF ( IS_TIMEPOINT-OFFSET_SIGN NE SPACE )                AND
         ( IS_TIMEPOINT-OFFSET_SIGN NE MC_OFFSET_SIGN_PLUS )  AND
         ( IS_TIMEPOINT-OFFSET_SIGN NE MC_OFFSET_SIGN_MINUS ) .
        RV_IS_VALID = ABAP_FALSE.
      ELSE.
*  check offset value
*  value is initial
        IF IS_TIMEPOINT-OFFSET_TIME IS INITIAL OR
           IS_TIMEPOINT-OFFSET_TIME EQ MC_OFFSET_MINUTES_INITIAL.
          RV_IS_VALID = ABAP_TRUE.
        ELSE.
* value is not initial => try to get timezone
          DATA: RV_TIMEZONE TYPE TZNZONE.
* Prepare return value
          CLEAR RV_TIMEZONE.
* map offset sign
          DATA: LV_SIGN TYPE C.
          IF IS_TIMEPOINT-OFFSET_SIGN EQ MC_OFFSET_SIGN_MINUS.
            LV_SIGN = 'M'.
          ELSE.
            LV_SIGN = 'P'.
          ENDIF.
* map offset minutes
          DATA: LV_MINUTES TYPE CHAR3.
          IF IS_TIMEPOINT-OFFSET_TIME IS INITIAL.
            LV_MINUTES = MC_OFFSET_MINUTES_INITIAL.
          ELSE.
            LV_MINUTES = IS_TIMEPOINT-OFFSET_TIME.
            " FDT: Difference between local and UTC time (in minutes)"
            _ENSURE_DIGITS( CHANGING CHV_OFFSET_MINUTES = LV_MINUTES ).   
          ENDIF.
*   get timezone
          RV_TIMEZONE = _GET_TIMEZONE( IV_SIGN     = LV_SIGN
                                       IV_MINUTES  = LV_MINUTES ).
          IF RV_TIMEZONE EQ MC_EMPTY_TIMEZONE.
            RV_IS_VALID = ABAP_FALSE.
          ENDIF.
        ENDIF.
      ENDIF.
      IF RV_IS_VALID EQ ABAP_FALSE.
        RV_IS_VALID = ABAP_FALSE.
        RETURN.
      ENDIF.
    WHEN OTHERS.
      "unsupported timepoint type"
      RV_IS_VALID = ABAP_FALSE.
      MESSAGE E051(FDT_SERVICE) WITH IS_TIMEPOINT-TYPE INTO LS_MESSAGE-TEXT.
      MOVE-CORRESPONDING SYST TO LS_MESSAGE.
      LS_MESSAGE-SOURCE = CX_FDT=>GET_SOURCE_NAME( ).
      APPEND LS_MESSAGE TO LT_MESSAGE.
      RAISE EXCEPTION TYPE CX_FDT_CONVERSION
        EXPORTING
          MT_MESSAGE = LT_MESSAGE.
  ENDCASE.
ENDMETHOD.
```

#### IS_NULL

![Method parameters](/images/ABAP/ABAP_JSON15.png)

```ABAP
METHOD IS_NULL.
  CONSTANTS MC_DATE_NULL TYPE DATS VALUE '00010101'.          "#NO_TEXT"
  CONSTANTS MC_TIME_ZERO TYPE TIMS VALUE '000000'.            "#NO_TEXT"
  CONSTANTS MC_TIMESTAMP_NULL TYPE IF_FDT_TYPES=>TIMESTAMP VALUE '00010101000000'. "#NO_TEXT"
  CONSTANTS MC_OFFSET_SIGN_PLUS TYPE TZNUTCSIGN VALUE '+'.    "#NO_TEXT"
  CONSTANTS MC_OFFSET_MINUTES_INITIAL TYPE CHAR3 VALUE '000'. "#NO_TEXT"
  DATA: LS_MESSAGE TYPE IF_FDT_TYPES=>S_MESSAGE,
        LT_MESSAGE TYPE IF_FDT_TYPES=>T_MESSAGE.
* set default
  RV_IS_NULL = ABAP_FALSE.
* handle every timepoint type
  CASE IS_TIMEPOINT-TYPE.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATE.
      IF IS_TIMEPOINT-DATE EQ MC_DATE_NULL.
        RV_IS_NULL = ABAP_TRUE.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_TIME.
      IF IS_TIMEPOINT-TIME IS INITIAL OR
         IS_TIMEPOINT-TIME EQ MC_TIME_ZERO.
        RV_IS_NULL = ABAP_TRUE.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATETIME.
      IF ( IS_TIMEPOINT-DATE EQ MC_DATE_NULL ) AND
         ( IS_TIMEPOINT-TIME IS INITIAL OR IS_TIMEPOINT-TIME EQ MC_TIME_ZERO ).
        RV_IS_NULL = ABAP_TRUE.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_TIMESTAMP_UTC.
      IF IS_TIMEPOINT-TIMESTAMP EQ MC_TIMESTAMP_NULL.
        RV_IS_NULL = ABAP_TRUE.
      ENDIF.
    WHEN IF_FDT_CONSTANTS=>GC_TP_DATETIME_OFFSET_UTC.
      IF ( IS_TIMEPOINT-DATE EQ MC_DATE_NULL ) AND
         ( IS_TIMEPOINT-TIME IS INITIAL OR IS_TIMEPOINT-TIME EQ MC_TIME_ZERO ) AND
         ( IS_TIMEPOINT-OFFSET_SIGN IS INITIAL OR IS_TIMEPOINT-OFFSET_SIGN EQ  MC_OFFSET_SIGN_PLUS ) AND
         ( IS_TIMEPOINT-OFFSET_TIME IS INITIAL OR IS_TIMEPOINT-OFFSET_TIME EQ  MC_OFFSET_MINUTES_INITIAL ).
        RV_IS_NULL = ABAP_TRUE.
      ENDIF.
    WHEN OTHERS.
      "unsupported timepoint type"
      MESSAGE E051(FDT_SERVICE) WITH IS_TIMEPOINT-TYPE INTO LS_MESSAGE-TEXT.
      MOVE-CORRESPONDING SYST TO LS_MESSAGE.
      LS_MESSAGE-SOURCE = CX_FDT=>GET_SOURCE_NAME( ).
      APPEND LS_MESSAGE TO LT_MESSAGE.
      RAISE EXCEPTION TYPE CX_FDT_CONVERSION
        EXPORTING
          MT_MESSAGE = LT_MESSAGE.
  ENDCASE.
ENDMETHOD.
```

#### _GET_TIMEZONE

![Method parameters](/images/ABAP/ABAP_JSON16.png)

```ABAP
method _GET_TIMEZONE.
* Prepare return value
  constants MC_EMPTY_TIMEZONE type TIMEZONE value '      '.  "#NO_TEXT"
  CLEAR rv_timezone.
* build the string
  DATA: lv_string TYPE c LENGTH 13.
  CONCATENATE 'MC_UTC_'
              iv_sign
              iv_minutes
         INTO lv_string.
* check validity
  CASE lv_string.
    WHEN 'MC_UTC_M000'.
      rv_timezone = mc_utc_m000 .
    WHEN 'MC_UTC_M060' .
      rv_timezone = mc_utc_m060 .
    WHEN 'MC_UTC_M120' .
      rv_timezone = mc_utc_m120 .
    WHEN 'MC_UTC_M180' .
      rv_timezone = mc_utc_m180 .
    WHEN 'MC_UTC_M210'.
      rv_timezone = mc_utc_m210 .
    WHEN 'MC_UTC_M240'.
      rv_timezone = mc_utc_m240 .
    WHEN 'MC_UTC_M270'.
      rv_timezone = mc_utc_m270 .
    WHEN 'MC_UTC_M300' .
      rv_timezone = mc_utc_m300 .
    WHEN 'MC_UTC_M360' .
      rv_timezone = mc_utc_m360 .
    WHEN 'MC_UTC_M420' .
      rv_timezone = mc_utc_m420 .
    WHEN 'MC_UTC_M480' .
      rv_timezone = mc_utc_m480 .
    WHEN 'MC_UTC_M510' .
      rv_timezone = mc_utc_m510 .
    WHEN 'MC_UTC_M540' .
      rv_timezone = mc_utc_m540 .
    WHEN 'MC_UTC_M600' .
      rv_timezone = mc_utc_m600 .
    WHEN 'MC_UTC_M660' .
      rv_timezone = mc_utc_m660 .
    WHEN 'MC_UTC_M720' .
      rv_timezone = mc_utc_m720 .
    WHEN 'MC_UTC_P000' .
      rv_timezone = mc_utc_p000 .
    WHEN 'MC_UTC_P060' .
      rv_timezone = mc_utc_p060 .
    WHEN 'MC_UTC_P120' .
      rv_timezone = mc_utc_p120 .
    WHEN 'MC_UTC_P180' .
      rv_timezone = mc_utc_p180 .
    WHEN 'MC_UTC_P210'.
      rv_timezone = mc_utc_p210 .
    WHEN 'MC_UTC_P240'.
      rv_timezone = mc_utc_p240 .
    WHEN 'MC_UTC_P270'.
      rv_timezone = mc_utc_p270 .
    WHEN 'MC_UTC_P300'.
      rv_timezone = mc_utc_p300 .
    WHEN 'MC_UTC_P330'.
      rv_timezone = mc_utc_p330 .
    WHEN 'MC_UTC_P345'.
      rv_timezone = mc_utc_p345 .
    WHEN 'MC_UTC_P360' .
      rv_timezone = mc_utc_p360 .
    WHEN 'MC_UTC_P390' .
      rv_timezone = mc_utc_p390 .
    WHEN 'MC_UTC_P420' .
      rv_timezone = mc_utc_p420 .
    WHEN 'MC_UTC_P480' .
      rv_timezone = mc_utc_p480 .
    WHEN 'MC_UTC_P540' .
      rv_timezone = mc_utc_p540 .
    WHEN 'MC_UTC_P570'.
      rv_timezone = mc_utc_p570 .
    WHEN 'MC_UTC_P600  '.
      rv_timezone = mc_utc_p600 .
    WHEN 'MC_UTC_P660' .
      rv_timezone = mc_utc_p660 .
    WHEN 'MC_UTC_P720' .
      rv_timezone = mc_utc_p720 .
    WHEN 'MC_UTC_P780' .
      rv_timezone = mc_utc_p780 .
    WHEN 'MC_UTC_P840' .
      rv_timezone = mc_utc_p840 .
    WHEN OTHERS.
      rv_timezone = mc_empty_timezone.
  ENDCASE.
endmethod.
```

#### BRF_STRUC_TO_JSON

![Method parameters](/images/ABAP/ABAP_JSON17.png)

```ABAP
METHOD BRF_STRUC_TO_JSON.
  DATA:
     LV_STR_NUMBER TYPE STRING,
     LV_STR_TIMEST TYPE STRING.
  FIELD-SYMBOLS:
    <A_NUMBER>       TYPE IF_FDT_TYPES=>ELEMENT_AMOUNT-NUMBER,
    <CURRENCY>       TYPE IF_FDT_TYPES=>ELEMENT_AMOUNT-CURRENCY,
    <Q_NUMBER>       TYPE IF_FDT_TYPES=>ELEMENT_QUANTITY-NUMBER,
    <UNIT>           TYPE IF_FDT_TYPES=>ELEMENT_QUANTITY-UNIT,
    <DATE>           TYPE DATS,
    <TIME>           TYPE TIMS,
    <TIMESTAMP>      TYPE DECV15,
    <OFFSET_TIME>    TYPE CHAR3,
    <OFFSET_SIGN>    TYPE TZNUTCSIGN,
    <TYPE>           TYPE NA_NATYP.
* Predetermined structures have an additional key value pair at the beginning: "$T": CHAR, 
* which describes the data type. The dollar symbol was chosen, because it is not allowed
* as first character for elements in ABAP structures and therefore $T can never 
* be a name fora structure element.
  CASE IV_TYPE.
    WHEN GC_ELEMENT_AMOUNT
      OR GC_FDT_AMOUNT.
      ASSIGN COMPONENT 'NUMBER'   OF STRUCTURE IA_DATA TO <A_NUMBER>.
      ASSIGN COMPONENT 'CURRENCY' OF STRUCTURE IA_DATA TO <CURRENCY>.
      LV_STR_NUMBER = <A_NUMBER>.
*        lv_str_number = data_to_json_internal( <a_number> ).
      CONCATENATE '{ "$T":"' IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_AMOUNT '", "NUMBER":'
                  LV_STR_NUMBER ', "CURRENCY":"' <CURRENCY> '"}' INTO RV_JSON.
    WHEN GC_ELEMENT_QUANTITY
      OR GC_FDT_QUANTITY.
      ASSIGN COMPONENT 'NUMBER' OF STRUCTURE IA_DATA TO <Q_NUMBER>.
      ASSIGN COMPONENT 'UNIT'   OF STRUCTURE IA_DATA TO <UNIT>.
      LV_STR_NUMBER = <Q_NUMBER>.
*        lv_str_number = data_to_json_internal( <q_number> ).
      CONCATENATE '{ "$T":"' IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_QUANTITY 
                  '", "NUMBER":' LV_STR_NUMBER 
                  ', "UNIT":"' <UNIT> '"}' INTO RV_JSON.
    WHEN GC_ELEMENT_TIMEPOINT
      OR GC_FDT_TIMEPOINT.
      ASSIGN COMPONENT 'TYPE' OF STRUCTURE IA_DATA TO <TYPE>.
      CASE <TYPE>.
        WHEN IF_FDT_CONSTANTS=>GC_TP_DATE.
          ASSIGN COMPONENT 'DATE'        OF STRUCTURE IA_DATA TO <DATE>.
          CONCATENATE '{"$T":"' IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_TIMEPOINT '",'
                      '"DATE":"' <DATE> '",'
                      '"TYPE":"' <TYPE>  '"}' INTO RV_JSON.
        WHEN IF_FDT_CONSTANTS=>GC_TP_TIME.
          ASSIGN COMPONENT 'TIME'        OF STRUCTURE IA_DATA TO <TIME>.
          CONCATENATE '{"$T":"' IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_TIMEPOINT '",'
                      '"TIME":"' <TIME> '",'
                      '"TYPE":"' <TYPE>  '"}' INTO RV_JSON.
        WHEN IF_FDT_CONSTANTS=>GC_TP_DATETIME.
          ASSIGN COMPONENT 'DATE'        OF STRUCTURE IA_DATA TO <DATE>.
          ASSIGN COMPONENT 'TIME'        OF STRUCTURE IA_DATA TO <TIME>.
          CONCATENATE '{"$T":"' IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_TIMEPOINT '",'
                      '"DATE":"' <DATE> '",'
                      '"TIME":"' <TIME> '",'
                      '"TYPE":"' <TYPE>  '"}' INTO RV_JSON.
        WHEN IF_FDT_CONSTANTS=>GC_TP_TIMESTAMP_UTC.
          ASSIGN COMPONENT 'TIMESTAMP'   OF STRUCTURE IA_DATA TO <TIMESTAMP>.
          LV_STR_TIMEST = <TIMESTAMP>.
          IF <TIMESTAMP> LT 0. "get sign to the front!"
            SHIFT LV_STR_TIMEST CIRCULAR RIGHT.
          ENDIF.
*            lv_str_timest = data_to_json_internal( <timestamp> ).
          CONCATENATE '{"$T":"' IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_TIMEPOINT '",'
                      '"TIMESTAMP":' LV_STR_TIMEST ','
                      '"TYPE":"' <TYPE>  '"}' INTO RV_JSON.
        WHEN IF_FDT_CONSTANTS=>GC_TP_DATETIME_OFFSET_UTC.
          ASSIGN COMPONENT 'DATE'        OF STRUCTURE IA_DATA TO <DATE>.
          ASSIGN COMPONENT 'TIME'        OF STRUCTURE IA_DATA TO <TIME>.
          ASSIGN COMPONENT 'OFFSET_TIME' OF STRUCTURE IA_DATA TO <OFFSET_TIME>.
          ASSIGN COMPONENT 'OFFSET_SIGN' OF STRUCTURE IA_DATA TO <OFFSET_SIGN>.
          CONCATENATE '{"$T":"' IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_TIMEPOINT '",'
                      '"DATE":"' <DATE> '",'
                      '"TIME":"' <TIME> '",'
                      '"OFFSET_TIME":"' <OFFSET_TIME> '",'
                      '"OFFSET_SIGN":"' <OFFSET_SIGN> '",'
                      '"TYPE":"' <TYPE>  '"}' INTO RV_JSON.
        WHEN OTHERS.
      ENDCASE.
    WHEN OTHERS.
      CLEAR RV_JSON.
  ENDCASE.
ENDMETHOD.
```

#### CONVERT_TOKEN

![Method parameters](/images/ABAP/ABAP_JSON18.png)

```ABAP
METHOD CONVERT_TOKEN.
  DATA: LO_DESCR      TYPE REF TO CL_ABAP_TYPEDESCR.
  " Check target type if conversion is needed "
  LO_DESCR = CL_ABAP_TYPEDESCR=>DESCRIBE_BY_DATA( IA_DATA ).
  CASE LO_DESCR->ABSOLUTE_NAME.
    WHEN GC_ABAP_BOOL
      OR GC_FDT_BOOLEAN
      OR GC_ELEMENT_BOOLEAN.
      IF CV_TOKEN EQ 'true'.
        CV_TOKEN = ABAP_TRUE.
      ELSEIF CV_TOKEN EQ 'false'.
        CV_TOKEN = ABAP_FALSE.
      ENDIF.
    WHEN OTHERS.
*        cv_token = cv_token.
      " replace escape characters "
      REPLACE ALL OCCURRENCES OF '\n' IN CV_TOKEN WITH CL_ABAP_CHAR_UTILITIES=>NEWLINE.
      REPLACE ALL OCCURRENCES OF '\t' IN CV_TOKEN WITH CL_ABAP_CHAR_UTILITIES=>HORIZONTAL_TAB.
      REPLACE ALL OCCURRENCES OF '\f' IN CV_TOKEN WITH CL_ABAP_CHAR_UTILITIES=>FORM_FEED.
      REPLACE ALL OCCURRENCES OF '\v' IN CV_TOKEN WITH CL_ABAP_CHAR_UTILITIES=>VERTICAL_TAB.
      REPLACE ALL OCCURRENCES OF '\b' IN CV_TOKEN WITH CL_ABAP_CHAR_UTILITIES=>BACKSPACE.
      REPLACE ALL OCCURRENCES OF '\r' IN CV_TOKEN WITH CL_ABAP_CHAR_UTILITIES=>CR_LF(1).
      REPLACE ALL OCCURRENCES OF '\"' IN CV_TOKEN WITH '"'.
      REPLACE ALL OCCURRENCES OF '\/' IN CV_TOKEN WITH '/'.
      REPLACE ALL OCCURRENCES OF '\\' IN CV_TOKEN WITH '\'.
  ENDCASE.
ENDMETHOD.
```

#### DATA_TO_JSON_INTERNAL

![Method parameters](/images/ABAP/ABAP_JSON19.png)

```ABAP
METHOD DATA_TO_JSON_INTERNAL.
  DATA:
     LO_DESCR       TYPE REF TO CL_ABAP_TYPEDESCR,
     LO_STRUC_DESCR TYPE REF TO CL_ABAP_STRUCTDESCR,
     LO_TABLE_DESCR TYPE REF TO CL_ABAP_TABLEDESCR,
     LT_COMP        TYPE CL_ABAP_STRUCTDESCR=>INCLUDED_VIEW,
     LV_FIRST       TYPE ABAP_BOOL VALUE ABAP_TRUE,
     LV_JSON        TYPE STRING.
  FIELD-SYMBOLS:
    <LA_DATA> TYPE ANY,
    <LT_DATA> TYPE ANY TABLE,
    <LS_COMP> TYPE LINE OF CL_ABAP_STRUCTDESCR=>INCLUDED_VIEW.
  IF IO_DESCR IS SUPPLIED.
    LO_DESCR = IO_DESCR.
  ELSE.
    LO_DESCR = CL_ABAP_TYPEDESCR=>DESCRIBE_BY_DATA( IA_DATA ).
  ENDIF.
  CASE LO_DESCR->TYPE_KIND.
    WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_STRUCT1
      OR CL_ABAP_TYPEDESCR=>TYPEKIND_STRUCT2.
      "check brf + structures first"
      RV_JSON = BRF_STRUC_TO_JSON(
        IA_DATA = IA_DATA
        IV_TYPE = LO_DESCR->ABSOLUTE_NAME ).
      IF RV_JSON IS INITIAL. "do standard evaluation
        "JSON starts with {"elem1":value1, ...
        RV_JSON = '{'.
        LO_STRUC_DESCR ?= LO_DESCR.
        "use following method instead of method get_components to get 
        " includes directly included!
        LT_COMP = LO_STRUC_DESCR->GET_INCLUDED_VIEW( ).
        LOOP AT LT_COMP ASSIGNING <LS_COMP>.
          IF LV_FIRST EQ ABAP_FALSE.
            CONCATENATE RV_JSON  ',' INTO RV_JSON.
          ENDIF.
          ASSIGN COMPONENT <LS_COMP>-NAME OF STRUCTURE IA_DATA TO <LA_DATA>.
          LV_JSON = DATA_TO_JSON_INTERNAL( IA_DATA = <LA_DATA> IO_DESCR = <LS_COMP>-TYPE ).
          CONCATENATE RV_JSON '"' <LS_COMP>-NAME '":' LV_JSON INTO RV_JSON.
          LV_FIRST = ABAP_FALSE.
        ENDLOOP.
        CONCATENATE RV_JSON '}' INTO RV_JSON.
      ENDIF.
    WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_TABLE.
      "JSON starts with [value1, ..."
      RV_JSON = '['.
      ASSIGN IA_DATA TO <LT_DATA>.
      LO_TABLE_DESCR ?= LO_DESCR.
      LO_DESCR = LO_TABLE_DESCR->GET_TABLE_LINE_TYPE( ).
      LOOP AT <LT_DATA> ASSIGNING <LA_DATA>.
        IF LV_FIRST EQ ABAP_FALSE.
          CONCATENATE RV_JSON  ',' INTO RV_JSON.
        ENDIF.
        LV_JSON = DATA_TO_JSON_INTERNAL( IA_DATA = <LA_DATA> IO_DESCR = LO_DESCR ).
        CONCATENATE RV_JSON LV_JSON INTO RV_JSON.
        LV_FIRST = ABAP_FALSE.
      ENDLOOP.
      CONCATENATE RV_JSON ']' INTO RV_JSON.
    WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_DREF.
      ASSIGN IA_DATA->* TO <LA_DATA>.
      IF SY-SUBRC = 0.
        RV_JSON = '{'.          "needed?"
        LV_JSON = DATA_TO_JSON_INTERNAL( IA_DATA = <LA_DATA> ).
        CONCATENATE RV_JSON LV_JSON '}' INTO RV_JSON.
      ELSE.
        RV_JSON = '{}'. "Will produce empty JS object"
      ENDIF.
    WHEN OTHERS.
      "convert single field value"
      CASE LO_DESCR->ABSOLUTE_NAME.
        WHEN GC_ELEMENT_BOOLEAN
          OR GC_FDT_BOOLEAN
          OR GC_ABAP_BOOL.
          IF IA_DATA EQ ABAP_TRUE.
            RV_JSON = 'true'.                               "#EC NOTEXT
          ELSE.
            RV_JSON = 'false'.                              "#EC NOTEXT
          ENDIF.
        WHEN OTHERS.
          CASE LO_DESCR->TYPE_KIND.
            WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_CLIKE
              OR CL_ABAP_TYPEDESCR=>TYPEKIND_STRING
              OR CL_ABAP_TYPEDESCR=>TYPEKIND_CHAR.
              RV_JSON = IA_DATA.
" Escape special characters "
*   REPLACE ALL OCCURRENCES OF '\'                                    IN RV_JSON WITH '\\'.
*   REPLACE ALL OCCURRENCES OF '/'                                    IN RV_JSON WITH '\/'.
*   REPLACE ALL OCCURRENCES OF '"'                                    IN RV_JSON WITH '\"'.
*   REPLACE ALL OCCURRENCES OF CL_ABAP_CHAR_UTILITIES=>CR_LF(1)       IN RV_JSON WITH '\r'.
*   REPLACE ALL OCCURRENCES OF CL_ABAP_CHAR_UTILITIES=>NEWLINE        IN RV_JSON WITH '\n'.
*   REPLACE ALL OCCURRENCES OF CL_ABAP_CHAR_UTILITIES=>HORIZONTAL_TAB IN RV_JSON WITH '\t'.
*   REPLACE ALL OCCURRENCES OF CL_ABAP_CHAR_UTILITIES=>FORM_FEED      IN RV_JSON WITH '\f'.
*   REPLACE ALL OCCURRENCES OF CL_ABAP_CHAR_UTILITIES=>VERTICAL_TAB   IN RV_JSON WITH '\v'.
*   REPLACE ALL OCCURRENCES OF CL_ABAP_CHAR_UTILITIES=>BACKSPACE      IN RV_JSON WITH '\b'. "
              CONCATENATE '"' RV_JSON '"' INTO RV_JSON.
            WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_NUM
              OR CL_ABAP_TYPEDESCR=>TYPEKIND_DATE
              OR CL_ABAP_TYPEDESCR=>TYPEKIND_TIME.
              CONCATENATE '"' IA_DATA '"' INTO RV_JSON.
            WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_FLOAT OR 'a' OR 'e' OR '/'.
              RV_JSON = IA_DATA.
            WHEN 'I' OR 'P' OR '8'.
              RV_JSON = IA_DATA.
              IF IA_DATA LT 0. "get sign to the front!"
                SHIFT RV_JSON CIRCULAR RIGHT.
              ENDIF.
            WHEN OTHERS.
              RV_JSON = IA_DATA.
          ENDCASE.
      ENDCASE.
  ENDCASE.
ENDMETHOD.
```

#### GET_STRING

![Method parameters](/images/ABAP/ABAP_JSON20.png)

```ABAP
METHOD GET_STRING.
  DATA:
    LV_ESCAPE   TYPE I,
    LV_CHAR     TYPE STRING, "instead of 'C' to keep spaces"
    LV_IS_STRING TYPE ABAP_BOOL VALUE ABAP_TRUE.
  WHILE STRLEN( CV_JSON ) > 0
    AND LV_IS_STRING EQ ABAP_TRUE.
    LV_CHAR = CV_JSON(1).
    SHIFT CV_JSON BY 1 PLACES.
    CASE LV_CHAR.
      WHEN '\'.  
        LV_ESCAPE = LV_ESCAPE + 1.
        CONCATENATE EV_STRING LV_CHAR INTO EV_STRING.
      WHEN '"'.
        LV_ESCAPE = LV_ESCAPE MOD 2.
        IF LV_ESCAPE EQ 0.         "end of string?"
          LV_IS_STRING = ABAP_FALSE.
        ELSE.
          CONCATENATE EV_STRING LV_CHAR INTO EV_STRING.
        ENDIF.
        CLEAR LV_ESCAPE.
      WHEN OTHERS. "all other chars could be collected without any check!"
        CONCATENATE EV_STRING LV_CHAR INTO EV_STRING.
        CLEAR LV_ESCAPE.
    ENDCASE.
  ENDWHILE.
ENDMETHOD.
```

#### JSON_TO_BRF_STRUC

![Method parameters](/images/ABAP/ABAP_JSON21.png)

```ABAP
METHOD JSON_TO_BRF_STRUC.
  DATA:
    LV_TOKEN      TYPE STRING,
    LS_AMOUNT     TYPE FDT_S_AMOUNT,
    LS_QUANTITY   TYPE FDT_S_QUANTITY,
    LS_TIMEPOINT  TYPE fdt_s_timepoint,
    LO_DESCR      TYPE REF TO CL_ABAP_TYPEDESCR,
    LX_ROOT       TYPE REF TO CX_ROOT.
  FIELD-SYMBOLS: <LA_ANY> TYPE ANY.
  "JSON example:' "A", "AMOUNT":1234,"CURRENCY":"EUR"} ...' "
  LO_DESCR = CL_ABAP_TYPEDESCR=>DESCRIBE_BY_DATA( CA_DATA ).

  "1. get the special type"
  SHIFT CV_JSON UP TO '"'.
  SHIFT CV_JSON BY 1 PLACES. " eleminate quotes  " 
  GET_STRING(
    IMPORTING
      EV_STRING = LV_TOKEN
    CHANGING
      CV_JSON = CV_JSON ).
  SHIFT CV_JSON UP TO ','.
  SHIFT CV_JSON BY 1 PLACES. " eleminate comma ',' "
  CASE LV_TOKEN.
    WHEN IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_AMOUNT.
      ASSIGN LS_AMOUNT TO <LA_ANY>.
    WHEN IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_QUANTITY.
      ASSIGN LS_QUANTITY TO <LA_ANY>.
    WHEN IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_TIMEPOINT.
      ASSIGN LS_TIMEPOINT TO <LA_ANY>.
    WHEN OTHERS.
      RETURN. "?"
  ENDCASE.
  "2. get values for special type"
  IF <LA_ANY> IS ASSIGNED.
    JSON_TO_DATA_INTERNAL(
      CHANGING
        CV_JSON = CV_JSON
        CA_DATA = <LA_ANY> ).
    TRY.
        CASE LV_TOKEN.
          WHEN IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_AMOUNT.
            "Check target type"
            CASE LO_DESCR->ABSOLUTE_NAME.
              WHEN GC_ELEMENT_AMOUNT
                OR GC_FDT_AMOUNT.
                CA_DATA = LS_AMOUNT.   "no conversion needed"
              WHEN GC_ELEMENT_NUMBER.
                CA_DATA = LS_AMOUNT-NUMBER.
              WHEN GC_ELEMENT_TEXT.
                CA_DATA = TO_STING( LS_AMOUNT ).
              WHEN OTHERS.
                CASE LO_DESCR->TYPE_KIND.
                  WHEN  'F' OR '/' OR 'a' OR 'e' OR 'P' OR 'I' OR '8'.
                    CA_DATA = LS_AMOUNT-NUMBER.
                  WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_CLIKE
                    OR CL_ABAP_TYPEDESCR=>TYPEKIND_CHAR
                    OR CL_ABAP_TYPEDESCR=>TYPEKIND_STRING.
                    CA_DATA = TO_STING( LS_AMOUNT ).
                  WHEN OTHERS.
                    CLEAR CA_DATA. "no conversion possible"
                ENDCASE.
            ENDCASE.
          WHEN IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_QUANTITY.
            CASE LO_DESCR->ABSOLUTE_NAME.
              WHEN GC_ELEMENT_QUANTITY
                OR GC_FDT_QUANTITY.
                CA_DATA = LS_QUANTITY.    "no conversion needed"
              WHEN GC_ELEMENT_NUMBER.
                CA_DATA = LS_QUANTITY-NUMBER.
              WHEN GC_ELEMENT_TEXT.
                CA_DATA = QUANTITY_TO_STING( LS_QUANTITY ).
              WHEN OTHERS.
                CASE LO_DESCR->TYPE_KIND.
                  WHEN 'F' OR '/' OR 'a' OR 'e' OR 'P' OR 'I' OR '8'.
                    CA_DATA = LS_QUANTITY-NUMBER.
                  WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_CLIKE
                    OR CL_ABAP_TYPEDESCR=>TYPEKIND_CHAR
                    OR CL_ABAP_TYPEDESCR=>TYPEKIND_STRING.
                    CA_DATA = QUANTITY_TO_STING( LS_QUANTITY ).
                  WHEN OTHERS.
                    CLEAR CA_DATA. "no conversion possible"
                ENDCASE.
            ENDCASE.
          WHEN IF_FDT_CONSTANTS=>GC_ELEMENT_TYPE_TIMEPOINT.
            CASE LO_DESCR->ABSOLUTE_NAME.
              WHEN GC_ELEMENT_TIMEPOINT
                OR GC_FDT_TIMEPOINT.
                CA_DATA = LS_TIMEPOINT.             "no conversion needed"
              WHEN GC_ELEMENT_NUMBER.
                CA_DATA = LS_TIMEPOINT-TIMESTAMP.
              WHEN GC_ELEMENT_TEXT.
                CA_DATA = CONVERT_TIMEPOINT_TO_TEXT( LS_TIMEPOINT ).
              WHEN OTHERS.
                CASE LO_DESCR->TYPE_KIND.
                  WHEN 'F' OR '/' OR 'a' OR 'e' OR 'P' OR 'I' OR '8'.
                    CA_DATA = LS_TIMEPOINT-TIMESTAMP.
                  WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_CLIKE
                    OR CL_ABAP_TYPEDESCR=>TYPEKIND_CHAR
                    OR CL_ABAP_TYPEDESCR=>TYPEKIND_STRING.
                    CA_DATA = CONVERT_TIMEPOINT_TO_TEXT( LS_TIMEPOINT ).
                  WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_DATE.
                    CA_DATA = LS_TIMEPOINT-DATE.
                  WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_TIME.
                    CA_DATA = LS_TIMEPOINT-TIME.
                  WHEN OTHERS.
                    CLEAR CA_DATA. "no conversion possible"
                ENDCASE.
            ENDCASE.
        ENDCASE.
      CATCH CX_ROOT INTO LX_ROOT.
        CLEAR CA_DATA.
    ENDTRY.
    "to end the level - as this sign is already stripped in method before"
    CONCATENATE '}' CV_JSON INTO CV_JSON RESPECTING BLANKS.
  ENDIF.
ENDMETHOD.
```

#### JSON_TO_DATA_INTERNAL

![Method parameters](/images/ABAP/ABAP_JSON22.png)

```ABAP
METHOD JSON_TO_DATA_INTERNAL.
  "JSON               ABAP                     "
  "{...} object  -->  field, structure, ...    "
  "[...] array   -->  table                    "
  DATA:
    LV_CHAR      TYPE C,
    LV_TOKEN     TYPE STRING,
    LV_DUMMY     TYPE STRING,   "is used in case of invalid fieldname"
    LT_DUMMY     LIKE STANDARD TABLE OF LV_DUMMY, "is used in case of invalid table"
    LV_IS_FILLED TYPE ABAP_BOOL,
    LR_LINE      TYPE REF TO DATA,
    LR_TD        TYPE REF TO CL_ABAP_TYPEDESCR,
    LR_TTD       TYPE REF TO CL_ABAP_TABLEDESCR,
    LX_ROOT      TYPE REF TO CX_ROOT.
  FIELD-SYMBOLS:
    <LA_ANY>  TYPE ANY,
    <LA_LINE> TYPE ANY,
    <LT_ANY>  TYPE ANY TABLE,
    <LT_SRTD> TYPE SORTED TABLE,
    <LT_HASH> TYPE HASHED TABLE,
    <LT_STND> TYPE STANDARD TABLE.

  LR_TD = CL_ABAP_TYPEDESCR=>DESCRIBE_BY_DATA( CA_DATA ).
  CASE LR_TD->TYPE_KIND.
    WHEN CL_ABAP_TYPEDESCR=>TYPEKIND_DREF.
      ASSIGN CA_DATA->* TO <LA_ANY>.
      IF SY-SUBRC <> 0. ASSIGN LV_DUMMY TO <LA_ANY>. ENDIF.
    WHEN OTHERS.
      ASSIGN CA_DATA TO <LA_ANY>.
  ENDCASE.
  WHILE STRLEN( CV_JSON ) > 0.
    LV_CHAR = CV_JSON(1).
    SHIFT CV_JSON BY 1 PLACES.
    CASE LV_CHAR.
      WHEN '{'.   "object starts --> next level"
        JSON_TO_DATA_INTERNAL(
          EXPORTING
            IV_IS_TABLINE = ABAP_FALSE
          IMPORTING
            EV_IS_FILLED  = EV_IS_FILLED
          CHANGING
            CV_JSON     = CV_JSON
            CA_DATA     = <LA_ANY> ).
      WHEN '['.   "array starts --> next level"
        LR_TD = CL_ABAP_TYPEDESCR=>DESCRIBE_BY_DATA( <LA_ANY> ).
        IF LR_TD->KIND EQ CL_ABAP_TYPEDESCR=>KIND_TABLE.
          ASSIGN <LA_ANY> TO <LT_ANY>.
        ELSE.     "Type mismatch - to prevent short dump"
          ASSIGN LT_DUMMY TO <LT_ANY>.
        ENDIF.
        CREATE DATA LR_LINE LIKE LINE OF <LT_ANY>.
        ASSIGN LR_LINE->* TO <LA_LINE>.
        CLEAR LV_IS_FILLED.
        JSON_TO_DATA_INTERNAL(
          EXPORTING
            IV_IS_TABLINE = ABAP_TRUE
          IMPORTING
            EV_IS_FILLED  = LV_IS_FILLED
          CHANGING
            CV_JSON     = CV_JSON
            CA_DATA     = <LA_LINE> ).
        IF <LA_LINE> IS NOT INITIAL
        OR LV_IS_FILLED EQ ABAP_TRUE.
          EV_IS_FILLED = ABAP_TRUE.
          LR_TD = CL_ABAP_TYPEDESCR=>DESCRIBE_BY_DATA( <LT_ANY> ).
          LR_TTD ?= LR_TD.
          IF LR_TTD->TABLE_KIND EQ CL_ABAP_TABLEDESCR=>TABLEKIND_SORTED.
            " sorted table "
            ASSIGN <LT_ANY> TO <LT_SRTD>.
            INSERT <LA_LINE> INTO TABLE <LT_SRTD>.
          ELSEIF LR_TTD->TABLE_KIND = CL_ABAP_TABLEDESCR=>TABLEKIND_HASHED.
            " hashed table "
            ASSIGN <LT_ANY> TO <LT_HASH>.
            INSERT <LA_LINE> INTO TABLE <LT_HASH>.
          ELSE.
            " standard table "
            ASSIGN <LT_ANY> TO <LT_STND>.
            APPEND <LA_LINE> TO <LT_STND>.
          ENDIF.
        ENDIF.
      WHEN ']'.    "Array ends --> previous level"
        IF LV_TOKEN IS NOT INITIAL.
          CONVERT_TOKEN(
            EXPORTING
              IA_DATA      = <LA_ANY>
            CHANGING
              CV_TOKEN     = LV_TOKEN ).
          TRY.
              <LA_ANY> = LV_TOKEN.
              EV_IS_FILLED = ABAP_TRUE.
            CATCH CX_ROOT INTO LX_ROOT.          "#EC NO_HANDLER"
          ENDTRY.
        ENDIF.
        RETURN.
      WHEN '}'.   "Object ends --> previous level"
        IF LV_TOKEN IS NOT INITIAL.
          CONVERT_TOKEN(
            EXPORTING
              IA_DATA      = <LA_ANY>
            CHANGING
              CV_TOKEN     = LV_TOKEN ).
          TRY.
              <LA_ANY> = LV_TOKEN.
              EV_IS_FILLED = ABAP_TRUE.
            CATCH CX_ROOT INTO LX_ROOT.        "#EC NO_HANDLER"
          ENDTRY.
        ENDIF.
        RETURN.
      WHEN ':'.   "key ends, value starts"
        TRANSLATE LV_TOKEN TO UPPER CASE.
        SHIFT LV_TOKEN LEFT DELETING LEADING SPACE.
        IF LV_TOKEN IS NOT INITIAL.
          IF LV_TOKEN EQ '$ROOT'.              "Do nothing - already assigned!"
          ELSEIF LV_TOKEN EQ '$T'.             "ignore the value! do special things"
            JSON_TO_BRF_STRUC(
              CHANGING
                CA_DATA    = <LA_ANY>
                CV_JSON    = CV_JSON ).
            EV_IS_FILLED = ABAP_TRUE.
          ELSE.
            ASSIGN COMPONENT LV_TOKEN OF STRUCTURE CA_DATA TO <LA_ANY>.
            IF SY-SUBRC NE 0.                  "Field not found?"
              ASSIGN LV_DUMMY TO <LA_ANY>.     "ignore the value!"
            ENDIF.
          ENDIF.
        ENDIF.
        CLEAR LV_TOKEN.
      WHEN ','.   "next value -- same level"
        IF LV_TOKEN IS NOT INITIAL.
          CONVERT_TOKEN(
            EXPORTING
              IA_DATA      = <LA_ANY>
            CHANGING
              CV_TOKEN     = LV_TOKEN ).
          TRY.
              <LA_ANY> = LV_TOKEN.
              EV_IS_FILLED = ABAP_TRUE.
            CATCH CX_ROOT INTO LX_ROOT.        "#EC NO_HANDLER"
          ENDTRY.
        ENDIF.
        CLEAR LV_TOKEN.
        IF IV_IS_TABLINE EQ ABAP_TRUE.  "to trigger next table line"
          CONCATENATE '[' CV_JSON INTO CV_JSON.
          RETURN.
        ENDIF.
      WHEN '"'.    "string starts"
        GET_STRING(
          IMPORTING
            EV_STRING = LV_TOKEN
          CHANGING
            CV_JSON = CV_JSON ).
      WHEN OTHERS. "ie. numbers, ..."
        CONCATENATE LV_TOKEN LV_CHAR INTO LV_TOKEN.
    ENDCASE.
  ENDWHILE.
ENDMETHOD.
```

