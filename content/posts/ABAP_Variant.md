---
title: "STVARV使用详情"
date: 2019-02-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
---

### STVARV使用

​	该配置不能跨Client因此需要在每个Client单独配置；使用TCode：stvarv进入配置界面，可以新建、修改、删除记录。![Maintain](/images/ABAP/Stvarv.png)

#### 程序内使用

​	程序内只是取出配置的值，该值存储在表TVARVC中。如果有前导零问题，需要调用Function添加前导零。

```JS
DATA: LT_TVARVC TYPE TABLE OF TVARVC,
      LS_TVARVC TYPE TVARVC.
"创建Ranges"
DATA: LT_XXXX TYPE RANGE OF field,
      LS_XXXX LIKE LINE OF LT_XXXX.
"Parameter类型数据"
SELECT SINGLE * INTO CORRESPONDING FIELDS OF LS_TVARVC
       FROM TVARVC
       WHERE NAME = 'Parameter_key'
       AND TYPE = 'P'.
IF SY-SUBRC = 0 .
  value = LS_TVARVC-LOW.
ENDIF.
"Selection Options类型数据"
SELECT * INTO CORRESPONDING FIELDS OF TABLE LT_TVARVC
	FROM TVARVC
    WHERE NAME = 'Selection_key'
    AND TYPE = 'S'.
IF SY-SUBRC = 0.
  LOOP AT LT_TVARVC INTO LS_TVARVC.
    LS_XXXX-SIGN = LS_TVARVC-SIGN.
    LS_XXXX-OPTION = LS_TVARVC-OPTI.
  	LS_XXXX-LOW  = LS_TVARVC-LOW.
    LS_XXXX-HIGH = LS_TVARVC-HIGH.
    APPEND LS_XXXX TO LT_XXXX.
  ENDLOOP.
ENDIF.
```
