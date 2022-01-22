---
title: " SAP HANA 开发环境 "
date: 2021-11-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - HANA

---

### SAP HANA Studio

SAP HANA Studio 是一个基于 Eclipse 的集成开发环境 (IDE)，可在 Eclipse 3.6 或更高版本上运行，它将各种开发和管理工具组合到一个包中。 SAP HANA Studio 拥有一个用于管理、监控、数据建模和供应技术的默认环境。

SAP HANA Studio 中的这些开发工具通常被称为 perspectives (透视图)； 相反，在 SAP GUI 中这些工具被称为 transactions (事务)。为了支持使用 ABAP 进行应用程序开发，需要额外的透视图，例如 ABAP 开发工具 (ADT)，并且需要在安装 SAP HANA Studio 后单独安装。ADT 还充当访问 SAP HANA 层或数据库服务器的接口，以利用code pushdown 技术开发或优化本机/非本机应用程序。

SAP HANA Studio 提供的几个关键功能包括：

- 能够作为单一集成开发环境 (IDE) 使用 SAP HANA 进行 ABAP 开发，包括移动和云应用程序
- 使用用户界面工具（例如用于 ABAP 的 Web Dynpro 或 Floorplan Manager）开发 Web Dynpro 应用程序的能力
- 提供了一个特殊的 ABAP Debug 透视图以支持调试
- 通过 ABAP Test Cockpit 保证质量
- 使用新的 ABAP Profiler 测量和优化应用程序的功能，该工具类似于 SAP GUI 中的事物码 SAT 或 SE30
- 使用传输管理器对 ABAP 和 SAP HANA 工件进行生命周期管理
- 代码完成模板和重构工具可提高开发人员的生产力和效率
- 轻松集成自定义或第三方工具

### 下载

#### 兼容性检查

在安装或使用 SAP HANA Studio 之前，需要进行一些兼容性检查，以避免在安装期间或稍后访问 SAP HANA Studio 透视图时出现错误。

#### 版本校验

确保选择正确的 SAP HANA Studio 版本来安装在系统上至关重要。 正确的版本将与操作系统和安装的 SAP HANA 数据库版本兼容。 因此，在选择要安装的 SAP HANA Studio 版本之前，必须验证系统的操作平台及其安装的 SAP HANA 数据库版本。

HANA 数据库版本：通过事物码 **DBACOCKPIT** 的 **DB Release** 字段来查看当前 SAP HANA 数据库版本，然后查阅 SAP Note **2375176**，它提供了 SAP HANA 修订及其相关的兼容 SAP HANA Studio 版本的列表。

#### 下载步骤

按照以下步骤从 SAP SAP Support Portal 的 Download Center 下载 SAP HANA Studio 组件：

