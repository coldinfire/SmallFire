---
title: " ABAP 反射 "
date: 2021-04-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### RTTS(Runtime Type Services) 使用

在 ABAP 中，反射是通过运行时类型服务 (RTTS) 提供的。该服务提供了两个主要功能：在运行时识别类型和描述，以及动态创建类型。更具体地说，RTTS 是以下各项的组合：

- *Run Time Type Identification (RTTI)*：运行时类型识别，类型识别和描述（类型自省）
- *RunTime Type Creation (RTTC)* ：运行时类型创建，动态类型创建

所有与 RTTS 相关的类都命名为 CL_ABAP_*DESCR，具体取决于该类所代表的类型种类。CL_ABAP_ELEMDESCR 是基本数据类型的描述类，CL_ABAP_TABLEDESCR 是表类型等等。所有这些动态是利用 CL_ABAP_TYPEDESCR 类及其子类实现的。

对于不同的类型都有不同的描述类，为了得到一个类型的 description object 你必须使用 CL_ABAP_TYPEDESCR 的静态方法来得到相应的 description class。在 runtime 中，每一个类型只有一个 description object。

- describe_by_name：从结构定义获取的方法

- describe_by_data：从内表变量中获取的方法

  ![ABAP Reflection](/images/ABAP/ABAP_Reflection_1.png)

### 动态获取结构/透明表结构

使用 cl_abap_typedescr 类的 describe_by_name 静态方法，使用这个方法获取结构的描述信息，从描述信息里可以得到结构的字段列表等信息。

```ABAP
"根据结构名获取结构描述信息 "
DATA: struct_type TYPE REF TO cl_abap_structdescr,
      gt_detail TYPE abap_compdescr_tab,
      gs_detail TYPE abap_compdescr.
DATA: field_name TYPE string.
FIELD-SYMBOLS: <field_value>.
struct_type ?= cl_abap_typedescr=>describe_by_name( 'spfli' ).
"从结构描述信息中获取字段列表"
gt_detail[] = struct_type->components[].
LOOP gt_detail INTO gs_detail.
  "使用FIELD-SYMBOLS获取特定字段中的值"  
  ASSIGN (field_name) TO <field_value>.  
  CLEAR <field_value>.
ENDLOOP.



DATA: elem_type TYPE REF TO cl_abap_elemdescr.
elem_type = cl_abap_elemdescr=>get_i( ).
elem_type = cl_abap_elemdescr=>get_c( 20 ).

DATA: oref1 TYPE REF TO object.
DATA: descr_ref1 TYPE REF TO cl_abap_typedescr.
CREATE OBJECT oref1 TYPE ('C1'). "C1为类名"
descr_ref1 = cl_abap_typedescr=>describe_by_object_ref( oref1 ).
```

### 动态获取内表的结构

通过 cl_abap_typedescr 类的 describe_by_data 静态方法获取了内表变量的描述信息，然后再调用描述信息的 get_table_line_type 方法获取单行的类型变量。

```ABAP
DATA: gt_itab TYPE TABLE OF spfli.  
DATA: table_type TYPE REF TO cl_abap_tabledescr,  
      ls_table_typeW TYPE REF TO cl_abap_structdescr,  
      wa_table   TYPE abap_compdescr.  
table_type ?= cl_abap_typedescr=>describe_by_data( gt_itab ).  
ls_table_type ?= table_type->get_table_line_type( ).  
```



```ABAP
DATA: data_type TYPE REF TO cl_abap_datadescr,
      field(5) TYPE c.
data_type ?= cl_abap_typedescr=>describe_by_data( field ).
```

