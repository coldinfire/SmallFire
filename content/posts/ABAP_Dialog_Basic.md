---
title: " Dialog 程序简介 "
date: 2018-08-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Dialog

---

SAP-ABAP 支持两种类型的程序：Report 和 Dialog。如果 ABAP 程序需要用户输入或则当我们需要在屏幕之间来回导航时，则使用 Dialog 编程。

对话程序的创建类型为“M”— Module Pool。 它们不能独立执行，必须附加到至少一个初始屏幕的事务代码。

### Report 和 Dialog  的差异

Report：报表是一种程序，它通常在不更改数据库的情况下读取和分析数据库表中的数据。

Dialog：对话程序允许您与系统交互工作并更改数据库表的内容。 每个对话程序都有特定的屏幕序列，由系统一个接一个地处理。

![Dialog](/images/ABAP/ABAP_Dialog_1.png)

### ABAP 与 Dialog 交互方式

用户对话是用户和程序之间的任何形式的交互，可以是以下任何一种:

- 输入数据
- 选择菜单项
- 单击按钮
- 单击或双击条目

ABAP 程序与 Dialog 屏幕进行数据交换的方式，通过在程序中定义一个与 Dialog 中同名的全局变量或者结构，从而实现数据的自动传递。

- 加载数据：在 PBO 中实现
- 推送数据：在 PAI 中控制

### Dialog 的组成部分

对话程序开发需要开发多个对象，而这些对象都不能单独执行。 相反，所有对象都分层链接到主程序，并按照对话主程序指定的顺序执行。

![Dialog](/images/ABAP/ABAP_Dialog_2.png)

#### Transaction Code

SE93 创建事务代码。事务代码链接到 ABAP 程序和初始屏幕。

可以使用 CALL SCREEN 语句从任何 ABAP 程序启动屏幕序列。

#### Screens

SAP 系统中的每个对话框都由一个或多个屏幕控制。可以通过事务 SE51 在 ABAP 工作台中使用 Screen Painter 创建屏幕，每个屏幕都属于一个 ABAP 程序。

这些屏幕由 screen mask 或 layout 及其流程逻辑组成。 屏幕具有确定输入/输出字段和其他图形元素（例如复选框和单选按钮）的位置的布局。 Flow Logic 确定屏幕内的逻辑处理。

#### GUI Status

每个屏幕都有一个 GUI 状态，它们是程序的独立组件。它控制菜单栏、标准工具栏、应用程序工具栏，用户可以通过这些工具栏选择应用程序中的功能。

可以使用 SE41：Menu Painter 在 ABAP Workbench 中创建它们。

#### ABAP Program

R/3 系统中的每个屏幕和 GUI 状态都属于一个 ABAP 程序。ABAP 程序包含由屏幕流逻辑调用的对话框模块，还处理来自 GUI 状态的用户输入。

使用屏幕的 ABAP 程序也称为对话程序。

在模块池中（M 型程序）； 要调用的第一个处理块始终是对话模块。 但是，也可以在其他 ABAP 程序中使用屏幕，例如可执行程序或功能模块。 然后以不同方式调用第一个处理块； 例如，由运行时环境或过程调用。 然后使用 CALL SCREEN 语句启动屏幕序列。

#### Screen Flow Logic

Screen Flow Logic(屏幕流逻辑) 定义了在 screen 上的各种事件，而 Dynpro 定义了这个 Screen 和各种事件。ABAP MODULE POOL 会处理这些 Event，在每次事件时调用一个 ABAP program。

屏幕流逻辑主要分为四个组件。

- PBO：Process Before Output 事件，在屏幕显示之前处理
- PAI：Process After Input 事件，在用户在屏幕上进行操作后处理
- POH：Process on help request，按 F1 时处理
- POV：Process on value request，按 F4 时处理

#### Dynpro

屏幕及其流程逻辑称为 Dynpro（“动态程序”，因为屏幕流程逻辑会影响程序流程），每个 dynpro 都精确地控制对话程序的一个步骤。

属于程序的屏幕被编号。 屏幕流序列可以是线性的，也可以是循环的。 在一个屏幕链中，您甚至可以调用另一个屏幕链，并在处理之后返回到原始链。 您还可以从 ABAP 程序的对话框模块中覆盖静态定义的下一个屏幕。

#### ABAP Module Pool

在 PBO 或 PAI 事件中，Dynpro 调用 ABAP 对话程序。 此类程序的集合称为 ABAP 模块池。

例如，在 PAI 事件中调用的模块用于检查用户输入并触发适当的对话步骤，例如更新任务。

从一个事务中调用的所有 dynpro 都引用一个公共模块池。



