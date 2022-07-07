---
title: " BAPI 操作ECN数据 "
date: 2021-07-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
  - BAPI
 
---

API modules for ECM： Function Group -- CCAP 

### Create Change Master Record

#### BAPI：CCAP_ECN_HEADER_CREATE / CCAP_ECN_CREATE

使用 BAPI 创建更改主记录的表头数据：status、authorization group、valid-from date、description、reason for change。还可以定义更改编号对其有效的对象类型。

- 必输字段：ECN、Status、Valid-from Date、Description。


如果多次调用此函数，则可以使用参数 `FL_COMMIT_AND_WAIT` 来确保每次更改都保存到数据库中，然后才能进行下一次更改。

#### 参数列表

| Parameter                | Description                                    |
| :----------------------- | :--------------------------------------------- |
| CHANGE_HEADER            | 表头数据信息                                   |
| Object Type : OBJECT_MAT | 设定有效的对象类型：Material、BOM、Task list等 |
| FL_COMMIT_AND_WAIT       | 多次调用保证每次都保存到数据库                 |
| FL_NO_COMMIT_WORK        | 不提交事务，可以用来做测试                     |
| CHANGE_NO                | BAPI 返回创建的 ECN 号码                       |
| T_TEXT                   | Long text table                                |

#### 程序实例

```ABAP
DATA:L_CHANGENO LIKE AENRB-AENNR,
  LS_HEADER LIKE AENR_API01,
  LS_OBJECT_MAT LIKE AENV_API01,
  LS_OBJECT_BOM LIKE AENV_API01,
  LS_OBJECT_TLIST LIKE AENV_API01,
  LT_ITEM TYPE TABLE OF AEOI_API01 WITH HEADER LINE.
DATA: LT_RETURN LIKE BAPIRET2 OCCURS 0 WITH HEADER LINE.
DATA: LV_RESULT TYPE BAPI_MTYPE,
      LV_MESSAGE TYPE BAPI_MSG.
"CHANGE_HEADER"
LS_HEADER-CHANGE_NO = AENR-AENNR
LS_HEADER-STATUS = '01'.
LS_HEADER-VALID_FROM = SY-DATUM. "Valid From Must >= Today"
LS_HEADER-DESCRIPT = 'Demo'.
LS_HEADER-REASON_CHG = 'For Test'.
"物件类型"
LS_OBJECT_MAT-ACTIVE = 'X'.
LS_OBJECT_MAT-OBJ_REQU = 'X'.
LS_OBJECT_MAT-MGTREC_GEN = 'X'.
"BOM类型"
LS_OBJECT_BOM-ACTIVE = 'X'.
LS_OBJECT_BOM-OBJ_REQU = 'X'.
LS_OBJECT_BOM-MGTREC_GEN = 'X'.
"Task List类型"
LS_OBJECT_TLIST-ACTIVE = 'X'.
LS_OBJECT_TLIST-OBJ_REQU = 'X'.
LS_OBJECT_TLIST-MGTREC_GEN = 'X'.
"CALL BAPI"
CALL FUNCTION 'CCAP_ECN_CREATE'
  EXPORTING
    CHANGE_HEADER            = LS_HEADER
    OBJECT_MAT               = LS_OBJECT_MAT
    OBJECT_BOM               = LS_OBJECT_BOM
    OBJECT_TLIST             = LS_OBJECT_TLIST
  IMPORTING
    CHANGE_NO                = L_CHANGENO
  TABLES
*   ALT_DATES                =
    OBJMGREC                 = LT_ITEM
*   EFFECTIVITY              =
*   TEXTHEADER               =
*   TEXTLINES                =
  EXCEPTIONS
    CHANGE_NO_ALREADY_EXISTS = 1
    ERROR                    = 2
    OTHERS                   = 3.
IF SY-SUBRC <> 0.
  LV_RESULT = 'E'.
  CALL FUNCTION 'BALW_BAPIRETURN_GET2'
    EXPORTING
      TYPE   = SY-MSGTY
      CL     = SY-MSGID
      NUMBER = SY-MSGNO
      PAR1   = SY-MSGV1
      PAR2   = SY-MSGV2
      PAR3   = SY-MSGV3
      PAR4   = SY-MSGV4
    IMPORTING
      RETURN = LT_RETURN.
  APPEND LT_RETURN.
  LOOP AT LT_RETURN WHERE TYPE = 'E'.
    CONCATENATE LT_RETURN-MESSAGE ';' INTO LV_MESSAGE.
  ENDLOOP.
ELSE.
  LV_RESULT = 'S'.
  LV_MESSAGE = 'ECN change successfully'.
ENDIF.
```

