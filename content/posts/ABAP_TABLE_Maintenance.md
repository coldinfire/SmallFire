---
title: " SM30 表维护 "
date: 2018-10-09 
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  ABAP

tags: 
  - abaputils

---

### 表维护创建

#### 创建自定义表 

![自定义表创建](/images/ABAP/SM301.png)

需要设置为 Display/Maintenance Allowed.允许修改，才可以在SM30中进行数据维护。否则会产生以下异常：

![异常消息](/images/ABAP/SM3011.png)

#### 创建表维护

![创建表维护](/images/ABAP/SM302.png)

![设置](/images/ABAP/SM303.png)

权限组：控制访问权限

函数组：放在自定义的函数组即可

Maintenance type:

- one step：每次维护数据的时候不会产生请求号
- two step：每次维护数据的时候会产生请求号

Recording rountine:

- Standard recording routine
- no,or user,recording routine：在其他Client上有权维护此表数据

![屏幕号生成](/images/ABAP/SM304.png)

当保存完成后即可通过TCode：SM30，通过对应的表名就可以对已建立表维护的表进行数据的维护。

#### 修改 Table Control 显示控制

双击 Overview Screen 进入 Screen Painter，修改 Attributes 显示界面的大小。

![修改屏幕属性](/images/ABAP/SM3013.png)

然后进行字段修改设置为必输或则只显示，修改列描述，字段显示长度调整。

![修改屏幕属性](/images/ABAP/SM3014.png)

#### 添加自定义 Module

可以根据输入值获取并设置相关字段的值； 设置维护日期 ，维护人赋值等操作

![自定义MODULE](/images/ABAP/sm3015.png)

![设置和维护值](/images/ABAP/sm3016.png)

### 分配 TCode

通常建立的自定义表不允许用户直接查看，有时可能需要人工维护某些数据。这时候，可以通过SM30建立表维护的方式开放给用户，但是SM30的权限比较大不适宜直接分配；因此需要给对应的表维护另外分配TCode，来控制其权限。

#### TCode 创建并分配

通过 SE93 创建 TCode 并选择 Transaction with parameters(parameter transaction)。

![TCode分配](/images/ABAP/SM308.png)

创建界面需要配置 Default values 和一些默认值：

- UPDATE = X：是否可以对数据进行维护
- VIEWNAME = 需要维护的表名

![TCode分配](/images/ABAP/SM305.png)

#### 查找 SM30 表维护对应的 Tcode

通过 SE16/SE16N 输入表 TSTCP，然后在 Parameters 中输入 Tablename，点击执行，将查找出表维护对应的 Tcode。

#### 对维护表进行增强操作

可以通过增强的方式对维护表中的已知字段进行操作，比如每次修改后自动记录修改时间和修改人信息。

![查找源代码](/images/ABAP/SM306.png)

![Program](/images/ABAP/SM307.png)

![Source code](/images/ABAP/SM309.png)

#### 事件定义

![event](/images/ABAP/SM3010.png)

- 如果出现错误信息：function group XXXX cannot be processed,检查Function group并完全激活。

![event](/images/ABAP/SM3012.png)

- 01：数据库保存数据前
- 02：数据库保存数据后
- 03：删除已显示的数据之前
- 04：删除数据显示之后
- 05：建立新的条目.....

FORM Routine 定义

```ABAP
*----------------------------------------------------------------------*
***INCLUDE 
*----------------------------------------------------------------------*
FORM test.
  DATA: lv_msg TYPE string.
  DATA: lt_data TYPE TABLE z_table,
        ls_data TYPE z_table.
  LOOP AT total.
    IF <action> NE 'D' AND <action> NE 'X'. "U:Update,D:Delete"
      APPEND <vim_total_struc> TO lt_data.
    ENDIF.
  ENDLOOP.
  IF lt_data IS NOT INITIAL.
    "Sucess"
    vim_abort_saving = abap_false.
    sy-subrc = 0.
  ELSE.
    "Error Msg"
    lv_msg = 'Error Message.'.
    MESSAGE lv_msg TYPE 'S' DISPLAY 'E'. 
    vim_abort_saving = abap_true.
    sy-subrc = 4.
  ENDIF.
  IF sy-subrc <> 0.
    MESSAGE 'XXXX' TYPE 'X'.
  ENDIF.
ENDFORM.
```

-  [TOTAL 及 action 等字段解析](http://remote-database.com/00000271/91ca9fb9a9d111d1a5690000e82deaaa.html)