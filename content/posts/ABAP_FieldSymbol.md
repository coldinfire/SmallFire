---
title: " SAP Field Symbols "
date: 2018-09-29
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 字段符号

#### 定义

- `FIELD-SYMBOLS: <fs_tb> TYPE ANY TABLE.`
- `FIELD-SYMBOLS: <fs_wa> TYPE ANY.`
- `FIELD-SYMBOLS: <fs_struc> LIKE LINE OF <table>.`
- `FIELD-SYMBOLS: <fs_struc> LIKE <structure>.`

#### 使用

FIELD-SYMBOLS 必须和某个变量，结构或者内表绑定后才能使用，在使用字段符号前必须分配给某个变量，不然会发生 FIELD-SYMBOLS 未分配的运行时错误。如果 LOOP 内表时 ASSIGNING 到FIELD-SYMBOLS，之后假如有 REFRESH 内表的操作的话，FIELD-SYMBOLS 也会再次回到初始未被  ASSIGNING 的状态，这时如果使用 FIELD-SYMBOLS 也会发生字段符号未分配的 RUNTIME ERROR。

`ASSIGN <value> TO <fs>.`

- 将某个内存区域分配给字段符号，这样字段符号就代表了该内存区域，即该内存区域别名字段符号可以看作仅是已经被解引用的指针，即某个变量的别名。

`UNASSIGN <fs>.`

- 初始化`<fs>`字段符号，执行后字段符号将不再引用内存区域，`<fs> IS ASSIGNED.`返回 False。

`CLEAR <fs>.`

- 与 UNASSIGN 不同的是，它只有一个作用就是初始化它所指向的内存区域，而不是解除 ASSIGN。

*循环中使用字段符号* 

- LOOP 内表 INTO 结构时，系统会把先把当前行的数据复制到结构，如果结构的值改了，还需要使用 MODIFY 语句把更改后的值传回内表。也就是说，结构是内表里的数据的一个副本，操作这个副本不会影响内表里的数据。为了提高效率，可以使用字段符号，字段符号直接指向内表数据，省去了复制数据到结构的过程。修改字段符号的值也就是相当于直接修改内表里的数据，不需要再使用 MODIFY 语句。


*读取表数据时使用字段符号*

- 将内表数据的行记录分配到字段符号，修改字段符号的值也就是相当于直接修改内表里的数据。

### 字段符号使用实例

#### APPEND & INSERT

```ABAP
FIELD-SYMBOLS <fs_mara> TYPE ANY.
"Append line"
APPEND INITIAL LINE TO t_mara ASSIGNING <fs_mara>.
<fs_mara>-matnr = 'sapclub'.
"Insert table"
INSERT INITIAL LINE INTO t_mara ASSIGNING <fs_mara> INDEX 2.
<fs_mara>-matnr = 'ABCDEF'.
```

#### LOOP MODIFY & READ MODIFY

```ABAP
"Loop table"
LOOP AT t_mara ASSIGNING <fs_mara>.
  <fs_mara>-ersda = sy-datum + 1.
ENDLOOP.
"Read table"
READ TABLE t_mara ASSIGNING <fs_mara> WITH KEY matnr = 'XXX'.
IF sy-subrc EQ 0.
  <fs_mara>-ersda = sy-datum.
ENDIF.
```

#### 直接复制内表数据

```ABAP
ASSIGN lt_1 TO <fs1>
ASSIGN lt_2 TO <fs2>.
...
<lt_2> = <lt_1>.
DATA: lv_lines TYPE i.
lv_lines = lines( lt_2 ).
WRITE: lv_lines.
```

#### 循环单条复制内表数据

```ABAP
LOOP AT lt_1 ASSIGNING <fs_1>.
  APPEND INITIAL LINE TO lt_2 ASSIGNING <ls_2>.
  <ls_2> = <fs_1>.
ENDLOOP.
```

#### 通过结构中字段名称获取内表数据

```ABAP
DATA field_name type char20.
FIELD-SYMBOLS:<lv_field> type any.
LOOP AT lt_1 ASSIGNING <fs>.
  ASSIGN COMPONENT <field_name> OF STRUCTURE <fs> TO <lv_field>.
  CHECK <lv_field> IS ASSIGNED.
ENDLOOP.
```

#### 通过结构中字段索引获取内表数据

```ABAP
DATA index_number type i.
FIELD-SYMBOLS:<lv_field> type any.
LOOP AT lt_1 ASSIGNING <fs>.
  ASSIGN COMPONENT index_number OF STRUCTURE <fs> TO <lv_field>.
  CHECK <lv_field> IS ASSIGNED.
ENDLOOP.
```

#### ASSIGN 隐式强转

```ABAP
TYPES: BEGIN OF t_date,
  year(4) TYPE  n,
  month(2) TYPE n,
  day(2) TYPE n,
END OF t_date.
FIELD-SYMBOLS <fs> TYPE t_date.  "将<fs>定义成了具体限定类型"
ASSIGN sy-datum TO <fs> CASTING. "后面没有指定具体类型，所以使用定义时的类型进行隐式转换"
```

#### ASSIGN 显示强转

```ABAP
DATA txt(8) TYPE c VALUE '19980606'.
FIELD-SYMBOLS <fs>.
ASSIGN txt TO <fs> CASTING TYPE d. "由于定义时未指定具体的类型，所以这里需要显示强转"
```

#### 动态引用

通过循环赋值给定义的字段符号，对其进行修改，等于直接修改原内表。

```ABAP
FIELD-SYMBOLS <l_shortageqty> type mng01.
LOOP AT <dyn_table> ASSIGNING <dyn_wa>.
  ASSIGN COMPONENT 'SHORTAGEQTY' of STRUCTURE <dyn_wa> to <l_shortageqty>.
  <l_shortageqty> = <l_shortageqty> - 20.
ENDLOOP.
```







