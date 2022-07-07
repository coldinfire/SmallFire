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
  - abapbasis

---

### BAPI 简介

BAPI：Business Application Process Interface(业务应用编辑接口)，是标准化的编程接口（方法），使外部应用程序能够访问 R/3 系统中的业务流程和数据。提供稳定和标准化的方法来实现 R/3 系统与外部应用程序、遗留系统和附加组件之间的无缝集成。

BAPI 在 BOR（业务对象存储库）中定义为执行特定业务功能的 SAP 业务对象类型的方法。它们作为支持 RFC 的功能模块实现，并在 ABAP 工作台的功能构建器中创建。

- BAPI 函数及函数参数参考的结构类型名，都要以 ZBAPI 开头
- BAPI 函数参数只能是传值，不能有 Change 与 Exception 参数
- BAPI 函数需要有 Return 返回参数

往往调用一个 BAPI 的时候不知道哪里赋值，怎么赋值，赋什么值。

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

同时调用多个 BAPI，需要在程序中进行事务控制，决定何时执行数据库提交或回滚。BAPI 内部不包含 COMMIT WORK、ROLLBACK WORK；需要根据 BAPI 函数的返回数据进行判断：

```ABAP
IF return-type <> 'S'.
  CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
ELSE.
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
    EXPORTING
      wait = 'X'.
ENDIF.
```

BAPI 和函数返回消息通常是包括程序名、日期、时间、操作人、消息号等。

其中，BAPI 不管运行成功或者失败都会返回消息，而系统标准函数则如果运行失败返回的消息是空、只有出问题的时候才是非空，这一点需要特别注意。

此外，CALL BAPI 之后，即便返回的消息是成功的，然而真正 commit 之后并不一定会成功。例如拣配的 bapi，我们判断拣配是否成功其实应该查询 VBUK 表来判断拣配状态，而不是看 BAPI 的返回消息中是否含有 E 类型。如果查询 VBUK 发现对应的状态不是 C，则表明 BAPI 实际上没有执行成功、必须要 ROLLBACK，否则后续的操作会有问题。

### 多个BAPI调用时需要遵守以下原则

1、如果有更新操作的BAPI,对实例进行另外的读取操作的BAPI只能访问上一个COMMIT WORK 执行后的最新数据

2、在同一个LUW中，不能对同一个业务对象实例时行超过一次的更新操作。

