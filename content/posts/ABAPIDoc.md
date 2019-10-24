---
title: "IDoc操作"
date: 2018-10-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - IDoc

---

[参考链接](http://www.baidusap.com/abap/ale_edi_idoc/782)

### IDoc



#### 1.IDoc简介

IDoc:是基于文档，用作异步传输数据的载体，类似于XML;使用功能场景：假设 1040 和 1020 是同一个集团下两个不同子公司的 SAP 系统，1040 需要将其采购订单信息及时发送给 1020，可以使用IDoc传输。

- **IDOC 的整个配置，涉及了远程连接、ALE、消息控制、tRFC 等技术的集成**

- IDoc支持异步、同步、可以收集一定数量的包后发送，最重要的是，IDOC有完整的监控系统和错误处理机制。


- IDoc支持SAP系统集团之间，SAP-CRM/SRM/PI等之间，SAP-第三方系统之间的集成


- 通过系统预定义IDOC类型，可以自动收集IDoc，挂JOB定时发送，也可以配置消息控制。


数据段是IDoc结构组件，是IDoc构成的单元;有Segment type：数据段类型与Segment.definition：数据段定义

- :Segment type名称与SAP版本无关，以"E1/Z1"开头
- :Segment definition名称与SAP版本有关，外部系统确定数据段版本的关键；以"E2/Z2"开头+版本号
  访问IDoc中具体某个字段时，需要通过Segment type。
- IDoc包含多个Segment,每个Segment内包含有多个字段
- Segment类似于XML的节点以及节点属性

IDoc特性：

- IDoc 包含有三种类型的记录：一条控制记录，一个或多个数据记录，一个或多个状态记录
- IDoc type：IDoc 结构，不同的 SAP 业务对象对应不同的 IDoc 类型（一个或多个）。
- IDoc 类型中定义了数据段（Segment）以及数据段的层级和次序。定义节点间的相互逻辑关系。
- 标准 SAP 系统提供的 IDoc 类型称为基本类型（Basic type），该类型可以通过 IDoc 扩展（Extention）进行调整，即在 SAP IDoc 类型结构的基础上增加新的数据段或者在数据段中增加新字段。
- IDoc Basic Type: 是承载数据的一个类型，可以认为是一个数据结构。Basic Type 由 Segment 构成，有点类似于 XML 的格式。每个 Segment 指定了一些字段，在这些字段里面可以输入一些限定的数据。

#### 2.包含组件

- ALE（Application Link and Enabling）是 SAP 专门为 SAP 与 SAP 之间所设计的整合中间件，多用于同一个企业中不同 SAP 系统之间的数据交换，通过 IDoc 格式的数据创建分布式系统 。分布式数据交换提供了可靠安全的通讯机制。

- EDI（Electronic Document Interchange，电子数据交换）其实就是采用标准格式的电子数据，用于在通讯网络中在业务伙伴间交换业务文档所用。按相同的排列放置数据到一个数据文档中，并按相同的排列解析此文档以得到所需的内容。

- IDoc（Intermediate Document，中转文档）是 SAP 提供的系统整合专用的数据 / 消息格式，它通过 ALE 方式来进行交换，IDoc 提供了 EDI 的支持，可以认为 IDoc 是 EDI 的一个实现。

#### 3.IDoc发送接收流程

外发过程（Outbound process：OP）:处理数据集并发出消息

- 应用文档被创建
- IDoc 生成
- IDoc 从 SAP 传送到操作系统
- IDoc 被转换成 EDI 标准格式
- EDI 文件被传送到业务伙伴处（所以业务伙伴可以没有 SAP，因为 EDI 是个标准）
- EDI 子系统将传送的状态回报给 SAP

接收过程（Inbound process：IP）：接收消息并处理

- EDI 文档被接收
- EDI 文档被转换成 IDoc
- IDoc 传送到 SAP 层
- 应用文档在 SAP 中创建
- 应用文档现在可供浏览了

####  4.IDoc流程步骤及TCode

```JS
    外发配置(OP)   					   接收配置(IP)
WE31:创建IDOC所需要的字段                        WE31:开发Segment Type
WE30:开发IDOC基本类型和扩展，把Segment分配给IDOC   WE81：开发Message Type
WE81：开发Message Type
WE82:Message Type和IDoc Type绑定                WE82:Message Type和IDoc Type绑定
BD64:添加视图模型，添加消息类型配置伙伴参数    BD64:增加消息类型
WE20:配置发送系统出站信息                     WE20:配置接收系统入站信息
SE38:编写发送程序                            SE37:编写接收接口
WE14:若为黄灯，手动发送                       WE57:分配IDOC类型给处理函数
BD51:配置进站函数模块属性
WE42:配置进站处理代码
WE02:IDOC发送信息检查
```



#### 5.Process Code处理代码设定

- WE41,WE42,WE40,WE64
- 处理代码用于确定数据写入 IDoc 或从 IDoc 读取的处理方式，处理代码对应具体的功能模块或者工作流。入站和出站处理的伙伴参数中都可以指定处理代码
- Outbound Process Code: 出站的处理代码。其实就是具体的一个操作流程。在得到了 Output Type，确定了 IDoc Basic Type 以后，需要从 Output Type 提供的所有数据里面，按 IDoc Basic Type 组合出 IDoc 来，即给 IDoc Basic Type 赋值。
- Inbound Process Code: 进站的处理代码。就是接收到一个 IDoc 以后，根据 Message Type 来确定要怎么处理这个 IDoc。
- 程序的思路就是，把每个 IDOC 结点按字符串形式逐个添加，而字符串的添加次序自然也体现了 IDOC 结点间的逻辑关系。

#### 6.端口（Port）：WE21

- 端口用于外发流程，它判断 EDI 子系统程序名称、IDoc 文件传送到操作系统的目录，IDoc 文件名和 RFC 目的地
- 端口，指定这个 IDoc 的接收方，可以是任何其他系统，也可以发到当前的 SAP 当前的 Client。

#### 7.RFC目的地：SM59

- 用于定义到远程系统通讯连接的特性以及需要调用何种功能

#### 8.Partnr Profile：WE20

- Partnr:对口的合作伙伴
- Partner Profile：是Partner的一些参数，分为OP和IP.它们是为了让系统知道怎么去处理 Outbound/Inbound 的事件.

- Partner Profile 指定在外发过程中所用的各类组件（业务伙伴号、IDoc 类型、信息类型、端口、处理码等），通讯方式（异步或同步）以及当错误时通知何人

#### 9.Message Type

- 用于消息类型的处理，就是告诉 Inbound 一方，我这个 IDoc 是用来做什么的。

- WE81， 创建Message type
- WE82，连接 Message type和 IDOC type

#### 10.IDoc类型定义的相关函数

函数组 EDIM 中的 SAP 内部功能模块用于操作 IDoc 基本类型和扩展：OBJECT代指所操作的队象，既IDOCTYPE或EXTTYPE.

- [OBJECT]_CREATE
- [OBJECT]_UPDATE
- [OBJECT]_READ
- [OBJECT]_DELETE
- [OBJECT]_CLOSE
- [OBJECT]_UNCLOSE
- [OBJECT]_EXISTENCE_CHECK
- [OBJECT]_INTEGRITY_CHECK
- [OBJECT]_TRANSPORT

函数组EDIJ中的SAP内部功能模块用于操作IDoc数据段

- SEGMENT_CREATE
- SEGMENT_MODIFY
- SEGMENT_DELETE
- SEGMENT_INITION_DELETE

#### 11.IDoc常用TCode

SAP 提供了一个事务码列出的基本上所有的 IDoc 相关的事务码`WEDI`

| /BD87 | /WE18 | /SUIM |
| ----- | ----- | :---: |
| /SM58 | /WE19 | /GS03 |
| /WE09 | /WE20 | /BDLS |








