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

- *Run Time Type Identification (RTTI)* ：运行时类型识别，类型识别和描述（类型自省）
- *RunTime Type Creation (RTTC)* ：运行时类型创建，动态类型创建

所有与 RTTS 相关的类都命名为 CL_ABAP_*DESCR，具体取决于该类所代表的类型种类。CL_ABAP_ELEMDESCR 是基本数据类型的描述类，CL_ABAP_TABLEDESCR 是表类型等等。所有这些动态是利用 CL_ABAP_TYPEDESCR 类及其子类实现的。

对于不同的类型都有不同的描述类，为了得到一个类型的 description object 你必须使用 CL_ABAP_TYPEDESCR 的静态方法来得到相应的 description class。在 runtime 中，每一个类型只有一个 description object。

- describe_by_name：从结构定义获取的方法

- describe_by_data：从内表变量中获取的方法

  ![ABAP Reflection](/images/ABAP/ABAP_Reflection.png)

### RTTI 使用

#### 动态获取 Structure 结构

使用 CL_ABAP_TYPEDESCR 类的 describe_by_name 静态方法，使用这个方法获取结构的描述信息，从描述信息里可以得到结构的字段列表等信息。

COMPONENTS 属性提供了一个内部表，该表按名称列出结构上的所有单个字段，包括每个字段的名称、数据类型（按其相对名称）、长度、小数位。

```ABAP
"根据结构名获取结构描述信息 "
DATA: struct_type TYPE REF TO cl_abap_structdescr,
      gt_detail TYPE abap_compdescr_tab,
      gs_detail TYPE abap_compdescr.
DATA: field_name TYPE string,
      lv_type TYPE string.
struct_type ?= cl_abap_typedescr=>describe_by_name( 'SPFLI' ).
lv_type = struct_type->get_relative_name( ).
WRITE: / lv_type.
"从结构描述信息中获取字段列表"
gt_detail[] = struct_type->components[].
LOOP AT gt_detail INTO gs_detail.
  WRITE :/ gs_detail-name,  
           gs_detail-type_kind,  
           gs_detail-length,  
           gs_detail-decimals. 
ENDLOOP.
```

#### 动态获取内表的结构

通过 cl_abap_typedescr 类的 describe_by_data 静态方法获取了内表变量的描述信息，然后再调用描述信息的 get_table_line_type 方法获取单行的类型变量。

可通过单行变量动态获取结构中的各个字段名称、类型、长度及精度信息。

```ABAP
DATA: gt_itab TYPE TABLE OF spfli.  
DATA: table_type TYPE REF TO cl_abap_tabledescr,  
      table_line_type TYPE REF TO cl_abap_structdescr,  
      wa_table TYPE abap_compdescr,
      lv_type  TYPE string.  
table_type ?= cl_abap_typedescr=>describe_by_data( gt_itab ).
WRITE: / lv_type.
table_line_type ?= table_type->get_table_line_type( ).  
LOOP AT table_line_type->components INTO wa_table .  
  WRITE :/ wa_table-name,  
           wa_table-type_kind,  
           wa_table-length,  
           wa_table-decimals.  
ENDLOOP.  
```

也可以通过 describe_by_data 直接获取字段信息。

```ABAP
DATA: data_type TYPE REF TO cl_abap_datadescr,
      field(5) TYPE c,
      kind(1)  TYPE c.
data_type ?= cl_abap_typedescr=>describe_by_data( field ).
kind = data_type->kind. "获取数据元素类型"
```

#### 动态获取数据元素信息

CL_ABAP_DATADESCR 的实现类，可以获取 Element 信息。

![CL_ABAP_DLEMDESCR](/images/ABAP/ABAP_Reflection1.png)

![CL_ABAP_DLEMDESCR](/images/ABAP/ABAP_Reflection2.png)

```ABAP
DATA: elem_type TYPE REF TO cl_abap_elemdescr.
      matnr TYPE matnr,
      kind(1)  TYPE c,
      edit_mask TYPE string,
      help_id TYPE string,
      output_length TYPE string.
elem_type ?= cl_abap_typedescr=>describe_by_data( matnr ).
kind = elem_type->kind. "获取数据元素类型"
edit_mask = elem_type->edit_mask.
help_id   = elem_type->help_id.
output_length = elem_type->output_length.
WRITE: / kind,
         edit_mask,
         help_id,
         output_length.
```

#### 动态获取类对象信息

