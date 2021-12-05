---
title: " Dialog 逻辑控制 "
date: 2018-07-06
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
  - ` FIELD <FLD1> MODULE<MDL1>. MODULE<MDL2>.`

#### 检查多字段，使用 CHAIN

```ABAP
CHAIN.
  FIELD<FLD1>.
  FIELD<FLD2>,<FLD3>,<FLD4>.
  MODULE<MDL1>.
  MODULE<MDL2>.
ENDCHAIN.
"表示FLD1,FLD2,FLD3,FLD4有MDL1与MDL2检查"
```

#### 其它检查

不是初始值检查 `FIELD <FLD1> MODLE <MDL1> ON INPUT.`

- ON INPUT表示是初始值改变时执行.
  有一个特殊情况：` FIELD <FLD1> MODULE <MDL1> ON *-INPUT`
  表示用户输入字段首先输入'*'，并且输入字段属性，MODULE无效。

有改变检查：`FIELD<FLD1>MODULE<MDL1>ON REQUEST.`

#### CHAIN 中有字段不是初始值检查

注意：CHAIN_INPUT 表示FLD1、FLD2、FLD3、FLD4不是初始值是执行 MDL1 检查。

```ABAP
CHAIN.
  FIELD<FLD1>.
  FIELD<FLD2>,<FLD3>,<FLD4>.
  MODULE<MDL1>ON CHAIN_INPUT.
  MODULE<MDL2>.
ENDCHAIN.
```

PBO：`CALL SUBSCREEN <SUBAREA> INCLUDING <PROGRAM_NAME> <DYNPRO_NUMBER>.`

PAI：`CALL SUBSCREEN <SUBAREA>. `    `<program_name> = sy-cprog `: 代表本程序

ON CHANGE OF : 当指定字段修改才会去读取数据

#### EXIT-COMMAND

- `MODULE <mod> AT EXIT-COMMAND.` 在对话屏幕中，对于E类型的Function Code触发退出屏幕，其他字段值不会传递到ABAP程序中。

- `AT SELECTION-SCREEN ON EXIT-COMMAND.`在选择屏幕中，按钮触发事件。

