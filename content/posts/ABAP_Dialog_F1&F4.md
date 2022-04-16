---
title: " Dialog POH&POV "
date: 2018-09-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Dialog

---

### POH：F1 帮助

当按下 F1 时，就会执行指定数据元素的 POH 事件。

如果屏幕的流程逻辑中不存在 PROCESS ON HELP-REQUEST 事件，则以 ABAP Dictionary 中该字段的文档为基础进行显示。 

#### 定义

要显示字段帮助文档，您必须在 POH 事件中编写以下屏幕流逻辑：

`PRODESS ON HELP-REQUEST FIELD <field> [MODULE <mod>] WITH <num>.`

- 注意 FIELD 语句不会将屏幕字段 field 的内容传输到 PROCESS ON HELP-REQUEST 事件中的 ABAP 程序。 它只显示帮助文档。
- 如果 field 有特定于屏幕的数据元素文档，可以通过指定其编号 num 来显示它。

- num 可以是文字或变量。该变量必须在相应的 ABAP 程序中进行声明和填充。

- MODULE 像普通的 PAI 模块一样在 ABAP 程序中定义。 模块的处理逻辑必须确保为相关字段显示足够的帮助。 

#### 使用 FM 显示 POH 内容

`HELP_OBJECT_SHOW_FOR_FIELD`

- 此功能模块显示来自 ABAP 词典的任何结构或数据库表的组件的数据元素文档。
- 您将组件和结构或表的名称传递给导入参数 FIELD 和 TABLE。

`HELP_OBJECT_SHOW`

- 使用此功能模块可以显示任何 SAPscript 文档。
- 您必须将文档类（例如，TX 表示一般文本，DE 表示数据元素文档）和文档名称给导入参数 DOKCLASS 和 DOKNAME。
- 出于技术原因，您还必须将行类型为 TLINE 的空内部表传递给功能模块的表参数。

### POV：F4 帮助

当用户选择功能 F4 时，系统会开始处理事件 PROCESS ON VALUE-REQUEST 显示字段的可能输入值（值、检查表、匹配代码），前提是它们由开发人员存储。

#### 定义

要为屏幕上的字段定义可能的值，需要在屏幕流逻辑的 POV 事件中定义以下内容：

`PROCESS ON VALUE-REQUEST. FIELD <field>  MODULE <module>.`

- FIELD：指定为哪个字段设置 F4 帮助
- MODULE：为字段的取值添加程序逻辑

#### 函数使用

`F4IF_FIELD_VALUE_REQUEST`：动态调用 ABAP 字典的输入帮助。

- 可以在输入参数 TABNAME 和 FIELDNAME 中将 ABAP Dictionary 的结构或数据库表的组件名称传递给 FM。
- 该功能模块启动该组件的 ABAP 字典输入帮助。读取所有相关屏幕字段。
- 如果指定输入参数 DYNPPROG、DYNPNR 和 DYNPROFIELD，则用户的选择将返回到屏幕上的相应字段。
- 如果指定表参数 RETURN_TAB，则选择将返回到表中。

```ABAP
MODULE VALUE_CARRIER INPUT.

  CALL FUNCTION 'F4IF_FIELD_VALUE_REQUEST'
    EXPORTING
      TABNAME     = 'DEMOF4HELP'
      FIELDNAME   = 'CARRIER1'
      DYNPPROG    =  PROGNAME
      DYNPNR      =  DYNNUM
      DYNPROFIELD = 'CARRIER'
    TABLES
      RETURN_TAB  =  RETURN_TAB.
ENDMODULE.
```

`F4IF_INT_TABLE_VALUE_REQUEST`：显示在 ABAP 程序中创建的值列表。

- 值列表作为表参数 VALUE_TAB 传递给功能模块。
- 如果指定导入参数 DYNPPROG、DYNPNR 和 DYNPROFIELD，则用户的选择将返回到屏幕上的相应字段。
- 如果指定表参数 RETURN_TAB，则选择将返回到表中。

```ABAP
CALL FUNCTION 'F4IF_INT_TABLE_VALUE_REQUEST'
  EXPORTING
    RETFIELD     = 'CONNID'
    DYNPPROG     =  PROGNAME
    DYNPNR       =  DYNNUM
    DYNPROFIELD  = 'CONNECTION'
    VALUE_ORG    = 'S'
  TABLES
    VALUE_TAB    = VALUES_TAB
    RETURN_TAB   = RETURN_TAB.
```

