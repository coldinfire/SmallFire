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

SAP HANA Studio 中的这些开发工具通常被称为 perspectives( 透视图)； 相反，在 SAP GUI 世界中，这些工具被称为 transactions(事务)。为了支持使用 ABAP 进行应用程序开发，需要额外的透视图，例如 ABAP 开发工具 (ADT)，并且应在安装 SAP HANA Studio 后单独安装。

ADT 还充当访问 SAP HANA 层或数据库服务器的接口，以利用code pushdown 技术开发或优化本机/非本机应用程序。

SAP HANA Studio 提供的几个关键功能包括：

- 能够作为单一集成开发环境 (IDE) 使用 SAP HANA 进行 ABAP 开发，包括移动和云应用程序
- 使用用户界面工具（例如用于 ABAP 的 Web Dynpro 或 Floorplan Manager）开发 Web Dynpro 应用程序的能力
- 提供了一个特殊的 ABAP Debug 透视图以支持调试
- 通过 ABAP Test Cockpit 保证质量
- 使用新的 ABAP Profiler 测量和优化应用程序的功能，该工具类似于 SAP GUI 中的事物码 SAT 或 SE30
- 使用传输管理器对 ABAP 和 SAP HANA 工件进行生命周期管理
- 代码完成模板和重构工具可提高开发人员的生产力和效率
- 轻松集成自定义或第三方工具

### 兼容性检查

在安装或使用 SAP HANA Studio 之前，需要进行一些兼容性检查，以避免在安装期间或稍后访问 SAP HANA Studio 透视图时出现错误。

#### 版本校验

确保选择正确的 SAP HANA Studio 版本来安装在系统上至关重要。 正确的版本将与操作系统和安装的 SAP HANA 数据库版本兼容。 因此，在选择要安装的 SAP HANA Studio 版本之前，必须验证系统的操作平台及其安装的 SAP HANA 数据库版本。

HANA 数据库版本：通过事物码 **DBACOCKPIT** 的 **DB Release** 字段来查看当前 SAP HANA 数据库版本，然后查阅 SAP Note **2375176**，它提供了 SAP HANA 修订及其相关的兼容 SAP HANA Studio 版本的列表。

### 下载

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

#### 打开透视图

导航栏选择 Windows --> Perspective --> Open Perspective --> Other…；从显示的列表中选择 Perspective，比如SAP HANA Modelers ；单击打开。

ABAP Perspective

ABAP 透视图主要用于创建非原生 ABAP 开发对象，如程序、类、字典对象、Web Dynpro 应用程序等。ABAP 透视图不作为默认 SAP HANA Studio 透视图交付，必须单独安装。 要使用 ABAP 透视图，首先必须安装 ABAP 开发工具 (ADT) 和相关的 Eclipse 功能。