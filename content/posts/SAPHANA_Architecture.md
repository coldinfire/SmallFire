---
title: " SAP HANA 结构分析 "
date: 2021-11-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - HANA

---

### HANA 结构

SAP HANA 服务器使用各种组件来处理传入请求并返回结果集。 这些组件分为服务器和引擎，服务器是在操作系统级别运行的进程和服务，引擎是服务器内处理特定查询请求的组件。经典应用程序用例中常见的一些重要的 SAP HANA 数据库服务：

![HANA Architecture](/images/HANA/HANA_DB_Architecture.png)

在经典场景中，从数据库中获取数据的请求是从 Web 服务器或应用程序发起的。这些请求可以是 SQL 语句或 MDX(Multidimensional Expressions)的形式。这些传入请求由会话和事务管理器处理和验证。 SQL/MDX 处理器执行 SQL 或 MDX 命令并传递给 SQL 引擎。

SAP HANA 数据库有自己的脚本语言，称为 SQLScript。 你可以在数据库层将 SQLScript 用于数据密集型逻辑，这意味着 SQLScript 可以与常规 SQL 命令一起执行。 这种集成是在内置库（也称为 SAP HANA 库）的帮助下实现的。 除了 SQLScript，SAP HANA 库还支持各种专用功能库，例如 SAP HANA 业务功能库 (BFL) 和 SAP HANA 预测分析库 (PAL)。这些库紧密集成到各种引擎中。

此外，复杂的查询由不同的组件引擎处理，例如计划引擎和计算引擎，或者由数据库中的存储过程处理。

#### XS Server

XS 服务器：支持使用 SAP HANA 扩展应用程序服务 (SAP HANA XS)，它由 HTTP 服务器和将查询分派到各个组件的运行时环境组成。 XS 服务器与索引服务器和开发对象的 SAP HANA 存储库进行交互。

#### Pre-Processor Server

预处理器服务器：分析文本数据并从该数据中提取信息。 该服务器还有助于 SAP HANA 的搜索功能。

#### Statistical Server

统计服务器：通过收集有关单个组件整体性能的信息以及每个组件消耗的资源状态来监控 SAP HANA 数据库。

#### Name Server

名称服务器：包含有关 SAP HANA 系统拓扑的所有信息，例如租户数据库的位置。 此名称服务器保存存储在相关租户数据库目录中的表和表分区的位置。

### Index Server

索引服务器：是 SAP HANA 的主要组件，主要负责服务 SQL/数据请求。 索引服务器是保存实际数据的地方，引擎可以帮助处理数据请求的数据。 在索引服务器中找到的引擎/组件包括以下内容：

- 会话和事务管理器
- SQL/多维表达式 (MDX) 处理器
- 数据引擎
- SQL引擎、SQLScript、R语言引擎、计算引擎
- 存储库
- 持久层

#### Data Engine

为了支持 R 语言的使用，R 语言引擎使 R 语言的程序能够在 SAP HANA 中执行。 类似地，计算引擎用于执行生成计算视图的各种过程。所有这些引擎都运行在 Data Engine 之上，Data Engine 具有称为数据引擎函数的内置函数。 这些函数可以访问 SAP HANA 存储库，其中包含元定义，包括关系表、列、视图、索引等的定义。所有这些元数据都保存到作为存储库一部分的目录中。

持久层：是保存所有数据以及事务日志的地方。 在这一层，系统确保数据符合 ACID 原则。 持久层确保在由于意外关闭而重新启动的情况下恢复最新的数据集。

### Platform Capabilities

SAP HANA 已经从一个数据库发展成为一个支持广泛的复杂业务需求，并且可以执行实时分析。SAP HANA 作为平台提供的功能不断扩展，现在包括高级分析功能、处理结构化和非结构化数据以及流数据的能力。

![HANA_Platform_Capabilities](/images/HANA/HANA_Platform_Capabilities.png)

#### Database Services

SAP HANA 的数据库服务是使其他应用程序能够在 SAP HANA 上更快、更好地运行的基础服务。其中的一些功能，例如多核 CPU、数据压缩、在线分析处理 (OLAP) 和在线事务处理 (OLTP)、数据分区、数据建模、多租户数据库和多层存储等。同时通过灾难恢复、管理和安全管理功能保持高可用性。

#### Processing Services

SAP HANA 的处理能力与基础数据库服务紧密集成。 这种紧密集成可帮助您执行预测分析、搜索、文本分析和空间分析。

#### Application Services

SAP HANA 支持一系列服务，例如 SAP Fiori 用户体验、图形建模和应用程序生命周期管理。 应用程序服务可用于构建支持开放式开发标准的基于 Web 的应用程序，例如 SQL、HTML5、JavaScript、Java 数据库连接 (JDBC)、开放式数据库连接 (ODBC)、JavaScript 对象表示法 (JSON) 和 OData。

#### Integration Services

在许多组织中，数据是在整个环境中的多个系统中生成和存储的。 为了提供完全集成的解决方案，SAP 支持各种集成策略，例如：

- SAP HANA 智能数据访问允许访问存在于孤岛中的数据，而无需复制这些数据。
- SAP HANA 智能数据集成将数据从基于云的或本地系统实时复制并移动到 SAP HANA 平台中。
- SAP HANA 远程数据同步在 SAP HANA 和远程数据库之间同步数据。