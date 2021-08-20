---
title: " BAPI:VIEW_MAINTENANCE_CALL 使用 "
date: 2021-04-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### BAPI: VIEW_MAINTENANCE_CALL

```ABAP
DATA:LT_SELLIST LIKE TABLE OF VIMSELLIST WITH HEADER LINE.
CLEAR:LT_SELLIST.
REFRESH: LT_SELLIST.
LT_SELLIST-VIEWFIELD = 'BUKRS'.
LT_SELLIST-OPERATOR = 'EQ'.
LT_SELLIST-VALUE = P_BUKRS.
LT_SELLIST-AND_OR = 'AND'.
APPEND LT_SELLIST.

CALL FUNCTION 'VIEW_MAINTENANCE_CALL'
  EXPORTING
    ACTION            = 'U'"S = Display U = Change T = Transport
    VIEW_NAME          = 'ZCO002'
  TABLES
    DBA_SELLIST         = LT_SELLIST
  EXCEPTIONS
    CLIENT_REFERENCE       = 1
    FOREIGN_LOCK         = 2
    INVALID_ACTION        = 3
    NO_CLIENTINDEPENDENT_AUTH  = 4
    NO_DATABASE_FUNCTION     = 5
    NO_EDITOR_FUNCTION      = 6
    NO_SHOW_AUTH         = 7
    NO_TVDIR_ENTRY        = 8
    NO_UPD_AUTH         = 9
    ONLY_SHOW_ALLOWED      = 10
    SYSTEM_FAILURE        = 11
    UNKNOWN_FIELD_IN_DBA_SELLIST = 12
    VIEW_NOT_FOUND        = 13
    MAINTENANCE_PROHIBITED    = 14.
```

#### DBA_SELLIST 参数

Selection range for view maintenance，如果设置了该参数，则后续打开的 SM30 表维护数据新增数据时，只能新增满足该条件的数据。SAP 会在保存时校验改参数的条件，如果不满足则提示以下错误：

![BAPI Error](/images/ABAP/SM3017.png)



​	