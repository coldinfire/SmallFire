---
title: "ABAP Object Oriented 语法"
date: 2019-06-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

## 类声明和实现

类具有属性和方法；对象是类的实例；对象是通过指针变量来访问的；对象有独立的 **interface**。

```ABAP
"Class Declarations"
CLASS application DEFINITION.
  PUBLIC SECTION.       "公有属性定义"
    DATA: attr1.        "实例属性 obj_name->attr1"
    METHODS: show_text. "实例方法"
    EVENTS: event1.     "实例事件"
    "静态属性定义 class_name=>cla_attr1."
    CLASS-DATA: cla_attr1.    "静态属性"
    CLASS-METHODS: cls_methd. "静态方法"
    CLASS-EVENTS: cls_event.  "静态事件"
    CONSTANTS: con1.          "常量定义"
  PROTECTED SECTION.
    "定义 Protected 内容"
  PRIVATE SECTION. "定义类私有属性"
    DATA text(100) TYPE c VALUE 'This is my first ABAP Object.'.
ENDCLASS.
"Class Implementation"
CLASS application IMPLEMENTATION.
  METHOD show_text.
    WRITE text.
  ENDMETHOD.
ENDCLASS.
```

- PUBLIC SECTION：可被所有对象使用

- PROTECTED SECTION：只能被类本身及其派生类中的方法使用
- PRIVATE SECTION：只能被类本身的方法使用

### 类组成结构

#### 属性

类内表定义的数据对象。

- 实例属性：使用 DATA 定义
- 静态属性：使用 CLASS-DATA 定义，在类声明的时候定义
- CONSTANT 语句定义类常量，必须在类定义时指定其值

#### 方法

需要在类声明和实现两部分进行定义，声明部分说明方法的参数接口；实现部分通过代码完成od_1具体功能。有两中种方法类型 METHODS、CLASS-METHODS。

*方法定义*

```JS
[CLASS-]METHODS method1
  IMPORTING  VALUE(i1) / i1 TYPE type / LIKE dobj DEFAULT def1
  EXPORTING  VALUE(e1) / e1 TYPE type / LIKE dobj
  CHANGING   VALUE(c1) / c1 TYPE type / LIKE dobj DEFAULT def2
  EXCEPTIONS ... x1 TYPE ...
```

*方法调用*：调用方法传参时，除去指定为可选的参数之外，所有的参数都必须传递相应的实参值。a1 ~ an 为实际参数值。

```js
CALL METHODS [oref->|class=>]method1
  [EXPOTING i1 = a1 ... in = an]
  [IMPOTING e1 = a1 ... in = an]
  [CHANGING c1 = a1 ... in = an]
  [EXCEPTION x1 = r1 ... Other = rn]
```

### 构造器

每个类只有一个构造器，程序运行时在 Create object 语句中自动调用构造器。

- 必须在 public section 中定义和应用构造器

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

```ABAP
DATA: ob_veh1 TYPE REF TO z_cl_test.
START-OF-SELECTION.
CREATE OBJECT ob_veh1.
CALL METHOD ob_veh1->add.
```
## 面向对象的特点

### 对象创建与引用

ABAP 对象的创建和访问需要通过对象引用进行，引用类型是 ABAP 基本类型之一，其中包括"数据引用"和"对象引用"。其中对象引用又包括类引用和接口引用，对于普通类应使用类引用。

在程序中，需要先根据对象类型声明引用类型变量，然后对该变量引用 ABAP 对象，该引用变量中实际上存储的是 ABAP 对象的内在地址，因而该引用类型变量也就是普通意义上的指向对象的指针。一个引用类型变量可以不指向任何内存地址或指向一个数据对象，但一个 ABAP 对象则可以同时存在多个指向它的指针，可以通过所有这些指针对其进行操作。

```ABAP
DATA ob_test TYPE REF TO Z_CL_TEST.
CREATE OBJECT ob_test1.
WRITE: / ob_test1->method().
CALL METHOD ob_test1->method().
```

- 其中 DATA 语句创建了一个引用数据对象，该数据对象的类型定义为"指向一个类名为 Z_CL_TEST 的对象的指针"


- 定义指针之后，CREATE OBJECT 语句则创建了一个类 Z_CL_TEST 的实例，并同时将该对象的地址指针赋给引用类型 ob_test1

#### 访问对象组件

对象创建之后，可以通过指向它的指针(引用变量)对其进行操作。可以使用的对象组件一般为当前可见的属性和方法，通过引用变量后接运算符 *->* 访问对象组件。

` ->`： 即可以访问类中定义的实例组件又可以访问静态组件。但对于静态组件还有另一种访问方式：通过`类名称=>`直接访问。自身访问通过 `me`；父类引用通过 `super`。

| 描述                               | 示例                                    |
| :--------------------------------- | :-------------------------------------- |
| 访问对象的实例或则静态属性         | oref->obj_attr                          |
| 访问类的静态属性                   | class_name=>class_attr                  |
| 在类内部访问自身实例或静态属性     | me->static_attr / static_attr           |
| 对象的实例属性或静态方法           | CALL METHOD oref->meth                  |
| 类的静态方法                       | CALL METHOD class=>meth                 |
| 在类内部访问自身实例方法或静态方法 | CALL METHOD me->attr / CALL METHOD attr |
| 链式访问结构                       | oref1->oref2->comp / class=>oref->comp  |

#### 删除对象

和其他语言类似，ABAP 拥有自动垃圾回收机制。但是可以通过 CLEAR 语句初始化，触发回收机制。

#### 方法定义和使用

