---
title: " Field Symbols "
date: 2018-09-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---



### 字段符号

#### 定义:

- `FIELD-SYMBOLS: <fs> TYPE ANY TABLE.`
- `FIELD-SYMBOLS: <fs> LIKE LINE OF <t_TABLE>`
- `FIELD-SYMBOLS: <fs> type any.`

​    <1> FS必须和某个变量，结构或者内表绑定后才能使用，在使用FS前必须分配给某个变量，不然会发生FS未分配的运行时错误。如果LOOP内表时ASSIGNING到FS，之后假如有REFRESH内表的操作的话，FS也会再次回到初始未被ASSIGN的状态，这时如果使用FS也会发生FS未分配的RUNTIME ERROR。

​	<2> `ASSIGN structure TO <fs>`:将某个内存区域分配给字段符号，这样字段符号就代表了该内存区域，即该内存区域别名字段符号可以看作仅是已经被解引用的指针，即某个变量的别名。
  `ASSIGN <Val> TO <fs>`: 将某个内存区域分配给字段符号，这样字段符号就代表了该内存区域。

**UNASSIGN:**  该语句是初始化`<FS>`字段符号，执行后字段符号将不再引用内存区域，`<fs> is assigned`返回假。

**CLEAR:** 与UNASSIGN不同的是，只有一个作用就是初始化它所指向的内存区域，而不是解除分配。

**循环：** LOOP内表INTO Structure和 LOOP内表ASSIGNING Structure的比较

​    LOOP内表INTO结构时，系统会把先把当前行的数据复制到结构，如果结构的值改了，还需要使用
MODIFY语句把更改后的值传回内表。也就是说，结构是内表里的数据的一个副本，操作这个副本不会影响内表里的数据。为了提高效率，可以使用FS，FS直接指向内表数据，省去了复制数据到结构的过程。修改FS的值也就是相当于直接修改内表里的数据，不需要再使用MODIFY语句。

**读表：** 将内表数据的行记录分配到FS，修改FS的值也就是相当于直接修改内表里的数据。

#### 使用：

- APPEND & INSERT

  ```JS
  "Append line"
  APPEND INITIAL LINE TO t_mara ASSIGNING <fs_mara>.
  <fs_mara>-matnr = 'sapclub'.
  "Insert table"
  INSERT INITIAL LINE INTO t_mara ASSIGNING <fs_mara> INDEX 2.
  <fs_mara>-matnr = 'ABCDEF'.
  ```
  
- LOOP & READ

  ```JS
  "Read table"
  READ TABLE t_mara ASSIGNING <fs_mara> WITH KEY matnr = 'sapclub'.
  IF sy-subrc EQ 0.
    WRITE:/ <fs_mara>-matnr.
  ENDIF.
  "Loop"
  LOOP AT t_mara ASSIGNING <fs_mara>.
    WRITE:/ <fs_mara>-matnr.
  ENDLOOP.
  ```
  
- MODIFY

  ```JS
  "READ and MODIFY"
  READ TABLE t_mara ASSIGNING <fs_mara> WITH KEY matnr = 'matnr'
  IF sy-subrc EQ 0.
    <fs_mara>-ersda = sy-datum.
  ENDIF.
  "LOOP and MODIFY"
  LOOP AT t_mara ASSIGNING <fs_mara>.
    <fs_mara>-ersda = sy-datum + 1.
  ENDLOOP.
  ```
  
- 直接复制内表数据

  ```JS
  ASSIGN lt_1 TO <fs1>
  ASSIGN lt_2 TO <fs2>.
  ...
  <lt_2> = <lt_1>.
  DATA: lv_lines TYPE i.
  lv_lines = lines( lt_2 ).
  WRITE: lv_lines.
  ```

- 循环单条复制内表数据

  ```JS
  LOOP AT lt_1 ASSIGNING <fs1>.
    APPEND INITIAL LINE TO lt_2 ASSIGNING <ls_2>.
    <ls_2> = <ls_1>.
  ENDLOOP.
  ```

- 通过结构中字段名称获取内表数据

  ```JS
  DATA:field_name type char20.
  FIELD-SYMBOLS:<lv_field> type any.
  LOOP AT lt ASSIGNING <fs>.
    ASSIGN COMPONENT <field_name> OF STRUCTURE <fs> TO <lv_field>.
    CHECK <lv_field> IS ASSIGNED.
  ENDLOOP.
  ```

- 通过结构中字段索引获取内表数据

  ```JS
  DATA:index_number type i.
  FIELD-SYMBOLS:<lv_field> type any.
  LOOP AT lt ASSIGNING <fs>.
    ASSIGN COMPONENT index_number OF STRUCTURE <fs> TO <lv_field>.
    CHECK <lv_field> IS ASSIGNED.
  ENDLOOP.
  ```

ASSIGN隐式强转

```JS
TYPES: BEGIN OF t_date,
    year(4) TYPE  n,
    month(2) TYPE n,
    day(2) TYPE n,
  END OF t_date.
FIELD-SYMBOLS <fs> TYPE t_date.  "将<fs>定义成了具体限定类型"
ASSIGN sy-datum TO <fs> CASTING. "后面没有指定具体类型，所以使用定义时的类型进行隐式转换"
```

ASSIGN显示强转

```JS
  DATA txt(8) TYPE c VALUE '19980606'.
  FIELD-SYMBOLS <fs>.
  ASSIGN txt TO <fs> CASTING TYPE d."由于定义时未指定具体的类型，所以这里需要显示强转"
```

**动态引用：**通过循环赋值给定义的字段符号，对其进行修改，等于直接修改原内表。

```JS
field-symbols:<l_shortageqty> type mng01.
loop at <dyn_table> assigning <dyn_wa>.
  assign component 'SHORTAGEQTY' of structure <dyn_wa> to <l_shortageqty>.
  <l_shortageqty> = <l_shortageqty> - <l_fvalue>.
endloop.
```



