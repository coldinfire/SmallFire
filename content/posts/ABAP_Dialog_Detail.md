---
title: " Dialog 逻辑控制 "
date: 2018-07-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Dialog

---

### Dialog 屏幕操作

#### 语法

- `Module  ...  END MODULE. `

- `SET screen xxxx .` 

- `CALL screen xxxx.`

- `LEAVE screen.`          

- `CALL screen xxx [starting at lc ur] [ending at rc lr].`   (Modal dialog box类型)

- `SET CURSOR FIELD <f> [offset <o>].  `
- `MODULE EXIT AT EXIT-COMMAND.`

#### Static Attributes

| Attribute     | Attribute       | Attribute          | Attribute     | Attribute     | Attribute     |
| ------------- | --------------- | ------------------ | ------------- | ------------- | ------------- |
| screen-name   | screen-active   | screen-invisible   | screen-input  | screen-group1 | screen-group3 |
| screen-length | screen-required | screen-intensified | screen-output | screen-group2 | screen-group4 |

### 字段检查与逻辑流的控制

#### 单字段检查

- 单个字段检查： `FIELD <FLD1> MODULE <MDL1> .`
- 单个字段多个 MODULE 检查，如 FLD1 有两个 MODULE 检查：
  - ` FIELD <FLD1> MODULE <MDL1>. MODULE <MDL2>.`

#### 检查多字段，使用 CHAIN

```ABAP
CHAIN.
  FIELD <FLD1>.
  FIELD <FLD2>,<FLD3>,<FLD4>.
  MODULE <MDL1>.
  MODULE <MDL2>.
ENDCHAIN.
"表示FLD1,FLD2,FLD3,FLD4有MDL1与MDL2检查"
```

#### 其它检查

不是初始值检查： `FIELD <FLD1> MODLE <MDL1> ON INPUT.`

- ON INPUT表示是初始值改变时执行。
  - 有一个特殊情况：` FIELD <FLD1> MODULE <MDL1> ON *-INPUT`
    表示用户输入字段首先输入'*'，并且输入字段属性，MODULE无效。

有改变检查：`FIELD <FLD1> MODULE <MDL1> ON REQUEST.`

#### CHAIN 中有字段不是初始值检查

- CHAIN_INPUT： 表示FLD1、FLD2、FLD3、FLD4 不是初始值时执行 MDL1 检查

- ON CHAIN_REQUEST：当指定字段修改才会去读取数据

```ABAP
CHAIN.
  FIELD <FLD1>.
  FIELD <FLD2>,<FLD3>,<FLD4>.
  MODULE <MDL1> ON CHAIN_INPUT.
  MODULE <MDL2> ON CHAIN_REQUEST.
ENDCHAIN.
```

#### EXIT-COMMAND

- `MODULE <mod> AT EXIT-COMMAND.` 在对话屏幕中，对于 E 类型的 Function Code 触发退出屏幕，其他字段值不会传递到 ABAP 程序中。

- `AT SELECTION-SCREEN ON EXIT-COMMAND.`在选择屏幕中，按钮触发事件。

### CALL Subscreen

子屏幕是显示在另一个（“主”）屏幕区域中的独立屏幕。子屏幕允许在运行时将一个屏幕嵌入到另一个屏幕中。 您可以在主屏幕上包含多个子屏幕。

子屏幕既适用于您嵌入的屏幕，也适用于您放置它的主屏幕上的区域。 如果在屏幕属性中定义，则通过 SE51 事务创建的实际屏幕称为子屏幕屏幕。

当使用子屏幕时，嵌入屏幕的流逻辑也嵌入到主屏幕的流逻辑中。因此，在屏幕上使用子屏幕就像在 ABAP 程序中使用包含一样。每当调用主屏幕时，都会调用主屏幕的 PBO。但是在显示之前，还调用了主屏幕上附有子屏幕区域的每个屏幕的PBO。

步骤

- 定义屏幕上的子屏幕区域
- 定义合适的子屏幕
- 在子屏幕区域中包含子屏幕屏幕

#### 使用子屏幕

可以在主屏幕的流逻辑中使用 CALL SUBSCREEN 语句包含子屏幕屏幕。

要在主屏幕的子屏幕区域中包含一个子屏幕屏幕并调用其 PBO 流程逻辑，请在主屏幕的 PBO 事件中使用以下语句：

- `CALL SUBSCREEN <subarea> INCLUDING [<prog>] <dynp>.`
- `<prog> = sy-cprog `：代表本程序

调用子屏幕的 PAI 流逻辑，在主屏的PAI流逻辑中使用如下语句：

- `CALL SUBSCREEN <subarea>.`

#### 注意事项

- 一个屏幕内的子屏幕元素的名称应该是唯一的
- 不应该将 OK_CODE 或 FCODE 附加到子屏幕。主屏本身的 OK_CODE 就是副屏的 OK_CODE
- 子屏幕不能有任何包含 SET TITLEBAR、SET PF-STATUS、SET SCREEN、LEAVE SCREEN 或 LEAVE TO SCREEN 的对话模块，这将导致运行时错误
- 子屏幕需要在主屏幕的流逻辑（PBO 和 PAI）中调用
- CHAIN..ENDCHAIN 和 LOOP ENDLOOP 语句中不允许 CALL SUBSCREEN
- 不能有 AT EXIT-COMMAND 模块
- 使用的字段是全局字段时必须先在顶部声明
- 如果使用来自另一个对话程序的子屏幕，除非您添加特定代码，否则不会发生数据传输。