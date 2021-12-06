---
title: " BAPI 使用 "
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

BAPI：Business Application Process Interface(业务应用编辑接口)，是标准化的编程接口（方法），使外部应用程序能够访问 R/3 系统中的业务流程和数据。提供稳定和标准化的方法来实现 R/3 系统与外部应用程序、遗留系统和附加组件之间的无缝集成。

BAPI 在 BOR（业务对象存储库）中定义为执行特定业务功能的 SAP 业务对象类型的方法。它们作为支持 RFC 的功能模块实现，并在 ABAP 工作台的功能构建器中创建。

- BAPI 函数及函数参数参考的结构类型名，都要以 ZBAPI 开头
- BAPI 函数参数只能是传值，不能有 Change 与 Exception 参数
- BAPI 函数需要有 Return 返回参数

往往调用一个 BAPI 的时候不知道哪里赋值，怎么赋值，赋什么值。

### 查找Tcode对应的BAPI

1、SE93 根据Tcode查找对应的Package

2、SE80 展开 PACKAGE 下的Business Engineering -> Business Object Types，根据业务需求选择对应的Item

![SE80](/images/SAPUtils/SAP_BAPI_1.png)

3、双击打开选择的Business Object，展开Methods根据描述选择需要的功能双击该方法，在弹出的屏幕中，选择 ABAP TAB 页，Name 中显示的既是对应的BAPI

![SE80](/images/SAPUtils/SAP_BAPI_2.png)

页签下面勾选的 **API Function**，说明找到的 BAPI 就是创建 PO 的，如果选的是 **Function module**，说明是通过函数实现的。

### 创建 BAPI

Step1：使用事物码 SWO1；Tools->Business Framework -> BAPI Development ->Business Object builder  根据为其创建 BAPI 的功能需求选择业务对象。

Step2：以更改模式打开业务对象；然后选择 Utilities ->API Methods ->Add method。然后输入功能模块的名称，选择Continue。

Step3：在下一个对话框中，需要指定以下信息

- 方法：为方法建议一个合适的名称
- 文本：输入 BAPI 的描述
- 单选按钮：对话、同步、独立于实例。 BAPI 通常是同步实现的。

Step4：要创建方法，请在下一个对话框中选择 Yes。

Step5：程序生成并执行后，在刚刚创建的方法中检查程序，这样就创建了一个BAPI。

### BAPI事务处理

同时调用多个BAPI，需要在程序中进行事务控制，决定何时执行数据库提交或回滚

BAPI内部不包含COMMIT WORK,ROLLBACK WORK

根据BAPI函数的返回数据进行判断：

```ABAP
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