### Change Change Master Record

#### BAPI：CCAP_ECN_HEADER_CHANGE

该 BAPI 用于更改 ECN 的标题数据和对象类型。

该 BAPI 支持两种初始化字段的过程。 在 CALO_INIT_API 模块的 DATA_RESET 字段中选择过程：

- 如果 DATA_RESET 字段与其初始值一起传输，则所有字段，包括包含其初始值的字段，都将从接口复制。 如果传输的字段包含其初始值，则会初始化更改主记录中的相关字段。
- 如果 DATA_RESET 字段被传送一个值，或者如果默认值不被覆盖，具有初始值的字段不会从界面复制。 要初始化字段，必须在接口字段的开头输入 DATA_RESET 字段中定义的字符。

#### 参数列表

| Parameter          | Description                                    |
| ------------------ | ---------------------------------------------- |
| CHANGE_HEADER      | 表头数据信息                                   |
| Object type        | 设定有效的对象类型：Material、BOM、Task list等 |
| FL_COMMIT_AND_WAIT | 多次调用保证每次都保存到数据库                 |
| T_TEXT             | Long text table                                |

#### 程序实例

```ABAP
DATA:L_CHANGENO LIKE AENRB-AENNR,
  LS_HEADER LIKE AENR_API01,
  LS_OBJECT_MAT LIKE AENV_API01,
  LS_OBJECT_BOM LIKE AENV_API01,
  LS_OBJECT_TLIST LIKE AENV_API01,
  LT_TEXT TYPE TABLE OF TLINE WITH HEADER LINE.
DATA: LT_RETURN LIKE BAPIRET2 OCCURS 0 WITH HEADER LINE.
"CHANGE_HEADER"
LS_HEADER-CHANGE_NO = AENR-AENNR
LS_HEADER-STATUS = '01'.
LS_HEADER-VALID_FROM = SY-DATUM. "Valid From Must >= Today"
LS_HEADER-DESCRIPT = 'Demo'.
LS_HEADER-REASON_CHG = 'For Test'.
"物件类型"
LS_OBJECT_MAT-ACTIVE = 'X'.
LS_OBJECT_MAT-OBJ_REQU = 'X'.
LS_OBJECT_MAT-MGTREC_GEN = 'X'.
CALL FUNCTION 'CCAP_ECN_HEADER_CHANGE'
  EXPORTING
    CHANGE_HEADER        = LS_HEADER
    OBJECT_MAT           = LS_OBJECT_MAT
    FL_COMMIT_AND_WAIT   = 'X'
  TABLES
    T_TEXT               = LT_TEXT
  EXCEPTIONS
    ERROR                = 1
    OTHERS               = 2.
IF SY-SUBRC <> 0.
  LV_RESULT = 'E'.
  CALL FUNCTION 'BALW_BAPIRETURN_GET2'
    EXPORTING
      TYPE   = SY-MSGTY
      CL     = SY-MSGID
      NUMBER = SY-MSGNO
      PAR1   = SY-MSGV1
      PAR2   = SY-MSGV2
      PAR3   = SY-MSGV3
      PAR4   = SY-MSGV4
    IMPORTING
      RETURN = LT_RETURN.
  APPEND LT_RETURN.
  LOOP AT LT_RETURN WHERE TYPE = 'E'.
    CONCATENATE LT_RETURN-MESSAGE ';' INTO LV_MESSAGE.
  ENDLOOP.
ELSE.
  LV_RESULT = 'S'.
  LV_MESSAGE = 'ECN change successfully'.
ENDIF.
```

### Delete Change Master Record

#### BAPI：CCAP_ECN_HEADER_DELETE

该 BAPI 用于删除变更主记录， 输入更改编号作为导入参数。

```ABAP
CALL FUNCTION 'CCAP_ECN_HEADER_DELETE'
  EXPORTING
    CHANGE_NO          = aenr-aennr
    FL_COMMIT_AND_WAIT = 'X'.
```

  