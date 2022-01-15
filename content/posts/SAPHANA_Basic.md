---
title: " SAP HANA 知识链接 "
date: 2019-05-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - HANA

---

### 了解 HANA 的途径有

http://help.sap.com 

http://www.sdn.sap.com/irj/sdn/in-memory 

http://service.sap.com/hana

#### HANA 主要组成成分

- 客户端：HANA Studio(In-memory studio)

- 内存内数据库：HANA Database

- 数据复制组件：SLT/Sybase Replication Sever/BusinessObjects DataServices

#### 三种复制数据方法

- Trigger-based：使用的是 SAP 自己开发的已经有一段历史的工具 SAP Landscape Transformation Replication Server (SLT RS)，这是与底层数据库无关的技术。
- ETL-based：其实就是 ETL 数据到 HANA，使用的是 SAP EIM 的旗舰产品 BusinessObjects DataServices。
- Log-based：是 Sybase 的 Replication Server，而这曾经是 SAP HANA 的首选，是基于数据库 change log 的复制技术。

#### Differences between S4HANA & ECC

| SAP S4 HANA                                | SAP ECC                                                     |
| :----------------------------------------- | :---------------------------------------------------------- |
| 仅在 HANA 数据库上运行                     | 在多个数据库上运行：Oracle、SQL Server、DB2                 |
| 它经过优化以充分利用 SAP HANA 的内存功能   | 即使它可以在 HANA 上运行，但是没有进行优化                  |
| 内部部署、私有云和公共云版本               | 本地版本，可以托管在 Hyperscaler 中，但没有可用的公共云版本 |
| 简化数据模型，部分功能替换为云应用         | 标准范围                                                    |
| 基于 fiori 的用户体验                      | 基于 SAP GUI 的用户界面。 FIORI 应用程序可用但有限          |
| 高级功能，如嵌入式分析、机器人流程自动化等 | 没有这样的创新                                              |

### SAP HANA 应用场景和方案

HANA DB 是一个列存储的数据库，列存储的数据库更容易压缩，聚合结果更快，所以是为分析所设计的。这是 HANA 将会使数据分析提速的因素之一。

HANA DB 是内存内计算数据库，也就是说不仅仅是部分数据存储在内存里，更重要的是，一些逻辑计算发生在内存的数据里，这样肯定要比在应用层计算快得多。这也是 HANA 使数据分析提速的重要因素。

#### HANA Code To Data

当使用内存数据库（code-to-data paradigm）时，ABAP 编程范式的这种根本性变化非常重要。

代码到数据范式与经典编程的数据到代码方法的比较：

![HANA_CodeToData](/images/HANA/HANA_CodeToData.png)

HANA 将执行复杂数据操作（如计算、聚合和文本处理）所需的应用程序逻辑通过实现多种可用技术之一被移至数据库层。

![HANA_CodeToData_Methods](/images/HANA/HANA_CodeToData_Methods.png)

#### Side-by-Side Scenario

![Side By Side](/images/HANA/HANA_SideBySide_Scenario.png)

SAP HANA accelerators：SAP HANA 加速器的使用是一种并行方案，其中 SAP HANA 与传统数据库并行部署。 一些选定的业务流程被复制到 SAP HANA。 所选业务流程的数据从主数据库（即传统数据库）复制到 SAP HANA。
这种对选定业务场景的加速是通过将数据访问重定向到 SAP HANA 来实现的。 这种数据访问的重定向可以通过各种过程来实现，通常是通过单独的数据库连接和对 ABAP 代码的小改动。

Data mart：SAP HANA 最初旨在支持在尽可能快的时间内分析大量数据，以促进实时分析。 SAP HANA 的这种应用被广泛使用。 在这种情况下，SAP HANA 就像一个数据集市，其中数据从主数据库移动到 SAP HANA 以进行数据报告和分析，该数据作为任何其他应用程序如 SAP BusinessObjects Business Intelligence (SAP BusinessObjects BI) 的数据源运行，SAP Lumira，以及文本和预测分析。

SAP HANA applications for enterprise suites：另一个并行场景涉及将 SAP HANA 与传统数据库并行部署。 数据被复制到 SAP HANA。不仅是选定的业务流程，而是以 SAP HANA 作为数据源运行成熟的应用程序。一些示例包括零售销售分析、流动性风险管理、ERP 运营报告、社交情绪智能、销售管道分析等。

#### Fully Integrated Scenario

在完全集成的场景中，SAP HANA 服务于主数据库。 在这种情况下，已经完全迁移到 SAP HANA 数据库并使用 SAP 提供的最新解决方案，这些解决方案已经过重新设计，可与 SAP HANA 一起高效运行，从而充分发挥 SAP HANA 的性能。

![Fully Integrated](/images/HANA/HANA_FullyIntegrated_Scenario.png)

SAP Business Suite powered by SAP HANA and SAP S/4HANA：各种业务套件是 SAP 解决方案系列的一部分。 示例包括由 SAP HANA 和 SAP S/4HANA 提供支持的传统 SAP Business Suite。

SAP BW powered by SAP HANA and SAP BW/4HANA：SAP BW 支持的 SAP HANA 和 SAP BW/4HANA 是使用 SAP HANA 作为主数据库的数据仓库解决方案。 这些解决方案用于 SAP HANA 上的业务规划和整合。

SAP Business One, version for SAP HANA：SAP Business One 是面向中小型企业 (SME) 的数字核心，在 MS SQL 上运行。 此解决方案在 SAP HANA 和 SAP HANA Cloud 上使用 SAP Business One 分析。