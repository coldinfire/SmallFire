---
title: "RFC外部调用"
date: 2018-10-12T15:15:42+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - RFC
  - abap


---

### RFC

#### 什么是 RFC

​	RFC 是 SAP 系统和其他（SAP 或非 SAP）系统间的一个重要而常用的双向接口技术，也被视为 SAP 与外部通信的基本协议。简单地说，RFC 过程就是系统调用当前系统外的程序模块，从而实现某个功能，而且调用系统和被调用系统中至少有一个必须是 SAP ABAP 系统。这种远程功能调用也可在同一系统内部进行（如本地 SAP 系统内的远程调用）；但通常情况下，调用程序和被调用程序处于不同系统。

#### RFC 调用过程

​	在系统间通信过程中，需区分发送系统和接受系统。RFC 调用请求从发送系统（调用系统）中传至接收系统（被调用系统，也称远程系统或目标系统），发送请求的系统在通信过程中又称为 RFC 客户端，通信另一方则称为 RFC 服务器。RFC 客户端发起远程功能调用以执行 RFC 服务器提供的功能。

​	其中，调用系统和被调用系统均可以是 SAP 系统和非 SAP 系统，此外还可以在 SAP 系统内部将特定应用服务器指定为目标系统。

#### RFC 通信的情况

根据通信方向和系统类型，共有如下三种 RFC 通信：

- 两个独立的 SAP 系统之间的通信；

- SAP 系统作为调用系统，与外部远程系统（非 SAP ABAP 系统）通信； 

- 外部系统作为调用系统，与 SAP 系统通信。

#### RFC创建

1. 通过TCode:<SE37>,进入RFC开发初始界面

2. 创建RFC与Report不同的是，RFC程序需要先定义一个FunctionGroup，一个Group下可包含多个Function。

3. Goto > Function Groups > Create Group

4. 对Function进行属性设置

5. Functino编辑器分为七个页面：
   - Attributes：设置Short text和Processing Type,是否允许外部调用。
   - Import：数据输入接口，接口参数可以为单个变量或则为一个结构体。
   - Export：数据输出接口，接口参数可以为单个变量或一个结构体。
   - Changing：将传入的表数据修改后返回
   - Tables：可同时作为输入输出接口，参数可谓单个变量或Struct或内表
   - Exceptions：异常输入几口，参数可以为单个变量或内表
   - Source code：ABAP代码编辑器，定义RFC的具体功能。

#### RFC 接口系统

​	SAP 调用远程功能的能力是通过 RFC 接口系统 (RFC interface system) 实现的。根据调用方向的不同（SAP 系统调用其他模块或其他系统调用 SAP 模块），RFC 接口提供以下两种服务。

（1）ABAP 程序的调用接口

（2）非 SAP ABAP 程序的调用接口。

#### RFC 通信模式

同步通信和异步通信：同步通信时间上允许误差较小，异步通信时间上允许一定的误差。 

*同步调用的优缺点：*

- 优点：可以及时将数据返还给发送系统；

- 缺点：系统对话时必须保证两个系统处于活动状态，否则对话出现中断，影响业务应用的处理。

*异步调用的优缺点：*

- 优点：不需要接收系统随时可用，如系统升级、维护等不影响请求发送系统的业务处理；

- 缺点：不适用于要求及时响应的处理过程。

 

#### RFC 版本包含的五种版本

1、同步 RFC（sRFC， synchronous RFC）是 RFC 的第一个版本，它要求连接的双方是同步的工作方式，即都是在可用状态才能够实现成功调用。

2、异步 RFC（aRFC，asynchronous RFC）这种 RFC 可以实现异步的 RFC 调用方式，它可以进行多个并发调用，并且不要求被调用系统的可用状态。发出调用系统会一直尝试直到获得被调用系统的应答。它通常用于当你需要提高系统并行调用多个 RFC 的效率，相对于强制等待程序的结果，它的效率更高。

3、事物 RFC（tRFC，transactional RFC）是对 aRFC 进行相关技术改进后的一个 RFC 版本，其于 aRFC 相同点是实现异步调用，其优点是可以将多个调用进行 LUW 分组处理， 并只执行一次运行。现在 aRFC 基本上已经停用。

4、队列 RFC（qRFC，queue (d) RFC）是 tRFC 的一个增强版本，它保证了所传输数据的处理次序，并可用于 SAP-SAP 及 SAP-non SAP。

5、并行 RFC（pRFC，Parallel RFC）是一种特殊的 RFC，它是 aRFC 的一种扩展类型。因为它改善了系统的性能，在执行大量的 aRFC 时。SAP 使用它在 MRP 里面提高速度。但是它只能执行在同一个系统和同一个 client 里。

**五种 RFC 调用特性对比：**

|          | sRFC     | aRFC     | tRFC           | qRFC               | pRFC       |
| -------- | -------- | -------- | -------------- | ------------------ | ---------- |
| 执行时间 | 立即执行 | 立即执行 | 需等待         | 需等待             | 立即执行   |
| 处理模式 | 异步     | 异步     | 异步，一次执行 | 异步，一次顺序执行 | 异步       |
| 交互对话 | 支持     | 支持     | 不支持         | 不支持             | 不建议使用 |
| 状态查询 | 不提供   | 不提供   | 提供           | 提供               | 不提供     |

 

### Webservice

#### SAP 调用 Webservice

- a. 获取 webservie wsdl,

- b. 生成 webservice

- c.LPCONFIG 创建 Logical Port, 保存并激活

- d. 编写 webserive 程序，
  CALL 方法调用 webservice，

- e. 测试程序；

#### SAP 发布 webservice 供其他系统调用

- a. 服务配置，- sicf
- b. 函数创建，- SE37：YRFC_TEST_WEB 
- c. 生产 webservice, 函数 -> 设置
- d. 进行 IE 发布 SOAMANAGER 
- e. 外部语言进行调用测试，

 

