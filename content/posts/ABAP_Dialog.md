---
title: "Dialog程序简介"
date: 2018-08-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Dialog

---

### TCode

SE51：屏幕操作器

SE41：菜单编辑器

### ABAP与Dialog交互方式

ABAP程序与Dialog屏幕进行数据交换的方式，通过在程序中定义一个与Dialog中同名的全局变量或者结构，从而实现数据的自动传递。

- 加载数据：在PBO中实现

- 推送数据：在PAI中控制

- PBO:Before the screen is displayed
  
- PAI: After a user action on the screen
  
- POH: when F1
  
- POV: When F4

Screen Flow Logic定义了在screen上的各种事件，而Dynpro定义了这个Screen和各种事件。ABAP MODULE POOL会处理这些event, 在每次事件时调用一个ABAP program。

#### 语法：

- `Module  ...  END MODULE. `

- `SET screen xxxx .` 

- `CALL screen xxxx.`

- `LEAVE screen.`          
  
- `Call screen xxx   [starting at lc ur] [ending at rc lr].`   (Modal dialog box类型)
   
- `set cursor field <f> [offset <o>].  `
- `MODULE EXIT AT EXIT-COMMAND.`

#### Static Attributes：

| Attribute     | Attribute          | Attribute        |
| ------------- | ------------------ | ---------------- |
| screen-name   | screen-length      | screen-invisible |
| screen-group1 | screen-input       | screen-active    |
| screen-group2 | screen-output      |                  |
| screen-group3 | screen-required    |                  |
| screen-group4 | screen-intensified |                  |

### 字段检查与逻辑流的控制

#### 单字段检查

- 单个字段检查： `FIELD <FLD1> MODULE <MDL1> .`
- 单个字段多个MODULE检查，如FLD1有两个MODULE检查：` FIELD <FLD1> MODULE<MDL1>.
  MODULE<MDL2>.`

#### 检查多字段，使用CHAIN

```JSP
CHAIN.
  FIELD<FLD1>.
  FIELD<FLD2>,<FLD3>,<FLD4>.
  MODULE<MDL1>.
  MODULE<MDL2>.
ENDCHAIN.
表示FLD1,FLD2,FLD3,FLD4有MDL1与MDL2检查
```

#### 不是初始值检查：

`FIELD <FLD1> MODLE <MDL1> ON INPUT.`

- ON INPUT表示是初始值改变时执行.
  有一个特殊情况：` FIELD <FLD1> MODULE <MDL1> ON *-INPUT`
  表示用户输入字段首先输入'*'，并且输入字段属性，MODULE无效。

#### 有改变检查：

`FIELD<FLD1>MODULE<MDL1>ON REQUEST.`

#### CHAIN中有字段不是初始值检查

```JS
CHAIN.
  FIELD<FLD1>.
  FIELD<FLD2>,<FLD3>,<FLD4>.
  MODULE<MDL1>ON CHAIN_INPUT.
  MODULE<MDL2>.
ENDCHAIN.
注意：CHAIN_INPUT表示FLD1,FLD2,FLD3,FLD4不是初始值是执行MDL1检查。
```

PBO：`CALL SUBSCREEN <SUBAREA> INCLUDING <PROGRAM_NAME> <DYNPRO_NUMBER>.`

PAI：`CALL SUBSCREEN <SUBAREA>. `    `<program_name> = sy-cprog `: 代表本程序

ON CHANGE OF : 当指定字段修改才会去读取数据

#### EXIT-COMMAND:

- `MODULE <mod> AT EXIT-COMMAND.` 在对话屏幕中，对于E类型的Function Code触发退出屏幕，其他字段值不会传递到ABAP程序中。

- `AT SELECTION-SCREEN ON EXIT-COMMAND.`在选择屏幕中，按钮触发事件。