方法中的参数定义和传递：定义多种参数值

```JS
METHODS meth
  IMPORTING ... [VALUE(i1)|i1] TYPE type [OPTIONAL|DEFAULT def1] ...
  EXPORTING ... [VALUE(e1)|e1] TYPE type [OPTIONAL] ...
  CHANGE ... [VALUE(c1)|c1] TYPE type [OPTIONAL|DEFAULT def1] ...
  RETURNING VALUE(r)
  EXCEPTIONS ... X1 ...
```

### 继承

ABAP 所有的类都是默认继承了系统中的空类 OBJECT；ABAP 中的继承为单继承。具有一般性的类称为基类 (Superclass)，其各个子类称为派生类 (Subclass)。

在类定义时，使用 `INHERTING FROM` 附加项可以指定派生类和基类之间的继承关系。

```JS
CLASS class_2 DEFINITION INHERITING FROM class_1.
  ...
ENDCLASS.
//class_2继承自class_1
```

在继承过程中各成员的组件可见性如下:

- 一个派生类中的公有成员包括其本身公有部分定义的成员以及所有基类公有成员，这些公有成员可以通过选择运算符 "->" 在类外部获得.
- 一个派生类中的被保护成员包括其本身被保护部分定义的成员以及所有基类的被保护成员。这些成员不能通过组件选择运算符 "->" 在类外部获得， 但可以在派生类内部使用。在类外部看，其行为与类私有成员完全一致.
- 一个派生类中的私有成员***只包括其本身私有部分定义的成员***。这些成员只能在派生类内部使用.
- 因此，继承类和基类的公有成员和被保护成员享有共同的命名空间，而私有成员则在不同类之间可以出现重名情况.

### 多态

派生类通过继承基类的方法，并可以重写基类的公有或保护方法以实现方法的重载，该种情况实现了多态性。

#### 方法重载

在派生类中声明：`METHOD meth REDEFINITION.`该方法和基类方法具有相同的接口，和参数，内部可以增加不同的代码实现。

方法重载过程中，使用基类的方法可以通过***SUPER***关键字来指定基类中对应的方法。

###  抽象和 Final

#### 抽象类

一个基类可能包含多个派生类，但该基类只是作为模板出现的，并不需要有任何对象作为实例，则可以将该类声明为抽象类 (Abstract Class).

```JS
CLASS class_1 DEFINITION ABSTRACT.
  ...
ENDCLASS.
```

抽象类不能通过 CREATE OBJECT 语句创建，可以通过派生类来实现其中的模板方法并定义功能。

#### 抽象方法

`METHODS: method1 ABSTRACT.`

抽象方法不能在该类中实现，必须通过其派生类实现。含有抽象方法的类必须定义为抽象类。

#### Final 类

Final 类，不能再被继承。该类型的类主要是制定规范，一经定义不允许进行任何修改。

不需要在程序中对该类进行派生可以将其设置为最终类。

一个 Final 类的所有方法也都被视为最终的，不需要再方法中再次声明 FINAL。

Final 方法不能是抽象方法，Final 类可以是抽象类，但是这种情况下只能使用它的静态组件，不能实例化。

```JS
CLASS class1 DEFINITION FINAL.
  ...
ENDCLASS.
```

#### Final方法

`METHODS: meth FINAL.`

不可重新定义的方法，不一定出现在最终类中，但最终类中的所有方法都是最终方法。

### 接口

#### 定义与实现

Interfaces 即可以保证这些类外部看起来具有一致性，标准化的接口，又可以在不同的类内部使用不同的实现方法， 而这个具体实现过程是类外部的用户无需关心的.

接口是一个独立结构，可以在其中定义一些成员并在具体类中实现，其作用是对类中已经定义的成员进行扩展。实现接口后，接口将成为类的公有成员， 但类可以自行对接口中的方法以其自身特定形式实现。这样，对于用户来说，不同的类对象包含相同的操作接口 (即接口中定义的成员名称), 但程序内部的具体实现则根据类的不同而有所区别。接口是 OOP 中除继承之外的另一个主要多态性的实现机制技术.

一个接口可以被任意多个不同的类实现，该接口中定义的成员集在各个类中名称相同 (形成了一个标准接口), 然而各个类中成员的方法的实现方式则可以有差异， 也就形成了多态性。如果一个类中除接口之外没有定义任何类自身的公有成员方法，则接口就成了该类中的全部 "外部接口".

定义：

```JS
INTERFACE intf1.
  METHODS m1.
ENDINTERFACE.
INTERFACE intf2.
  METHODS m2.
  ALIASES meht1 FOR i1~m1.
ENDINTERFACE.
```

实现：

- 接口中的成员通过`INTERFACE~METHOD`方式访问：intf~comp。
- 在类的实现部分，必须包含所有的接口方法实现。
- 别名是用来代替接口中组件名称，在类的实现部分，通过别名来代替组件名称；在类或类的对象调用的时候，必须使用接口组件全名称。

```JS
CLASS class1 DEFINITION.
 PUBLIC SECTION.
   ...
   INTERFACES: int1, int2.
   ALIASES meth2 FOR intf2~m2.
  ...
ENDCLASS.
CLASS class1 IMPLEMENTATION.
 ...
 METHOD intf1~m1.
  ...
 ENDMETHOD.
 ...
 METHOD intf2~m2.
  ...
 ENDMETHOD.
 ...
ENDCLASS.
START-OF-SELECTION.
   DATA: oref TYPE REF TO class1.
   CREATE OBJECT oref.
   CALL METHOD oref->intf~meth1.
   CALL METHOD oref->meth2.
```



 