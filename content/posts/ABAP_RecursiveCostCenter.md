---
title: "递归遍历成本中心组"
date: 2019-08-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

#### 成本中心组下嵌套成本中心组

​	成本中心后台数据表是 CSKS，描述表是 CSKT。在表 CSKS 中，主键是 MANDT（客户端）、KOKRS（控制范围）、KOSTL（成本中心）、DATBI（有效截至日期），在一个控制范围下的某个成本中心，会因为时间段的不同，会在表 CSKS 和 CSKT 存储多条记录。

​	![成本中心组](/images/ABAP/utils17.jpg)

​	这是一个树型结构，针对根节点、子节点、叶节点，SAP 有三张表：SETHEADER、SETNODE、SETLEAF。既然是树型结构那用递归遍历是最合适不过了。

```JS
*&--------------------------------------------------------------------*
*&      Form  frm_get_kostl
*&--------------------------------------------------------------------*
*       递归查询一个成本中心组下的所有成本中心。
*---------------------------------------------------------------------*
*      -->I_TREE     成本中心组根节点
*---------------------------------------------------------------------*
FORM FRM_GET_KOSTL USING I_TREE LIKE I_TREE.
  DATA: I_TEMPTREE LIKE I_TREE OCCURS 0 WITH HEADER LINE.
  SELECT SUBSETNAME
    INTO I_TEMPTREE
    FROM SETNODE
    WHERE SETCLASS = '0101'
    AND SUBCLASS = 'YZJT'
    AND SETNAME = I_TREE-SETNAME.
   IF SY-SUBRC = 0.
    PERFORM FRM_GET_KOSTL USING I_TEMPTREE.
   ENDIF.
  ENDSELECT.
  SELECT VALFROM FROM SETLEAF INTO F_KOSTL
    WHERE SETCLASS = '0101'
    AND SUBCLASS = 'YZJT'
    AND SETNAME = I_TREE-SETNAME.
   APPEND F_KOSTL .
  ENDSELECT.
ENDFORM.                    "frm_get_kostl
```

调用：

```JS
"数据结构定义
DATA: BEGIN OF I_TREE OCCURS 0,
      SETNAME LIKE SETNODE-SETNAME,
	  END OF I_TREE.
DATA: BEGIN OF I_KOSTL OCCURS 0,
      VALFROM LIKE SETLEAF-VALFROM,
      END OF I_KOSTL.
DATA: I_SETHEADER LIKE I_TREE OCCURS 0 WITH HEADER LINE. 

PERFORM FRM_GET_KOSTL USING I_SETHEADER.
```



针对 SAP 中 SET（集）的操作，有好多函数，如 G_SET_GET_ALL_VALUES：Read All Values in a Set Hierarchy