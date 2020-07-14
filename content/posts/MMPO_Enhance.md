---
title: "PO 增强"
date: 2019-09-11
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---





创建采购订单(Purchase Order)时可以通过增强做一些控制。



**BADI:ME_PROCESS_PO_CUST**

**process_header：** 对PO抬头数据进行检查

**process_item：** 对PO行项目做一些输入数据检查(限价检查)

**post：** 点击保存时进行数据更新(底价更新)

#### METHOD： IF_EX_ME_PROCESS_PO_CUST~PROCESS_ITEM.

```JS
METHOD IF_EX_ME_PROCESS_PO_CUST~PROCESS_ITEM.

    INCLUDE MM_MESSAGES_MAC.
    DATA : LW_ITEM TYPE MEPOITEM.
    DATA : LT_KONV TYPE TABLE OF KOMV .
    DATA : LW_KONV TYPE KOMV .
    DATA : LV_KNUMH TYPE A118-KNUMH .
    DATA : LT_KONP TYPE TABLE OF KONP .
    DATA : LW_KONP TYPE KONP .
    DATA : LV_MARKS(1) TYPE C .
    DATA:LT_KONM TYPE TABLE OF  KONM,
         LW_KONM TYPE  KONM.

    DATA : LO_HEADER TYPE REF TO IF_PURCHASE_ORDER_MM .
    DATA : LW_HEADER TYPE MEPOHEADER .

    CLEAR:LT_KONM[],LW_KONM.

    IF  SY-TCODE = 'ME21N' OR SY-TCODE = 'ME22N'  OR SY-TCODE = 'ME29N'
      OR SY-TCODE = 'ME23N'  .
      LO_HEADER = IM_ITEM->GET_HEADER( ).
      LW_HEADER = LO_HEADER->GET_DATA( ).

      IF LW_HEADER-BSART = '913' OR  LW_HEADER-BSART = '914'
        OR  LW_HEADER-BSART = '915' OR  LW_HEADER-BSART = '916' .
      ELSE .

        LW_ITEM = IM_ITEM->GET_DATA( ).

        CHECK LW_ITEM-UMSON = '' ."免费货物不检查
        CHECK LW_ITEM-LOEKZ = '' ."删除货物不检查
        CHECK LW_ITEM-MATNR <> '' ."无物料号不检查

        CALL METHOD IM_ITEM->GET_CONDITIONS
          IMPORTING
            EX_CONDITIONS = LT_KONV[].

        CLEAR LV_MARKS .
        CLEAR LW_KONV .
        READ TABLE LT_KONV INTO LW_KONV
                WITH KEY KPOSN = LW_ITEM-EBELP
                         KSCHL = 'PB00' .
        IF SY-SUBRC <>  0 .
          CLEAR LW_KONV .
          READ TABLE LT_KONV INTO LW_KONV
                  WITH KEY KPOSN = LW_ITEM-EBELP
                           KSCHL = 'PBXX' .
          IF SY-SUBRC <> 0 .
            LV_MARKS = 'X' .
          ENDIF .
        ENDIF .

        CHECK LV_MARKS = '' .


        SELECT SINGLE KNUMH
            INTO LV_KNUMH
            FROM A901
            WHERE KSCHL = 'PB00'
              AND MATNR = LW_ITEM-MATNR
              AND ZONWA = LW_HEADER-WAERS
              AND DATBI >= SY-DATUM
              AND DATAB <= SY-DATUM .
        IF SY-SUBRC = 0 .
          CLEAR LT_KONP[] .
          SELECT *
            INTO TABLE LT_KONP
            FROM KONP
            WHERE KNUMH = LV_KNUMH
              AND KONWA = LW_HEADER-WAERS .
          IF SY-SUBRC = 0 .
            SELECT * INTO CORRESPONDING FIELDS OF TABLE LT_KONM
            FROM KONM
            FOR ALL ENTRIES IN LT_KONP WHERE KNUMH = LT_KONP-KNUMH AND KOPOS = LT_KONP-KOPOS." AND KSTBM <= LW_ITEM-MENGE.
            CLEAR LW_KONP .
            READ TABLE LT_KONP INTO LW_KONP INDEX 1 .

            SORT LT_KONM BY KNUMH KOPOS KSTBM DESCENDING."按数量倒序
            READ TABLE LT_KONM INTO LW_KONM INDEX 1."取最近最大的值对应的价格

            IF LT_KONM[] IS NOT INITIAL."如果存在，将阶梯价复制过来
              LOOP AT LT_KONM INTO LW_KONM WHERE KSTBM >= LW_ITEM-MENGE..
                LW_KONP-KBETR = LW_KONM-KBETR.
              ENDLOOP.
            ENDIF.
            IF LW_HEADER-WAERS <> LW_KONV-WAERS .
              CALL FUNCTION 'CONVERT_TO_FOREIGN_CURRENCY'
                EXPORTING
                  DATE             = SY-DATUM
                  FOREIGN_CURRENCY = LW_HEADER-WAERS
                  LOCAL_AMOUNT     = LW_KONV-KBETR
                  LOCAL_CURRENCY   = LW_KONV-WAERS
                IMPORTING
                  FOREIGN_AMOUNT   = LW_KONV-KBETR
                EXCEPTIONS
                  NO_RATE_FOUND    = 1
                  OVERFLOW         = 2
                  NO_FACTORS_FOUND = 3
                  NO_SPREAD_FOUND  = 4
                  DERIVED_2_TIMES  = 5
                  OTHERS           = 6.
            ENDIF .

            IF LW_KONP-KMEIN <> LW_KONV-KMEIN ."数量单位转换
              DATA : LV_MENGE TYPE EKPO-MENGE .
              LV_MENGE = LW_KONP-KPEIN .
              CALL FUNCTION 'MD_CONVERT_MATERIAL_UNIT'
                EXPORTING
                  I_MATNR              = LW_ITEM-MATNR
                  I_IN_ME              = LW_KONP-KMEIN
                  I_OUT_ME             = LW_KONV-KMEIN
                  I_MENGE              = LV_MENGE
                IMPORTING
                  E_MENGE              = LV_MENGE
                EXCEPTIONS
                  ERROR_IN_APPLICATION = 1
                  ERROR                = 2
                  OTHERS               = 3.
              IF LV_MENGE <> 0 .
                LW_KONP-KBETR = LW_KONP-KBETR  / LV_MENGE * LW_KONV-KPEIN .
              ELSE .
                MMPUR_MESSAGE 'E' 'ZMM' '001' '行项目' LW_ITEM-EBELP  '限价转换单位太小，程序无法判断' ''.
              ENDIF .
            ELSE .
              LW_KONP-KBETR = LW_KONP-KBETR  / LW_KONP-KPEIN * LW_KONV-KPEIN .
            ENDIF .


            IF LW_KONV-KBETR > LW_KONP-KBETR .
              MMPUR_MESSAGE 'E' 'ZMM' '001' '行项目' LW_ITEM-EBELP  '采购价格超出物料最高限价！' ''.
            ENDIF .
          ELSE .
            CALL FUNCTION 'CONVERSION_EXIT_MATN1_OUTPUT'
              EXPORTING
                INPUT  = LW_ITEM-MATNR
              IMPORTING
                OUTPUT = LW_ITEM-MATNR.
            MMPUR_MESSAGE 'E' 'ZMM' '001' '行项目' LW_ITEM-EBELP  '的最高限价货币记录为空！' ''.
          ENDIF .
        ELSE ."

          SELECT SINGLE KNUMH
              INTO LV_KNUMH
              FROM A901
              WHERE KSCHL = 'PB00'
                AND MATNR = LW_ITEM-MATNR
                AND DATBI >= SY-DATUM
                AND DATAB <= SY-DATUM .
          IF SY-SUBRC = 0 .
            CALL FUNCTION 'CONVERSION_EXIT_MATN1_OUTPUT'
              EXPORTING
                INPUT  = LW_ITEM-MATNR
              IMPORTING
                OUTPUT = LW_ITEM-MATNR.
            MMPUR_MESSAGE 'E' 'ZMM' '001' '行项目' LW_ITEM-EBELP  '的最高限价货币记录为空！' ''.
          ELSE .
            CALL FUNCTION 'CONVERSION_EXIT_MATN1_OUTPUT'
              EXPORTING
                INPUT  = LW_ITEM-MATNR
              IMPORTING
                OUTPUT = LW_ITEM-MATNR.
            MMPUR_MESSAGE 'W' 'ZMM' '001' '行项目' LW_ITEM-EBELP '的最高限价记录为空，将自动维护' ''.
          ENDIF .
        ENDIF .

      ENDIF .
    ENDIF .

  ENDMETHOD.
```

