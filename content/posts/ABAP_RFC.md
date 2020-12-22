---
title: " RFC 介绍 "
date: 2018-10-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

 [RFC : SAP Hlep Portal 链接](https://help.sap.com/viewer/753088fc00704d0a80e7fbd6803c8adb/7.5.10/en-US/485bc57cd004501ee10000000a421937.html)

### 什么是 RFC

RFC 是 SAP 系统和其他（SAP 或非 SAP）系统间的一个重要而常用的双向接口技术，也被视为 SAP 与外部通信的基本协议。简单地说，RFC 过程就是系统调用当前系统外的程序模块，从而实现某个功能，而且调用系统和被调用系统中至少有一个必须是 SAP ABAP 系统。这种远程功能调用也可在同一系统内部进行（如本地 SAP 系统内的远程调用）；但通常情况下，调用程序和被调用程序处于不同系统。

### RFC 调用过程

在系统间通信过程中，需区分发送系统和接受系统。RFC 调用请求从发送系统（调用系统）中传至接收系统（被调用系统，也称远程系统或目标系统），发送请求的系统在通信过程中又称为 RFC 客户端，通信另一方则称为 RFC 服务器。RFC 客户端发起远程功能调用以执行 RFC 服务器提供的功能。

其中，调用系统和被调用系统均可以是 SAP 系统和非 SAP 系统，此外还可以在 SAP 系统内部将特定应用服务器指定为目标系统。

### RFC 通信的情况

根据通信方向和系统类型，共有如下三种 RFC 通信：

- 两个独立的 SAP 系统之间的通信

- SAP 系统作为调用系统，与外部远程系统（非 SAP ABAP 系统）通信

- 外部系统作为调用系统，与 SAP 系统通信

### RFC创建步骤

1. 通过T-Code: SE37,进入RFC开发初始界面

2. 创建RFC与Report不同的是，RFC程序需要先定义一个Function Group，一个Group下可包含多个Function。

3. Go to > Function Groups > Create Group

4. 对Function进行属性设置

5. Function编辑器包含有七个页签：
   - Attributes：设置Short text和Processing Type,是否允许外部调用。
   - Import：数据输入接口，接口参数可以为单个变量或则为一个结构体。
   - Export：数据输出接口，接口参数可以为单个变量或一个结构体。
   - Changing：将传入的表数据修改后返回
   - Tables：可同时作为输入输出接口，参数可谓单个变量或Struct或内表
   - Exceptions：异常输入几口，参数可以为单个变量或内表
   - Source Code ：ABAP代码编辑器，定义RFC的具体功能。

### RFC 接口系统

SAP调用远程功能的能力是通过 RFC 接口系统 (RFC interface system) 实现的。根据调用方向的不同（SAP 系统调用其他模块或其他系统调用 SAP 模块），RFC 接口提供以下两种服务。

- ABAP程序的调用接口


- 非SAP ABAP程序的调用接口


### RFC 通信模式

RFC有同步通信(Synchronous RFC)和异步通信(Asynchronous RFC)两种方式。

同步通信使用单个函数调用，时间上允许误差较小，调用者会等待远程被调用者的处理过程。

![sRFC](/images/ABAP/RFC_1.png)

异步通信时间上允许一定的误差，从发送方系统分派功能调用时，接收系统不一定必须可用。接收系统可以在以后接收和处理该调用。如果接收系统不可用，则函数调用将保留在发送系统的出站队列中，从该位置开始定期进行重复调用，直到可以被接收系统处理为止。用户在继续调用会话之前，不需要等待它们的完成。

异步通信特点： 

- 调用后，函数控制立即返回到调用程序
- 异步RFC的参数未记录到数据库，而是直接发送到服务器

- 异步RFC允许用户与远程系统进行交互式对话
- 当调用者启动aRFC时，被调用服务器必须可用于接受请求

- 调用程序可以从异步RFC接收结果

![aRFC](/images/ABAP/RFC_2.png)

#### 同步调用的优缺点

- 优点：可以及时将数据返还给发送系统

- 缺点：系统对话时必须保证两个系统处于活动状态，否则对话出现中断，影响业务应用的处理

#### 异步调用的优缺点

- 优点：不需要接收系统随时可用，如系统升级、维护等不影响请求发送系统的业务处理

- 缺点：不适用于要求及时响应的处理过程

#### RFC调用方式

**调用同步RFC**

```html
CALL FUNCTION func [DESTINATION dest]
  EXPORTING
    F1 = a1
    F2 = a2
  INPORTING
    F3 = a3
  CHANGING
    F4 = a4
  TABLES
    T1 = ITAB
  EXCEPTIONS
    ....
 . 
```

**异步RFC**

调用异步RFC

```html
CALL FUNCTION Remotefunction STARTING NEW TASK Taskname
DESTINATION ...
PERFORMING RETURN_FLIGHT ON END OF TASK  "接收异步调用结果"
EXPORTING...
TABLES ...
EXCEPTIONS...
IF SY-SUBRC = 0.
  WRITE: 'Wait for response'.
ELSE.
  WRITE MSG
ENDIF. 
```

从异步调用的函数接收结果，请使用以下语法

```html
FORM RETURN_FLIGHT USING TASKNAME.
  RECEIVE RESULTS FROM FUNCTION Remotefunction
    IMPORTING 
      F1 = a1
    EXCEPTIONS 
      SYSTEM_FAILURE
    MESSAGE 
       SYSTEM_MSG
    [KEEPING TASK].
  SET USER-COMMAND 'OKCD'.
ENDFORM. 
```

添加 **KEEPING TASK** 可以阻止连接在接收处理结果后关闭。仅当您想将当前的远程上下文重新用于后续的异步调用时，才应使用附加的KEEPING TASK。保持远程上下文会增加存储负载，并由于系统中额外的滚动区域管理而降低性能。

WAIT UNTIL 使用

- WAIT UNTIL log_exp [UP TO sec SECONDS].

只要逻辑表达式log_exp的结果为false，它就会中断程序的执行。可以为log_exp指定任何逻辑表达式。如果log_exp的结果为false，则程序将等待直到执行了先前异步调用的函数的回调例程，然后再次检查逻辑表达式。如果逻辑表达式的结果为true，或者已经执行了先前异步调用的所有函数的回调例程，则程序将继续执行WAIT之后的语句。

#### RFC 版本包含的五种版本

1、同步 RFC（sRFC， synchronous RFC）是 RFC 的第一个版本，它要求连接的双方是同步的工作方式，即都是在可用状态才能够实现成功调用。

2、异步 RFC（aRFC，asynchronous RFC）这种 RFC 可以实现异步的 RFC 调用方式，它可以进行多个并发调用，并且不要求被调用系统的可用状态。发出调用系统会一直尝试直到获得被调用系统的应答。它通常用于当你需要提高系统并行调用多个 RFC 的效率，相对于强制等待程序的结果，它的效率更高。

3、事物 RFC（tRFC，transactional RFC）是对 aRFC 进行相关技术改进后的一个 RFC 版本，其于 aRFC 相同点是实现异步调用，其优点是可以将多个调用进行 LUW 分组处理， 并只执行一次运行。

4、队列 RFC（qRFC，queue (d) RFC）是 tRFC 的一个增强版本，它保证了所传输数据的处理次序，并可用于 SAP-SAP 及 SAP-non SAP。

5、并行 RFC（pRFC，Parallel RFC）是一种特殊的 RFC，它是 aRFC 的一种扩展类型。因为它改善了系统的性能，在执行大量的 aRFC 时。SAP 使用它在 MRP 里面提高速度。但是它只能执行在同一个系统和同一个 client 里。

#### 五种 RFC 调用特性对比

| 对比内容 | sRFC     | aRFC     | tRFC           | qRFC               | pRFC       |
| -------- | -------- | -------- | -------------- | ------------------ | ---------- |
| 执行时间 | 立即执行 | 立即执行 | 需等待         | 需等待             | 立即执行   |
| 处理模式 | 同步     | 异步     | 异步，一次执行 | 异步，一次顺序执行 | 异步       |
| 交互对话 | 支持     | 支持     | 不支持         | 不支持             | 不建议使用 |
| 状态查询 | 不提供   | 不提供   | 提供           | 提供               | 不提供     |

 

 

