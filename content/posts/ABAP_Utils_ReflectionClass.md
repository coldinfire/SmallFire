---
title: " ABAP 获取CLASS参数结果 "
date: 2021-04-27
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---



### Read Class Data

```ABAP
data: i_class type klah-class.
data: i_classtype type klah-klart.
data: i_key_date type sy-datum default sy-datum.
data: e_structure type ref to cl_abap_structdescr,
      et_data type ref to data,
      e_table type ref to cl_abap_tabledescr.
field-symbols: <char> type bapi1003_charact_r.
data: mt_classdescriptions type standard table of bapi1003_catch_r.
data: mt_classlongtexts type standard table of bapi1003_longtext_r.
data: mt_classcharacteristics type standard table of bapi1003_charact_r.
data: mt_classcharactvalues type standard table of bapi1003_char_val_r.
data: mt_return type standard table of bapiret2.
data: ms_charact_detail type bapicharactdetail.
data: m_lenght type i.
data: m_decimals type i.
data: mt_comp type cl_abap_structdescr=>component_table.
data: mt_comp_final type cl_abap_structdescr=>component_table.
data: mo_str  type ref to cl_abap_structdescr.
data: mo_tab type ref to cl_abap_tabledescr.
data: mt_data type ref to data.
data: ms_comp like line of mt_comp.
data: ms_return type bapiret2.
"read class data"
  call function 'BAPI_CLASS_READ'
    exporting
      classtype            = i_classtype
      classnum             = i_class
*     LANGUISO             = LANGUISO
*     LANGUINT             = SY-LANGU
      keydate              = i_key_date
    importing
*     CLASSBASICDATA       = CLASSBASICDATA
*     CLASSDOCUMENT        = CLASSDOCUMENT
*     CLASSADDITIONAL      = CLASSADDITIONAL
*     CLASSSTANDARD        = CLASSSTANDARD
      return               = ms_return
    tables
      classdescriptions    = mt_classdescriptions
      classlongtexts       = mt_classlongtexts
      classcharacteristics = mt_classcharacteristics
      classcharactvalues   = mt_classcharactvalues.
if ms_return-type CN 'AE'.
    "firstly add key fields for structure like Object, class, class type,"
    " object table "
    ms_comp-name = 'OBJECT_NUMBER'.
    ms_comp-type ?= cl_abap_elemdescr=>describe_by_name( p_name = 'OBJNUM' ).
    append ms_comp to mt_comp_final.
    ms_comp-name = 'CLASS'.
    ms_comp-type ?= cl_abap_elemdescr=>describe_by_data( p_data = i_class ).
    append ms_comp to mt_comp_final.
    ms_comp-name = 'CLASSTYPE'.
    ms_comp-type ?= cl_abap_elemdescr=>describe_by_data( p_data = i_classtype ).
    append ms_comp to mt_comp_final.
    ms_comp-name = 'OBJECT_TABLE'.
    ms_comp-type ?= cl_abap_elemdescr=>describe_by_name( p_name = 'TABELLE' ).
    append ms_comp to mt_comp_final.
    "Then let's loop through characteristics"
    loop at mt_classcharacteristics assigning <char>.
      clear ms_charact_detail.
      "check each characteristic details"
      call function 'BAPI_CHARACT_GETDETAIL'
        exporting
          charactname         = <char>-name_char
          keydate             = i_key_date
*         LANGUAGE            = LANGUAGE
        importing
          charactdetail       = ms_charact_detail
        tables
*         CHARACTDESCR        = CHARACTDESCR
*         CHARACTVALUESNUM    = CHARACTVALUESNUM
*         CHARACTVALUESCHAR   = CHARACTVALUESCHAR
*         CHARACTVALUESCURR   = CHARACTVALUESCURR
*         CHARACTVALUESDESCR  = CHARACTVALUESDESCR
*         CHARACTREFERENCES   = CHARACTREFERENCES
*         CHARACTRESTRICTIONS = CHARACTRESTRICTIONS
          return              = mt_return.
      "check if we have error"
      loop at mt_return into ms_return where type ca 'AE'.
        raise characteristics_error.
      endloop.
      refresh mt_comp[].
      clear: ms_comp.
      m_lenght = ms_charact_detail-length.
      m_decimals = ms_charact_detail-decimals.
      clear: ms_comp.
      ms_comp-name = 'LOW'.
      "depanding on data type we create different type for LOW component"
      case ms_charact_detail-data_type.
        when 'NUM'.
          ms_comp-type = cl_abap_elemdescr=>get_p(
                p_length   = m_lenght
                p_decimals = m_decimals  ).
        when 'DATE'.
          ms_comp-type = cl_abap_elemdescr=>get_d( ).
        when 'TIME'.
          ms_comp-type = cl_abap_elemdescr=>get_t( ).
        when 'CHAR'.
          ms_comp-type = cl_abap_elemdescr=>get_c( p_length = m_lenght ).
        when 'CURR'.
          ms_comp-type = cl_abap_elemdescr=>get_p(
                p_length   = m_lenght
                p_decimals = m_decimals  ).
        when others.
          ms_comp-type = cl_abap_elemdescr=>get_c( p_length = m_lenght ).
      endcase.
      append ms_comp to mt_comp.
      "HIGH component will have same type as LOW so I do not clear work area"
      ms_comp-name = 'HIGH'.
      append ms_comp to mt_comp.
      "now OPTION for LOW"
      clear: ms_comp.
      ms_comp-name = 'OPTLOW'.
      ms_comp-type = cl_abap_elemdescr=>get_c( p_length = 2 ).
      append ms_comp to mt_comp.
      "OPTION for HIGH"
      clear: ms_comp.
      ms_comp-name = 'OPTHIGH'.
      ms_comp-type = cl_abap_elemdescr=>get_c( p_length = 2 ).
      append ms_comp to mt_comp.
      "UNIT FROM"
      clear: ms_comp.
      ms_comp-name = 'UNIT_FROM'.
      ms_comp-type ?= cl_abap_elemdescr=>describe_by_data( p_data = ms_charact_detail-unit_of_measurement ).
      append ms_comp to mt_comp.
      "UNIT TO"
      clear: ms_comp.
      ms_comp-name = 'UNIT_TO'.
      ms_comp-type ?= cl_abap_elemdescr=>describe_by_data( p_data = ms_charact_detail-unit_of_measurement ).
      append ms_comp to mt_comp.
      "CURRENCY FROM"
      clear: ms_comp.
      ms_comp-name = 'CURRENCY_FROM'.
      ms_comp-type ?= cl_abap_elemdescr=>describe_by_data( p_data = ms_charact_detail-currency  ).
      append ms_comp to mt_comp.
      "CURRENCY TO"
      clear: ms_comp.
      ms_comp-name = 'CURRENCY_TO'.
      ms_comp-type ?= cl_abap_elemdescr=>describe_by_data( p_data = ms_charact_detail-currency  ).
      append ms_comp to mt_comp.
      "We got all components so we can create structure of a characteristic"
      mo_str =  cl_abap_structdescr=>create( mt_comp ).
      "and we can create table for this characteristic"
      mo_tab = cl_abap_tabledescr=>create( p_line_type  = mo_str
                                           p_table_kind = cl_abap_tabledescr=>tablekind_std
                                           p_unique     = abap_false ).

      "then we add the characteristic table as a component for our final structure"
      clear ms_comp.
      ms_comp-name = <char>-name_char.
      ms_comp-type ?= mo_tab.
      append ms_comp to mt_comp_final.
      free mo_tab.
      free mo_str.
    endloop.
"we've got now key fields + separate deep field for each characteristic so we can"
    "create our final structure and table"
    if mt_comp_final[] is not initial.
      e_structure =  cl_abap_structdescr=>create( mt_comp_final ).
      e_table      = cl_abap_tabledescr=>create(  p_line_type  = e_structure
                                                  p_table_kind = cl_abap_tabledescr=>tablekind_std
                                                  p_unique     = abap_false ).
      try.
          create data et_data  type handle e_table.
        catch cx_sy_create_data_error.
          raise error_creating_table.
      endtry.
    endif.
  else.
    raise class_classtype_fetch_error.
  endif.
```

