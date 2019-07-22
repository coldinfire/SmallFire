---
title: "OO语法"
date: 2018-07-27T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - 报表开发

---



### 类声明和实现

```js
*Class Declarations
CLASS application DEFINITION.
  PUBLIC SECTION.
    METHODS: show_text.
  PRIVATE SECTION.
    DATA text(100) TYPE c VALUE 'This is my first ABAP Object.'.
ENDCLASS.

*Class Implementation
CLASS application IMPLEMENTATION.
  METHOD show_text.
    WRITE text.
  ENDMETHOD.
ENDCLASS.
```

- PUBLIC SECTION：可被所有对象使用

- PROTECTED SECTION：只能被类本身及其派生类中的方法使用
- PRIVATE SECTION：只能被类本身的方法使用

#### 组成结构

- 属性：类内表定义的数据对象。

  - 实例属性：使用DATA定义
  - 静态属性：使用CLASS-DATA定义，在类声明的时候定义
  - CONSTANT 语句定义类常量，必须在类定义时指定其值.

- 方法

  - 需要在类声明和实现两部分进行定义，声明部分说明方法的参数接口；实现部分通过ABAP代码完成具体功能
  - METHODS;CLASS-METHODS

  ```JS
  METHODS meth
    IMPORTING ... i1 TYPE ...
    EXPORTING ... e1 TYPE ...
    CHANGING ... c1 TYPE ...
    EXCEPTIONS ... x1 TYPE ...
  ```

- 事件

  - 

### SAP中定义系统类

通过TCode：SE80或则SE24进入Class Builder进行创建类

![](/images/ABAP/OO1.png)

- 创建时选择限制范围
- Final勾选则该类不能被继承