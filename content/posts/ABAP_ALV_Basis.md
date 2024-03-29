---
title: " 报表开发<概述> "
date: 2018-06-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

**ALV** (SAP List Viewer) 是 SAP 常用的屏幕显示列表控件对象，通过传递数据内表方式显示数据； **ALV** 显示格式分为 Grid 和 List 两种模式。

-  Grid 模式有栏位选择按钮功能，允许用户直接输出格式，操作更为灵活。
- List 模式则固定格式，应用于较严格的标准报表。

#### 创建 ALV 的不同方法

- Basic：函数生成 ALV，REUSE_ALV_GRID_DISPLAY / REUSE_ALV_GRID_DISPLAY_LVC 
- OOALV：以面向对象的方式实现，核心类 CL_GUI_ALV_GRID
- [SALV](https://zevolving.com/category/salv-tutorial/salv-table/)：另一种面向对象的实现，简化了开发的复杂程度；核心类：CL_SALV_TABLE；不提供编辑模式
- [FALV](https://abapblog.com/falv) ：ZCL_FALV，开源可用的

### 报表组成

#### Report 开头

```ABAP
REPORT <report_name> no standard page heading
  line-size <size>
  line-count <n(n1)>
  message-id <message_class>.
```

- no standard page heading：为了抑制列表标题或程序名称。


- line-size：设置报表的行大小。


- line-count：使用加法行数 n(n1) 来设置特定页面的行数。N 是页面的行数，N1 是为页脚保留的行数。


- message-id：为了显示程序执行的消息，向程序添加消息类：Message-id <消息类名称>。消息类在 SE91 中维护。


#### Report 主体内容

数据定义：定义报表依赖的表，定义全局变量、内表、ALV 变量、宏定义等。

INCLUDE x：可以通过 include 方式保存报表的功能函数。

[选择屏幕](https://coldinfire.github.io/2018/ABAP_ALV_Screen/)：指定程序应运行的输入值的屏幕，Parameters；Select-Options。

报表事件：在报表事件中对选择屏幕内容进行处理。

业务代码：在报表事件START-OF-SELECTION 后面实现报表的主要业务逻辑。

显示报表：将处理后的业务数据以指定样式和格式显示；使用 ALV 显示数据，首先需要知道输出什么**数据**(内表类型的数据结构)，还需要知道输出数据的**数据结构**（比如第一列显示什么，第二列显示什么，如何对齐，表头是什么，宽度多少，等等）。SAP 将这个数据结构称为 Field catalog。

### 执行程序的使用范围，报表事件

报表编程是一种事件驱动的编程。

#### 选择屏幕事件

- LOAD-OF-PROGRAM.
- INITIALIZATION. 
  - 初始化事件，用来填充选择屏幕默认值
- AT-SELECTION SCREEN ON field p_xxx.  
  - 在 PAI 事件结束后执行，进行校验和检查输入值
- AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_xxx. 
  - 选择屏幕字段选择功能扩展
- AT SELECTION-SCREEN ON block_xxx.
  - Block 的触发事件（控制框架中的屏幕元素值的输入）
- AT SELECTION-SCREEN OUTPUT.   
  - (PBO) 显示选择屏幕之前触发
- AT SELECTION-SCREEN.   
- (PAI) 选择屏幕中执行某些功能后触发
  - PERFORM check_input.
- START-OF-SELECTION.
- Begin the main programmer

#### 列表屏幕事件

- TOP-OF-PAGE.
- END-OF-SELECTION. 
- AT PF (nn)
- AT LINE-SELECTION.
- AT USER-COMMAND.
- TOP-OF-PAGE DURING LINE-SELECTION.
- Interactive Events. (User for interactive reporting)

### 报表程序大体逻辑结构
**抬  头:** 报表的主要信息 (抬头信息)。

**行项目:** 查询出的每行记录信息的关键字段。

**明  细:** 每个行项目除了关键字段外其他明细内容。

### 报表程序性能问题在业务层面的表现

**1、报表查询维度多，不同的查询维度是完全不同的查询路径**

如果大量的查询逻辑和处理代码都是不同的，就应该设计成不同的查询报表。否则将带来极大的运维成本。

**2、报表展示维度杂乱，用户希望在一个普通 ALV 中展示，导致运算处理逻辑混乱、低效**

需求不规范，一般情况下一张普通的报表就应该只有一个维度，不同维度的应该用多个报表或者树状报表展示。

**3、业务逻辑复杂，取数过程繁琐，造成大量的性能问题**

可以根据具体需求，通过增强、自定义表等方式，在不同的业务数据间建立桥梁，简化业务逻辑，简化取数过程。











