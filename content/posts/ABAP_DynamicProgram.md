---

title: " ABAP Dynnamic Programer"
date: 2019-09-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 利用Field Symbols和数据参考的实例：

```JS
*&--------------------------------------------------------------------
*& Report  ZZSM_TEST_DYN
*&--------------------------------------------------------------------
REPORT  zzsm_test_dyn.
DATA: tab_reference TYPE REF TO data,
      struc_reference TYPE REF TO data.
DATA: descr TYPE REF TO cl_abap_structdescr.
FIELD-SYMBOLS: <struc> TYPE ANY,
               <field> TYPE ANY,
               <itab> TYPE ANY TABLE.
PARAMETERS: p_table(20) DEFAULT 'LFA1' OBLIGATORY,
            p_rows   TYPE i OBLIGATORY.
TRY .
    "Dynamic create Table & Structure "
    CREATE DATA tab_reference TYPE STANDARD TABLE OF (p_table)
           WITH NON-UNIQUE DEFAULT KEY.
    ASSIGN tab_reference->* TO <itab>.
    
    CREATE DATA struc_reference TYPE (p_table).
    ASSIGN struc_reference->* TO <struc>.
    "Get data from table"
    SELECT * FROM (p_table) INTO TABLE <itab> UP TO p_rows ROWS .
    "Get structure desc by struc"
    descr ?=  cl_abap_typedescr=>describe_by_data( <struc> ).
    "Data processing logic"
    LOOP AT <itab> INTO <struc>.
      DO 20 TIMES.
        ASSIGN COMPONENT sy-index OF STRUCTURE <struc> TO <field>.
        IF sy-subrc EQ 0.
          WRITE: <field>.
        ELSE.
          EXIT.
        ENDIF.
      ENDDO.
    ENDLOOP.
  CATCH cx_sy_create_data_error.
    WRITE 'Wrong Database!'.
ENDTRY.
```





