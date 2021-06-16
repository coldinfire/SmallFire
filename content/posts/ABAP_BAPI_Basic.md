---
title: "BAPI使用"
date: 2019-06-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BAPI

---

### BAPI 简介

BAPI：Business Application Process Interface(业务应用编辑接口)，它实质上就是一种特殊的RFC函数。
- BAPI函数及函数参数参考的结构类型名，都要以ZBAPI开头
- BAPI函数参数只能是传值，不能有Change与Exception参数
- BAPI函数需要有Return返回参数

### 查找Tcode对应的BAPI

1、SE93 根据Tcode查找对应的Package

2、SE80 展开 PACKAGE 下的Business Engineering -> Business Object Types，根据业务需求选择对应的Item

![SE80](/images/SAPUtils/SAP_BAPI_1.png)

3、双击打开选择的Business Object，展开Methods根据描述选择需要的功能双击该方法，在弹出的屏幕中，选择 ABAP TAB 页，Name 中显示的既是对应的BAPI

![SE80](/images/SAPUtils/SAP_BAPI_2.png)

页签下面勾选的 **API Function**，说明找到的 BAPI 就是创建 PO 的，如果选的是 **Function module**，说明是通过函数实现的。

### BAPI事务处理

同时调用多个BAPI，需要在程序中进行事务控制，决定何时执行数据库提交或回滚

BAPI内部不包含COMMIT WORK,ROLLBACK WORK

根据BAPI函数的返回数据进行判断：

```JS
IF return-type <> 'S'.
  CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
ELSE.
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
    EXPORTING
      wait = 'X'.
ENDIF.
```

### 多个BAPI调用时需要遵守以下原则

1、如果有更新操作的BAPI,对实例进行另外的读取操作的BAPI只能访问上一个COMMIT WORK 执行后的最新数据

2、在同一个LUW中，不能对同一个业务对象实例时行超过一次的更新操作。

