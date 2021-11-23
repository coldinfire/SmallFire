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

  ![ABAP Reflection](/images/ABAP/ABAP_Reflection.png)

### 动态获取结构/透明表结构

使用 CL_ABAP_TYPEDESCR 类的 describe_by_name 静态方法，使用这个方法获取结构的描述信息，从描述信息里可以得到结构的字段列表等信息。

COMPONENTS 属性提供了一个内部表，该表按名称列出结构上的所有单个字段，包括每个字段的数据类型（按其相对名称）。

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
  CONCATENATE 'SPFLI-' gs_detail-name INTO field_name. 
  ASSIGN (field_name) TO <field_value>.  
  CLEAR <field_value>.
ENDLOOP.
```

### 动态获取内表的结构

通过 cl_abap_typedescr 类的 describe_by_data 静态方法获取了内表变量的描述信息，然后再调用描述信息的 get_table_line_type 方法获取单行的类型变量。

可通过单行变量动态获取结构中的各个字段名称、类型、长度及精度信息。

```ABAP
DATA: gt_itab TYPE TABLE OF spfli.  
DATA: table_type TYPE REF TO cl_abap_tabledescr,  
      table_line_type TYPE REF TO cl_abap_structdescr,  
      wa_table   TYPE abap_compdescr.  
table_type ?= cl_abap_typedescr=>describe_by_data( gt_itab ).  
table_line_type ?= table_type->get_table_line_type( ).  
LOOP AT table_line_type->components INTO wa_table .  
  WRITE :/ wa_table-name,  
           wa_table-type_kind,  
           wa_table-length,  
           wa_table-decimals.  
ENDLOOP.  
```

也可以通过 describe_by_data 获取字段信息。

```ABAP
DATA: data_type TYPE REF TO cl_abap_datadescr,
      field(5) TYPE c,
      kind(1)  TYPE c.
data_type ?= cl_abap_typedescr=>describe_by_data( field ).
kind = data_type->kind. "获取数据元素类型"
```

### 动态获取数据元素信息

CL_ABAP_DATADESCR 的实现类，可以获取 Element 信息。

![CL_ABAP_DLEMDESCR](/images/ABAP/ABAP_Reflection1.png)

![CL_ABAP_DLEMDESCR](/images/ABAP/ABAP_Reflection2.png)

```ABAP
DATA: elem_type TYPE REF TO cl_abap_elemdescr.
elem_type = cl_abap_elemdescr=>get_i( ).
elem_type = cl_abap_elemdescr=>get_c( 20 ).
```

### 动态获取类信息

```ABAP
DATA: oref1 TYPE REF TO object.
DATA: descr_ref1 TYPE REF TO cl_abap_typedescr.
CREATE OBJECT oref1 TYPE ('C1'). "C1为类名"
descr_ref1 = cl_abap_typedescr=>describe_by_object_ref( oref1 ).
```

