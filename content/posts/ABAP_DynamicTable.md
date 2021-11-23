---
title: " ABAP 动态内表 "
date: 2019-07-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

## ABAP动态内表操作

动态内表的列是可以跟随数据的变化而变化，可以使报表显示更简洁漂亮。

```ABAP
REPORT  zdyn_test. 
DATA: dyn_table TYPE REF TO data, 
      dyn_line  TYPE REF TO data,
FIELD-SYMBOLS: <dyn_table> TYPE STANDARD TABLE.
FIELD-SYMBOLS: <dyn_wa> TYPE ANY. 
FIELD-SYMBOLS: <dyn_field> TYPE ANY. 
DATA: layout TYPE LVC_S_LAYO,
      structures TYPE lvc_t_fcat, 
      structure TYPE lvc_s_fcat. 
START-OF-SELECTION. 
PERFORM create_structure.       "定义内表的结构" 
PERFORM create_dynamic_table.   "按照定义的内表结构，产生一个内表"
PERFORM write_data_to_dyntable. "向动态内表中写数"
PERFORM output_dyntable_data.   "从动态内表中取数，并写到屏幕"
```

### Step1：创建动态内表

#### 动态内表的结构的定义. 

动态内表表结构的定义必须使用表结构与 `lvc_t_fcat` 一样的内表。一般情况下，我们都内表的所有列定义成字符型. 

```ABAP
*&-----------------------------------------------------------* 
*&      Form  create_structure 
*&-----------------------------------------------------------* 
FORM create_structure . 
  structure-fieldname = 'COL1'.  " 第一列列名 " 
  structure-col_pos   = 1.       
  structure-inttype = 'C'.       " 数据类型 "
  structure-intlen = 6.          " 长度 " 
  APPEND structure TO structures. 
  structure-fieldname = 'COL2'.  " 第二列列名 " 
  structure-col_pos   = 2.       
  structure-inttype = 'C'.       " 数据类型 " 
  structure-intlen = 6.          " 长度 " 
  APPEND wa_structure TO structures. 
  structure-fieldname = 'COL3'.  " 第三列名 " 
  structure-col_pos   = 3.        
  structure-inttype = 'C'.       " 数据类型 " 
  structure-intlen = 6.          " 长度 " 
  APPEND structure TO structures. 
ENDFORM.                    " create_structure "
```

#### 根据表结构生成内表. 

系统提供了一个标准的 method 来产生动态表，使用方法如下:

```ABAP
*&-----------------------------------------------------------* 
*&      Form  create_dynamic_table 
*&-----------------------------------------------------------* 
FORM create_dynamic_table . 
  CALL METHOD cl_alv_table_create=>create_dynamic_table 
    EXPORTING 
      it_fieldcatalog = structures
    IMPORTING 
      ep_table        = dyn_table. 
  "用表类型指针<dyn_table>指向数据对象的内容. "
  ASSIGN dyn_table->* TO <dyn_table>. 
ENDFORM.                    " create_dynamic_table " 
```

### Step2：动态内表的赋值

#### 获取指定的字段并给指定的字段赋值 

```ABAP
*&-----------------------------------------------------------* 
*&      Form  write_data_to_dyntable 
*&-----------------------------------------------------------* 
FORM write_data_to_dyntable . 
  DATA:i TYPE n. 
  DATA:j TYPE n. 
  " 建立一个与动态内表结构相同的数据对象，且数据对象为是一个结构 " 
  CREATE DATA dyn_line LIKE LINE OF <dyn_table>.  
  ASSIGN dyn_line->* TO <dyn_wa>. " 用<dyn_wa>指针指向该结构 " 
  DO 3 TIMES. 
    i = i + 1. 
    CLEAR j. 
    LOOP AT structures INTO structure.  
      j = j + 1. 
      " 用指针<dyn_field>指向工作区<dyn_wa>中的一个字段，字段名为structure-fieldname. " 
      ASSIGN COMPONENT structure-fieldname OF STRUCTURE <dyn_wa> TO <dyn_field>.  
      CONCATENATE i j INTO <dyn_field>.  " 给指针指向的字段赋值 "
    ENDLOOP. 
    APPEND <dyn_wa> TO <dyn_table>. 
    CLEAR <dyn_wa>.
  ENDDO. 
ENDFORM.                    " write_data_to_dyntable " 
*&-----------------------------------------------------------* 
```

### Step3：读取动态内表的值

#### 获取指定的字段并读取指定的字段值 

```ABAP
*&-----------------------------------------------------------* 
*&      Form  output_dyntable_data 
*&-----------------------------------------------------------* 
FORM output_dyntable_data . 
  LOOP AT structures INTO structure. 
    WRITE: structure-fieldname(5). 
  ENDLOOP. 
  LOOP AT <dyn_table> INTO <dyn_wa>. 
    WRITE: / . 
    CLEAR structure.
    LOOP AT structures INTO structure.
    " 用指针<dyn_field>指向工作区<dyn_wa>中的一个字段，字段名为structure-fieldname. "
      ASSIGN COMPONENT structure-fieldname OF STRUCTURE <dyn_wa> TO <dyn_field>.   
     WRITE: <dyn_field>. 
    ENDLOOP. 
  ENDLOOP. 
ENDFORM.                    " output_dyntable_data "
```

