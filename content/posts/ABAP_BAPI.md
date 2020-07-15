---
title: "BAPI使用"
date: 2019-06-16
draft: false
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BAPI

---



1. BAPI：Business Application Process Interface(业务应用编辑接口)，它实质上就是一种特殊的RFC函数。
   - BAPI函数及函数参数参考的结构类型名，都要以ZBAPI开头；
   - BAPI函数参数只能是传值，不能有Change与Exception参数
   - BAPI函数需要有Return返回参数

2. 查找事务码对应的BAPI
   
- 进入事务码，system status --> 事务码双击，找到Package VA --> SE80打开包VA/Display Object List
  
3. BAPI事务处理

   - 同时调用多个BAPI，需要在程序中进行事务控制，决定何时执行数据库提交或回滚；

   - BAPI内部不包含COMMIT WORK,ROLLBACK WORK

   - 根据BAPI函数的返回数据进行判断：

     ```JS
     IF return-type <> 'S'.
       CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
     ELSE.
       CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
     EXPORTING
       wait = 'X'.
     ENDIF.
     ```

4.多个BAPI调用时需要遵守以下原则

- <1>如果有更新操作的BAPI,对实例进行另外的读取操作的BAPI只能访问上一个COMMIT WORK 执行后的最新数据
- <2>在同一个LUW中，不能对同一个业务对象实例时行超过一次的更新操作。

