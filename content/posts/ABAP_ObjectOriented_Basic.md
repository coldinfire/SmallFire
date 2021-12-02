---
title: " ABAP Object Oriented 语法 "
date: 2019-06-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### 类声明和实现

类具有属性和方法；对象是类的实例；对象是通过指针变量来访问的；对象有独立的 **interface**。

#### 属性

类内定义的数据对象。

- 实例属性：使用 DATA 定义
- 静态属性：使用 CLASS-DATA 定义，在类声明的时候定义
- 常量属性：使用 CONSTANT 定义类常量，必须在类定义时指定其值

#### 方法

需要在类声明和实现两部分进行定义，声明部分说明方法的参数接口；实现部分通过代码完成方法的具体功能。有两中种方法类型：

- 实例方法：使用 METHODS 定义
- 静态方法：使用 CLASS-METHODS 定义，在类声明的时候定义

*方法定义*

```ABAP
[CLASS-]METHODS method
  IMPORTING  VALUE(i1)|i1 TYPE type|LIKE dobj DEFAULT def1
  EXPORTING  VALUE(e1)|e1 TYPE type|LIKE dobj
  CHANGING   VALUE(c1)|c1 TYPE type|LIKE dobj DEFAULT def2
  EXCEPTIONS ... x1 TYPE ...
```

*方法调用*：调用方法传参时，除去指定为可选的参数之外，所有的参数都必须传递相应的实参值。a1 ~ an 为实际参数值。

```ABAP
oref->method|class=>method(
  [EXPOTING i1 = a1 ... in = an]
  [IMPOTING e1 = a1 ... in = an]
  [CHANGING c1 = a1 ... in = an]
  [EXCEPTION x1 = r1 ... Other = rn]
).
```

#### 构造器

每个类只有一个构造器，程序运行时在 Create object 语句中自动调用构造器。

- 必须在 public section 中定义和应用构造器

#### Class Declarations

- PUBLIC SECTION：可被所有对象使用
- PROTECTED SECTION：只能被类本身及其派生类中的方法使用
- PRIVATE SECTION：只能被类本身的方法使用

```ABAP
CLASS application DEFINITION.
  PUBLIC SECTION.       "公有属性定义"
    DATA: attr1.        "实例属性 obj_name->attr1"
    METHODS: show_text. "实例方法"
    EVENTS: event1.     "实例事件"
    "静态属性 class_name=>cla_attr1."
    CLASS-DATA: cls_attr1.    "静态属性"
    CLASS-METHODS: cls_methd. "静态方法"
    CLASS-EVENTS: cls_event.  "静态事件"
    CONSTANTS: con1.          "常量定义"
  PROTECTED SECTION.
    "定义 Protected 内容"
  PRIVATE SECTION. "定义类私有属性"
    DATA text(100) TYPE c VALUE 'This is my first ABAP Object.'.
ENDCLASS.
```

#### Class Implementation

```ABAP
CLASS application IMPLEMENTATION.
  METHOD show_text.
    WRITE / text.
  ENDMETHOD.
ENDCLASS.
```

### 事件

事件用于一个类对象发布其状态的改变，因而其他对象可以捕获该方法并作出响应。

事件的工作机制：在 ABAP 中，我们使用发布和预定的机制(publish and subscribe)。类声明一个事件，通过实现一个方法来发布该事件，这样事件就和方法绑定在一起，当事件触发时就会调用该方法；另外一个类可以实现一个方法来预定这个事件。

事件的触发和处理是通过特定的方法进行的，一个方法作为触发者触发事件， 而程序中的另一个方法则作为处理者捕获并处理该事件，处理方法在事件出现进被执行。

*事件的使用*

```JS
* 声明事件
//使用 EXPORTING 附加项指定需要向事件处理方法中传递的参数，该参数传递方法恒为值传递
EVENTS|CLASS-EVENTS event
  EXPORTING ... VALUE(e1) TYPE type [OPTIONAL] ...
* 触发事件
//实例事件可以被类中的任意方法触发，静态事件则可以被静态方法触发
//参数除可选外必须传输。ME自身引用对象在被默认的传递到隐含参数 SENDER.
RAISE EVENT evt EXPORTING ... e1 = f1 ...
* 处理事件
//事件需要通过方法捕获事件并处理，必须首先为该事件定义一个事件处理方法，然后在运行时为事件进行注册.
//事件处理方法不需要使用所有 RAISE EVENT 中定义的参数
METHODS|CLASS-METHODS meth 
  FOR EVENT event OF cif IMPORTING ... ei ...
* 注册事件处理方法.
//要使事件处理方法能够对事件进行响应，必须在运行时为相关事件注册该方法，语法格式如下:
SET HANDLER ... hi ... [FOR] ...obj|ALL INSTANCES...
1.定义在类中的实例事件.
2.定义在接口中的实例事件.
3.定义在类中的静态事件.
4.定义在接口中的静态事件.
* 事件处理时间
在程序执行到 RAISE EVENT 语句之后，所有已注册的处理方法都将在下一个语句之前被处理。
如果处理方法本身触发事件，则该处理方法在原方法继续执行之前被重新调用。
```

*事件使用实例*

```ABAP
"事件触发器"
CLASS class1 DEFINITION.
  PUBLIC SECTION.
    EVENTS E1
      EXPORTING VALUE(P1) TYPE I.
    METHODS M1.
  PRIVATE SECTION.
  	DATA atr1 TYPE I.
ENDCLASS.

CLASS class1 IMPLEMENTATION.
  METHOD M1.
    atr1 = 100.
    RAISE EVENT E1 EXPORTING P1= atr1.
  ENDMETHOD.
ENDCLASS.
"事件处理器"
CLASS class2 DEFINITION.
  PUBLIC SECTION.
    METHODS: M2 FOR EVENT E1 OF class1 IMPORTING P1. 
  PRIVATE SECTION.
  	DATA atr2 TYPE I.
ENDCLASS.

CLASS class2 IMPLEMENTATION.
//M2进行事件处理
  METHOD M2.
    atr2 = P1.
    ...
  ENDMETHOD.
ENDCLASS.
```


## SAP中定义并使用类

### 定义 Class

通过事务代码：SE80 或 SE24 进入 Class Builder 进行创建类操作。

![Create](/images/ABAP/OO1.png)

- 创建时选择限制范围
- Final 勾选代表该类不能被继承

![Attribute](/images/ABAP/OO2.png)

- 设置属性时，和定义变量一样，可以设置其等级，类型，可见范围
- Read-Only：选中时该属性不能被类外部读取，可被类内方法修改

![Method](/images/ABAP/OO3.png)

- 方法可以设置传入参数，传出参数以及异常消息
- 方法内部可使用ABAP实现具体的业务逻辑

### 使用创建的 Class

#### 使用步骤

1. 程序开头部分定义类的声明和实现方法；

2. 使用 DATA 语句中的 TYPE REF TO 参照类类型声明引用变量；

3. 使用 CREATE OBJECT 语句创建对象；

4. 通过 -> 或 => 运算符访问对象或类组件；

#### 使用实例

CALL METHOD 已经是不推荐使用的 Statement，使用 `class->method( ).`。

```ABAP
DATA: go_veh1 TYPE REF TO z_cl_test.
START-OF-SELECTION.
CREATE OBJECT go_veh1.
*CALL METHOD go_veh1->add.
go_veh1->add( ).
```