1. Log on to [https://support.sap.com/en/index.html](https://support.sap.com/en/index.html) with your S-user ID and password.

2. Select **Download Software**.

3. Click on **SUPPORT PACKAGES & PATCHES** and navigate **By Alphabetical Index (A-Z)** to select the letter **H**.

4. Search for and select the **SAP HANA Platform Edition**.

5. Select **SAP HANA PLATFORM EDITION 2.0**.

6. Click on **SAP HANA Studio 2**, to choose the appropriate SAP HANA Studio version compatible with the underlying SAP HANA database revision.

7. Click on the SAP HANA Studio version to download and save the **SAR file**.

8. To install SAP HANA Studio, you’ll need to extract this SAR file. To do that, you also need to download the **SAPCAR.exe** file from the SAP Software Download Center, which will enable you to unzip the SAR file. Search for the SAPCAR file and download it.

9. Open a command prompt and go to the location where you saved the SAPCAR file. In the command prompt, enter `SAPCAR.exe –xvf <Studio SAR File name>.SAR` to unpack the SAR file.

### 安装

从 SAP Support Portal 下载并解压 SAP HANA Studio 版本后，单击 **hdbsetup** 文件并按照安装向导安装 SAP HANA Studio。 您将执行以下步骤：

1. In the **Define Studio Properties** step of the installation wizard, you can update the existing SAP HANA Studio version or install a new instance. Choose **Install new SAP** **HANA Studio**. Then, click **Next** to proceed.
2. In this step, you’ll select the features to install. The available features are **SAP HANA** **Studio Administration**, **SAP HANA Studio Application Development**, and **SAP HANA** **Studio Database Development**. Select all three and click on **Next**.
3. Review your selections and click on **Install** to proceed with the installation.
4. Click on **Finish** to complete the installation.

### 使用 HANA Studio

#### 打开透视图

- 导航栏选择 Windows --> Perspective --> Open Perspective --> Other…
- 从显示的列表中选择 Perspective，比如 SAP HANA Modelers
- 单击打开

#### ABAP Perspective

ABAP 透视图主要用于创建非原生 ABAP 开发对象，如程序、类、字典对象、Web Dynpro 应用程序等。

ABAP 透视图不作为默认 SAP HANA Studio 透视图组件，必须单独安装。 要使用 ABAP 透视图，首先必须安装 ABAP 开发工具 (ADT) 和相关的 Eclipse 功能。

使用 ABAP Prespective 后可以创建 ABAP Project：创建 ABAP Project 时需要选择合适的 SAP 后端系统并验证与 SAP S/4HANA 后端系统的连接设置，然后单击下一步；输入 Client、用户名和密码信息并点击完成。

#### Modeler Perspective

Modeler 透视图提供了一个图形环境，用于为 SAP HANA 优化模型设计信息视图，包括以下功能：

- 使用基于 SQL 和 SQLScript 的模型，开发高级计算模型
- 使用 SAP HANA 应用程序函数库 (AFL) 和基于 SQLScript 的存储过程。
- 快速启动对标准建模工具的访问以配置导入服务器，以进行数据供应、批量复制、自动记录、激活、验证或重新部署内容

Modeler 透视图提供了一个环境来定义信息模型，例如主要用于分析报告的 Attribute、Analytic 和 Calculation 视图。

- Attribute Views
  - 主要参考主数据表，如物料主数据、客户主数据等
  - 也能够将文本表相互连接

- Analytic views
  - 表示一个类似 OLAP 立方体的视图
  - 包括基于带有度量的事实表的数据基础（数量、价格等关键数据）
  - 加入星型模式维度的事实表
  - 能够在运行时评估连接和计算的度量
  - 用于计算和聚合

- Calculation views
  - 执行其他视图无法执行的复杂计算
  - 必须至少有一项措施
  - 定义为图形或脚本视图 (SQLScript)

Modeler 透视图还用于访问 Systems 视图。 Systems 视图是 SAP HANA Studio 中配置的 SAP HANA 数据库内容的中央访问点。所有 SAP HANA 数据库连接都列在 Systems 视图下。每个 SAP HANA 连接的内容将被组织到以下文件夹中：

- Catalog
  - Catalog 文件夹包含数据库对象，如表、函数、索引、过程、序列、同义词、触发器和视图，这些对象分组在数据库模式下
  - 这些数据库对象使用 schemas 进行逻辑分组，SAP 默认 schemas： DBACOCKPIT --> DB User
  - Catalog 文件夹在激活数据库对象时保存运行时对象，并存储在默认 Database Schema
- Content
  - Content 文件夹包含 SAP HANA 特定的设计时信息模型，包括 Attribute Views、 Analytic Views、 Calculation Views、Analytic Privileges 和 Decision Tables。信息视图主要用于分析用例
  - 这些建模对象被组织成包，可以通过定义权限限制开发人员访问；也可以将这些对象传输到 Landscape 中配置的其他系统
  - 当在 content repository 中创建对象时，内置用户 `_SYS_REPO` 负责激活对象。 该用户是唯一拥有使用这些对象的所有权和授权的用户。 另外，作为内置系统用户，其他用户不能以`_SYS_REPO`用户身份登录； 因此，要访问和使用 content repository 对象，必须创建角色并将这些角色分配给适当的用户。 涉及角色的任务（创建角色、为用户分配角色）在 Modeler 透视图中的 Security 文件夹中执行
- Security
  - Security 文件夹使系统管理员能够创建和分配角色，以便根据定义的权限为用户提供适当的访问权限。SAP HANA 在对象、系统、分析、包和应用程序级别提供多种权限。
  - Security 文件夹包含默认角色或建模者角色。当用户使用客户端接口（例如 ODBC、JDBC 或 HTTP）访问 SAP HANA 数据库时，他们对数据库对象执行数据库操作的能力取决于他们被授予的权限。通过角色直接或间接授予用户的所有权限都被组合在一起。因此，每当用户尝试访问对象时，系统都会对用户 ID、分配的角色和授予的权限执行授权检查。
- Provisioning

  - Provisioning 文件夹与智能数据访问相关，用作创建、准备和建立网络连接以向业务用户提供数据的方法。 即使在使用前端工具向用户提供数据之前，也必须将数据从各种远程数据源（如 Hadoop、SAP Adaptive Server Enterprise [SAP ASE] 等）整合到 SAP HANA 数据库表中。 要执行此数据整合活动，可以使用提取、加载和转换 (ETL) 流程

  - 几种上传数据到 HANA Database 技术的使用。 可以根据项目和业务需求调整以下数据供应技术：

    ![HANA ProvisioningTools](/images/HANA/HANA_ProvisioningTools.png)

    | Techniques | Desc                                                         |
    | :--------- | :----------------------------------------------------------- |
    | SLT        | SAP Landscape Transformation Replication Server (table to table replication, real time) |
    | ETL        | SAP Data Services (for ETL)                                  |
    | DXC        | Direct Extractor Connect (using standard datasources)        |
    | Flat File  | Flat file (csv or xls upload)                                |
    | BW         | SAP Business Warehouse or SAP BW/4HANA as ETL                |
| BODS       | BO data services                                             |
    
    