```ABAP
CLASS c1 DEFINITION.
  PUBLIC SECTION.
    DATA: c VALUE 'C'.
    METHODS: test.
ENDCLASS.
CLASS c1 IMPLEMENTATION.
  METHOD:test.
    WRITE:/ 'test'.
  ENDMETHOD.
ENDCLASS.
DATA: oref TYPE REF TO object .
DATA: oref_classdescr TYPE REF TO cl_abap_classdescr
DATA: gt_attrdescr_tab TYPE abap_attrdescr_tab WITH HEADER LINE,"类中的属性列表"
      gt_methdescr_tab TYPE abap_methdescr_tab WITH HEADER LINE."类中的方法列表"
FIELD-SYMBOLS <fs_attr> TYPE any.
CREATE OBJECT oref TYPE ('C1'). "C1为类名"
"类似JAVA中的Class类对象"
oref_classdescr = cl_abap_typedescr=>describe_by_object_ref( oref ).
gt_attrdescr_tab[] = oref_classdescr->attributes.
gt_methdescr_tab[] = oref_classdescr->methods.
LOOP AT gt_attrdescr_tab."动态访问类中的属性
  ASSIGN oref->(gt_attrdescr_tab-name) TO <fs_attr>.
  WRITE: / <fs_attr>.
ENDLOOP.
LOOP AT gt_methdescr_tab."动态访问类中的方法
  CALL METHOD oref->(gt_methdescr_tab-name).
ENDLOOP.
```

### RTTC 使用

#### 动态创建数据Data 或对象 Object

根据基本类型名动态创建数据，handle 只能是 **CL_ABAP_DATADESCR** 或其子类的引用变量。

- CREATE DATA dref TYPE HANDLE type_xxx.

- CREATE DATA dref TYPE ('I').

  ```ABAP
    TYPES: ty_i TYPE i.
    DATA: dref TYPE REF TO ty_i .
    CREATE DATA dref TYPE ('I'). 
    dref->* = 1.
    WRITE: / dref->*.
  ```

根据类名动态创建实例对象

- CREATE OBJECT oref TYPE ('C1').

#### 动态创建结构、内表

```ABAP
DATA: dref_i TYPE REF TO data,
      dref_c TYPE REF TO data,
      dref_str TYPE REF TO data,
      dref_tab TYPE REF TO data.
DATA: elem_type TYPE REF TO cl_abap_elemdescr,
      struct_type TYPE REF TO cl_abap_structdescr,
      table_type TYPE REF TO cl_abap_tabledescr,
      comp_tab TYPE abap_component_tab WITH HEADER LINE.
FIELD-SYMBOLS :<fs_itab> TYPE ANY TABLE.
FIELD-SYMBOLS :<fs_line> TYPE ANY.
"=====动态的创建基本类型数据====="
elem_type ?= cl_abap_elemdescr=>get_i( 10 ).
"动态的创建基本类型数据对象"
CREATE DATA dref_i TYPE HANDLE elem_type.
"=====动态创建结构类型====="
struct_type ?= cl_abap_typedescr=>describe_by_name( 'SFLIGHT' ).
comp_tab[] = struct_type->get_components( ). "组成结构体的各个字段组件"
"向结构中动态的新增一个成员"
comp_tab-name = 'L_COUNT'. "为结构新增一个成员"
comp_tab-type = elem_type. "新增成员的类型对象"
INSERT comp_tab INTO comp_tab INDEX 1.
"使用结构类型对象来创建结构对象"
struct_type = cl_abap_structdescr=>create( comp_tab[] ).
CREATE DATA dref_str TYPE HANDLE struct_type.
"=====动态创建内表====="
"基于结构类型对象创建内表类型对象"
itab_type = cl_abap_tabledescr=>create( struct_type ).
"使用内表类型对象来创建内表类型对象"
CREATE DATA dref_tab TYPE HANDLE itab_type.
ASSIGN dref_tab->* TO <fs_itab>."将字段符号指向新创建出来的内表对象"
"=====给现有的内表动态的加一列====="
table_type  ?= cl_abap_tabledescr=>describe_by_data( itab ).
struct_type ?= table_type->get_table_line_type( ).
comp_tab[] = struct_type->get_components( ).
comp_tab-name = 'FIDESC'.
comp_tab-type = cl_abap_elemdescr=>get_c( 120 ).
INSERT comp_tab INTO comp_tab INDEX 2.
struct_type = cl_abap_structdescr=>create( comp_tab[] ).
itab_type = cl_abap_tabledescr=>create( struct_type ).
CREATE DATA dref_tab TYPE HANDLE itab_type.
```