#### METHOD： IF_EX_ME_PROCESS_PO_CUST~POST

```JS
METHOD IF_EX_ME_PROCESS_PO_CUST~POST.
    TYPES : BEGIN OF LS_EKPO ,"物料对应
              MATNR    TYPE EKPO-MATNR,
              MENGE    TYPE EKPO-MENGE,
              KBETR    TYPE KONV-KBETR, "P DECIMALS 6 ,
              WAERS    TYPE KONV-WAERS,
              KMEIN    TYPE KONV-KMEIN,
              KBET2    TYPE KONV-KBETR,
              KPEIN    TYPE KONV-KPEIN,
              MARKX(1) TYPE C,
            END OF LS_EKPO .

    DATA : LW_EKPO TYPE LS_EKPO .
    DATA : LT_EKPO TYPE TABLE OF LS_EKPO .
    DATA : LW_ITEM TYPE MEPOITEM.
    DATA : LW_HEAD TYPE MEPOHEADER.
    DATA : LT_ITEM TYPE TABLE OF MEPOITEM.
    DATA : LT_KONV TYPE TABLE OF KOMV .
    DATA : LT_KONV_01 TYPE TABLE OF KOMV .
    DATA : LW_KONV TYPE KOMV .
    DATA : LV_KNUMH TYPE A118-KNUMH .
    DATA : LT_KONP TYPE TABLE OF KONP .
    DATA : LW_KONP TYPE KONP .
    DATA: LT_ITEMS TYPE PURCHASE_ORDER_ITEMS,
          LW_ITEMS TYPE PURCHASE_ORDER_ITEM.
    DATA : LM_KONV TYPE REF TO IF_PURCHASE_ORDER_ITEM_MM .
    DATA:LT_KONM TYPE TABLE OF  KONM,
         LW_KONM TYPE  KONM.
    DATA:LV_FLAG TYPE C.

***************************************************
***************************************************

    DATA: LT_BAPICONDCT TYPE TABLE OF  BAPICONDCT   ,  "
          LW_BAPICONDCT TYPE  BAPICONDCT,
          LT_BAPICONDHD TYPE TABLE OF BAPICONDHD ,  "
          LW_BAPICONDHD TYPE  BAPICONDHD,
          LT_BAPICONDIT TYPE TABLE OF BAPICONDIT,
          LW_BAPICONDIT TYPE  BAPICONDIT,
          LT_BAPICONDQS TYPE TABLE OF BAPICONDQS,
          LW_BAPICONDQS TYPE  BAPICONDQS,
          LT_BAPICONDVS TYPE TABLE OF BAPICONDVS,
          LW_BAPICONDVS TYPE  BAPICONDVS.

    DATA: LT_BAPIRET2 TYPE TABLE OF BAPIRET2 .
    DATA: LW_BAPIRET2 TYPE BAPIRET2 .
    DATA: LT_BAPIKNUMHS TYPE TABLE OF BAPIKNUMHS  .
    DATA: LT_MEM_INITIAL TYPE  TABLE OF CND_MEM_INITIAL.
    DATA : LV_MARKX(1) TYPE C ."需要扩大标识
    DATA: H_VARKEY(100),
          H_COND_UNIT TYPE MEINS .
***************************************************
***************************************************

    CLEAR : LT_EKPO[] .
    CLEAR : LT_ITEM[] .
    CLEAR : LT_KONV[] .
    CLEAR : LT_KONP[] .
    IF SY-TCODE = 'ME21N' OR SY-TCODE = 'ME22N' OR SY-TCODE = 'ME23N'
      OR SY-TCODE = 'ME29N' .
      LT_ITEMS = IM_HEADER->GET_ITEMS( ).
      LW_HEAD = IM_HEADER->GET_DATA( ).
      IF LW_HEAD-BSART = '913' OR  LW_HEAD-BSART = '914'
        OR  LW_HEAD-BSART = '915' OR  LW_HEAD-BSART = '916' .
      ELSE .
        CLEAR LW_ITEMS .
        LOOP AT LT_ITEMS INTO LW_ITEMS .

          CLEAR LW_ITEM .
          LW_ITEM = LW_ITEMS-ITEM->GET_DATA( ).
          APPEND LW_ITEM TO LT_ITEM .

          CLEAR LT_KONV_01[] .
          CALL METHOD LW_ITEMS-ITEM->GET_CONDITIONS
            IMPORTING
              EX_CONDITIONS = LT_KONV_01[].
          APPEND LINES OF LT_KONV_01 TO LT_KONV .

        ENDLOOP.

        CLEAR LW_ITEM  .
        LOOP AT LT_ITEM INTO LW_ITEM .
          CHECK LW_ITEM-UMSON = '' ."免费货物不检查
          CHECK LW_ITEM-LOEKZ = '' ."删除货物不检查
          CHECK LW_ITEM-MATNR <> '' ."无物料号不检查
          CLEAR LW_KONV .
          READ TABLE LT_KONV INTO LW_KONV
                  WITH KEY KPOSN = LW_ITEM-EBELP
                          KSCHL = 'PB00' .
          IF SY-SUBRC = 0 .

            CLEAR LW_EKPO .
            LW_EKPO-KBETR = LW_KONV-KBETR  .
            LW_EKPO-MENGE = LW_ITEM-MENGE  .
            LW_EKPO-MATNR = LW_ITEM-MATNR .
            LW_EKPO-WAERS = LW_KONV-WAERS .
            LW_EKPO-KMEIN = LW_KONV-KMEIN .
            LW_EKPO-KBET2 = LW_KONV-KBETR .
            LW_EKPO-KPEIN = LW_KONV-KPEIN .
            APPEND LW_EKPO TO LT_EKPO .
          ELSE .
            CLEAR LW_KONV .
            READ TABLE LT_KONV INTO LW_KONV
                    WITH KEY KPOSN = LW_ITEM-EBELP
                            KSCHL = 'PBXX' .
            IF SY-SUBRC = 0 .
              CLEAR LW_EKPO .
              LW_EKPO-MENGE = LW_ITEM-MENGE  .
              LW_EKPO-KBETR = LW_KONV-KBETR  .
              LW_EKPO-MATNR = LW_ITEM-MATNR .
              LW_EKPO-WAERS = LW_KONV-WAERS .
              LW_EKPO-KMEIN = LW_KONV-KMEIN .
              LW_EKPO-KBET2 = LW_KONV-KBETR .
              LW_EKPO-KPEIN = LW_KONV-KPEIN .
              APPEND LW_EKPO TO LT_EKPO .
            ENDIF .
          ENDIF .
          CLEAR LW_ITEM  .
        ENDLOOP .

        "取到唯一的最小值
        SORT LT_EKPO BY MATNR KBETR ASCENDING .
        DELETE ADJACENT DUPLICATES FROM LT_EKPO
                COMPARING MATNR .

        IF LT_EKPO[] IS NOT INITIAL .

          CLEAR LW_EKPO .
          LOOP AT LT_EKPO INTO LW_EKPO .
            CLEAR : LT_BAPICONDCT[]  ,
                    LT_BAPICONDHD[]  ,
                    LT_BAPICONDIT[]  ,
                    LT_BAPICONDQS[]  ,
                    LT_BAPICONDVS[]  ,
                    LT_BAPIRET2[]    ,
                    LT_BAPIKNUMHS[]  ,
                    LT_MEM_INITIAL[] .

            LW_EKPO-KBETR = LW_EKPO-KBET2 .
            SELECT SINGLE KNUMH
                INTO LV_KNUMH
                FROM A901
                WHERE KSCHL = 'PB00'
                  AND MATNR = LW_EKPO-MATNR
                  AND ZONWA = LW_EKPO-WAERS
                  AND DATBI >= SY-DATUM
                  AND DATAB <= SY-DATUM .
            IF SY-SUBRC = 0 .
              CLEAR: LT_KONP[],LT_KONM .
              SELECT *
                INTO TABLE LT_KONP
                FROM KONP
                WHERE KNUMH = LV_KNUMH  .
              IF SY-SUBRC = 0.
                SELECT * INTO CORRESPONDING FIELDS OF TABLE LT_KONM FROM KONM
                  FOR ALL ENTRIES IN LT_KONP WHERE KNUMH = LT_KONP-KNUMH AND KOPOS = LT_KONP-KOPOS." AND KSTBM <= LW_EKPO-MENGE.
              ENDIF.
              CLEAR LW_KONP .
              READ TABLE LT_KONP INTO LW_KONP INDEX 1 .

              SORT LT_KONM BY KNUMH KOPOS KSTBM DESCENDING."先取最大的，然后倒序，小于等于当前数量 赋值
              READ TABLE LT_KONM INTO LW_KONM INDEX 1.
              IF LT_KONM[] IS NOT INITIAL.
                LW_KONP-KBETR = LW_KONM-KBETR.
                LOOP AT LT_KONM INTO LW_KONM WHERE KSTBM >= LW_EKPO-MENGE.
                  LW_KONP-KBETR = LW_KONM-KBETR.
                ENDLOOP.
              ENDIF.

              IF LW_EKPO-WAERS <> LW_KONP-KONWA .
                CALL FUNCTION 'CONVERT_TO_FOREIGN_CURRENCY'
                  EXPORTING
                    DATE             = SY-DATUM
                    FOREIGN_CURRENCY = LW_KONP-KONWA
                    LOCAL_AMOUNT     = LW_EKPO-KBETR
                    LOCAL_CURRENCY   = LW_EKPO-WAERS
                  IMPORTING
                    FOREIGN_AMOUNT   = LW_EKPO-KBETR
                  EXCEPTIONS
                    NO_RATE_FOUND    = 1
                    OVERFLOW         = 2
                    NO_FACTORS_FOUND = 3
                    NO_SPREAD_FOUND  = 4
                    DERIVED_2_TIMES  = 5
                    OTHERS           = 6.
              ENDIF .

              IF LW_EKPO-KMEIN <> LW_KONP-KMEIN ."数量单位转换
                DATA : LV_MENGE TYPE EKPO-MENGE .
                LV_MENGE = LW_KONP-KPEIN .
                CALL FUNCTION 'MD_CONVERT_MATERIAL_UNIT'
                  EXPORTING
                    I_MATNR              = LW_ITEM-MATNR
                    I_IN_ME              = LW_KONP-KMEIN
                    I_OUT_ME             = LW_EKPO-KMEIN
                    I_MENGE              = LV_MENGE
                  IMPORTING
                    E_MENGE              = LV_MENGE
                  EXCEPTIONS
                    ERROR_IN_APPLICATION = 1
                    ERROR                = 2
                    OTHERS               = 3.

                LW_KONP-KBETR = LW_KONP-KBETR  / LV_MENGE * LW_EKPO-KPEIN .
              ELSE .
                LW_KONP-KBETR = LW_KONP-KBETR  / LW_KONP-KPEIN * LW_EKPO-KPEIN .
              ENDIF .

              IF LW_EKPO-KBETR < LW_KONP-KBETR .

                "   LW_KONP-KBETR = LW_EKPO-KBETR * LW_KONP-KPEIN  .
                LW_KONP-KBETR = LW_EKPO-KBET2 .
********************************************************
********************************************************
                CHECK LW_KONP-KBETR <> 0 .
                H_VARKEY  = LW_ITEM-MATNR  .
                LW_BAPICONDCT-OPERATION = '004'.         "更改
                LW_BAPICONDCT-COND_USAGE = 'A'.      "条件表用途 ‘A' 定价
                LW_BAPICONDCT-TABLE_NO = '901'.
                LW_BAPICONDCT-APPLICATIO = LW_KONP-KAPPL.
                LW_BAPICONDCT-COND_TYPE = LW_KONP-KSCHL.    "定价条件
                LW_BAPICONDCT-VARKEY = H_VARKEY.
                LW_BAPICONDCT-VALID_TO = '99991231'.
                LW_BAPICONDCT-VALID_FROM = SY-DATUM.
                LW_BAPICONDCT-COND_NO = LV_KNUMH.    "更改
                APPEND LW_BAPICONDCT TO LT_BAPICONDCT.

                LW_BAPICONDHD-OPERATION = '004'.
                LW_BAPICONDHD-COND_NO = LV_KNUMH.
                LW_BAPICONDHD-COND_USAGE = 'A'.
                LW_BAPICONDHD-TABLE_NO = '901'.
                LW_BAPICONDHD-APPLICATIO = LW_KONP-KAPPL.
                LW_BAPICONDHD-COND_TYPE = LW_KONP-KSCHL.
                LW_BAPICONDHD-VALID_FROM = SY-DATUM.
                LW_BAPICONDHD-VALID_TO = '99991231'.
                APPEND LW_BAPICONDHD TO  LT_BAPICONDHD .

                IF LT_KONM[] IS NOT INITIAL.
                  CLEAR:LV_FLAG.
                  LOOP AT LT_KONM INTO LW_KONM.

                    AT LAST.
                      IF LV_FLAG <> 'E'.
                        LV_FLAG = 'S'.
                      ENDIF.
                    ENDAT.

                    IF LW_KONM-KSTBM > LW_EKPO-MENGE." AND LV_FLAG <> 'E'."小于等于订单数量的第一个
                      IF LV_FLAG = 'S'.
                        LV_FLAG = 'X'.
                      ENDIF.
                    ELSEIF LW_KONM-KSTBM = LW_EKPO-MENGE.
                      LV_FLAG = 'X'.
                    ELSEIF LW_KONM-KSTBM < LW_EKPO-MENGE.
                      IF LV_FLAG = ''.
                        LV_FLAG = 'X'.
                      ENDIF.
                    ENDIF.
                    IF LV_FLAG = 'X'.
                      LW_BAPICONDQS-CURRENCY = LW_KONP-KBETR.
                      LV_FLAG = 'E'.
                    ELSE.
                      LW_BAPICONDQS-CURRENCY = LW_KONM-KBETR.
                    ENDIF.
                    LW_BAPICONDQS-OPERATION  = '004'.
                    LW_BAPICONDQS-COND_NO    = LW_KONM-KNUMH.
                    LW_BAPICONDQS-COND_COUNT = LW_KONM-KOPOS.
                    LW_BAPICONDQS-LINE_NO    = LW_KONM-KLFN1.
                    LW_BAPICONDQS-SCALE_QTY  = LW_KONM-KSTBM.
                    LW_BAPICONDQS-COND_UNIT  = LW_EKPO-KMEIN.
                    LW_BAPICONDQS-CONDCURR   = LW_KONP-KONWA.
                    APPEND LW_BAPICONDQS TO LT_BAPICONDQS.
                  ENDLOOP.
                  LW_BAPICONDIT-UNITMEASUR = LW_EKPO-KMEIN.
                ENDIF.

                LW_BAPICONDIT-OPERATION = '004'.   "修改
                LW_BAPICONDIT-COND_NO = LV_KNUMH.
                LW_BAPICONDIT-COND_COUNT = '01'.     "条件序列号
                LW_BAPICONDIT-APPLICATIO = LW_KONP-KAPPL.
                LW_BAPICONDIT-COND_TYPE =  LW_KONP-KSCHL.       "条件类型
                LW_BAPICONDIT-SCALETYPE = 'A'.       "STFKZ Staffelsoort
                LW_BAPICONDIT-SCALEBASIN = LW_KONP-STFKZ.      "KZBZG Teken:rekeneenheid "  LW_bapicondit-scalebasin = 'C'. 存在数量等级
                LW_BAPICONDIT-SCALE_QTY = LW_KONP-KSTBM.       "KSTBM Conditiestaffelbasis hoeveelheid
                LW_BAPICONDIT-COND_P_UNT = LW_EKPO-KPEIN .      "KPEIN prijseenheid
                LW_BAPICONDIT-COND_UNIT = LW_EKPO-KMEIN.      "KMEIN Conditie-hoeveelheidseenheid
                LW_BAPICONDIT-CALCTYPCON = LW_KONP-KRECH.      "KRECH Conditie-rekenregel
                LW_BAPICONDIT-COND_VALUE = LW_KONP-KBETR.
                LW_BAPICONDIT-CONDCURR = LW_KONP-KONWA.
                APPEND  LW_BAPICONDIT  TO LT_BAPICONDIT  .


                CALL FUNCTION 'BAPI_PRICES_CONDITIONS'
                  TABLES
                    TI_BAPICONDCT  = LT_BAPICONDCT
                    TI_BAPICONDHD  = LT_BAPICONDHD
                    TI_BAPICONDIT  = LT_BAPICONDIT
                    TI_BAPICONDQS  = LT_BAPICONDQS
                    TI_BAPICONDVS  = LT_BAPICONDVS
                    TO_BAPIRET2    = LT_BAPIRET2
                    TO_BAPIKNUMHS  = LT_BAPIKNUMHS
                    TO_MEM_INITIAL = LT_MEM_INITIAL
                  EXCEPTIONS
                    UPDATE_ERROR   = 1
                    OTHERS         = 2.
                CLEAR LW_BAPIRET2 .
                READ TABLE LT_BAPIRET2 INTO LW_BAPIRET2 WITH KEY TYPE = 'E' .
                IF SY-SUBRC <> 0 .
                  COMMIT WORK AND WAIT .
                ELSE .
                  ROLLBACK WORK .
                  CALL FUNCTION 'ZMMFM001'
                    EXPORTING
                      EBELN    = LW_HEAD-EBELN
                      MATNR    = LW_EKPO-MATNR
                      KBETR    = LW_KONP-KBETR
                    TABLES
                      BAPIRET2 = LT_BAPIRET2.

                ENDIF .
********************************************************
********************************************************
              ENDIF .

            ELSE .

              SELECT SINGLE KNUMH
                 INTO LV_KNUMH
                 FROM A901
                 WHERE KSCHL = 'PB00'
                   AND MATNR = LW_EKPO-MATNR
                   AND DATBI >= SY-DATUM
                   AND DATAB <= SY-DATUM .
              IF SY-SUBRC <> 0 .

********************************************************
********************************************************
*               CHECK LW_KONP-KBETR <> 0 .
                LW_KONP-KAPPL = 'M' .
                LW_KONP-KSCHL = 'PB00' .
                LW_KONP-STFKZ = 'A' .
                LW_KONP-KSTBM = 0 .
                LW_KONP-KPEIN = LW_EKPO-KPEIN .
                LW_KONP-KMEIN = LW_EKPO-KMEIN .
                LW_KONP-KRECH = 'C' .
                LW_KONP-KBETR = LW_EKPO-KBET2 .
                LW_KONP-KONWA = LW_EKPO-WAERS .
                LV_KNUMH = '$000000001'  .

                CONCATENATE  LW_EKPO-MATNR LW_KONP-KONWA INTO H_VARKEY   .
                LW_BAPICONDCT-OPERATION = '009'.         "更改
                LW_BAPICONDCT-COND_USAGE = 'A'.      "条件表用途 ‘A' 定价
                LW_BAPICONDCT-TABLE_NO = '901'.
                LW_BAPICONDCT-APPLICATIO = LW_KONP-KAPPL.
                LW_BAPICONDCT-COND_TYPE = LW_KONP-KSCHL.    "定价条件
                LW_BAPICONDCT-VARKEY = H_VARKEY.
                LW_BAPICONDCT-VALID_TO = '99991231'.
                LW_BAPICONDCT-VALID_FROM = SY-DATUM.
                LW_BAPICONDCT-COND_NO = LV_KNUMH.    "更改
                APPEND LW_BAPICONDCT TO LT_BAPICONDCT.

                LW_BAPICONDHD-OPERATION = '009'.
                LW_BAPICONDHD-COND_NO = LV_KNUMH.
                LW_BAPICONDHD-COND_USAGE = 'A'.
                LW_BAPICONDHD-TABLE_NO = '901'.
                LW_BAPICONDHD-APPLICATIO = LW_KONP-KAPPL.
                LW_BAPICONDHD-COND_TYPE = LW_KONP-KSCHL.
                LW_BAPICONDHD-VARKEY = H_VARKEY.
                LW_BAPICONDHD-VALID_FROM = SY-DATUM.
                LW_BAPICONDHD-VALID_TO = '99991231'.
                APPEND LW_BAPICONDHD TO  LT_BAPICONDHD .

                LW_BAPICONDIT-OPERATION = '009'.   "修改
                LW_BAPICONDIT-COND_NO = LV_KNUMH.
                LW_BAPICONDIT-COND_COUNT = '01'.     "条件序列号
                LW_BAPICONDIT-APPLICATIO = LW_KONP-KAPPL.
                "LW_BAPICONDIT-MATLSETTL = LW_EKPO-MATNR.
                LW_BAPICONDIT-COND_TYPE =  LW_KONP-KSCHL.       "条件类型
                LW_BAPICONDIT-SCALETYPE = 'A'.       "STFKZ Staffelsoort
                LW_BAPICONDIT-SCALEBASIN = LW_KONP-STFKZ.      "KZBZG Teken:rekeneenheid "  LW_bapicondit-scalebasin = 'C'. 存在数量等级
                LW_BAPICONDIT-SCALE_QTY = LW_KONP-KSTBM.       "KSTBM Conditiestaffelbasis hoeveelheid
                LW_BAPICONDIT-COND_P_UNT = LW_KONP-KPEIN .      "KPEIN prijseenheid
                LW_BAPICONDIT-COND_UNIT = LW_KONP-KMEIN.      "KMEIN Conditie-hoeveelheidseenheid
                LW_BAPICONDIT-CALCTYPCON = LW_KONP-KRECH.      "KRECH Conditie-rekenregel
                LW_BAPICONDIT-COND_VALUE = LW_KONP-KBETR.
                LW_BAPICONDIT-CONDCURR = LW_KONP-KONWA.
                APPEND  LW_BAPICONDIT  TO LT_BAPICONDIT  .

                CALL FUNCTION 'BAPI_PRICES_CONDITIONS'
                  TABLES
                    TI_BAPICONDCT  = LT_BAPICONDCT
                    TI_BAPICONDHD  = LT_BAPICONDHD
                    TI_BAPICONDIT  = LT_BAPICONDIT
                    TI_BAPICONDQS  = LT_BAPICONDQS
                    TI_BAPICONDVS  = LT_BAPICONDVS
                    TO_BAPIRET2    = LT_BAPIRET2
                    TO_BAPIKNUMHS  = LT_BAPIKNUMHS
                    TO_MEM_INITIAL = LT_MEM_INITIAL
                  EXCEPTIONS
                    UPDATE_ERROR   = 1
                    OTHERS         = 2.
                CLEAR LW_BAPIRET2 .
                READ TABLE LT_BAPIRET2 INTO LW_BAPIRET2 WITH KEY TYPE = 'E' .
                IF SY-SUBRC <> 0 .
                  COMMIT WORK AND WAIT .
                ELSE .
                  ROLLBACK WORK .
                  CALL FUNCTION 'ZMMFM001'
                    EXPORTING
                      EBELN    = LW_HEAD-EBELN
                      MATNR    = LW_EKPO-MATNR
                      KBETR    = LW_KONP-KBETR
                    TABLES
                      BAPIRET2 = LT_BAPIRET2.
                ENDIF .

              ENDIF .

            ENDIF .
          ENDLOOP .
        ENDIF .

      ENDIF .
    ENDIF .
  ENDMETHOD.
```

