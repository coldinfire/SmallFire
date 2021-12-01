---
title: " ABAP Object Oriented 特性 "
date: 2019-06-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

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

| 描述                               | 示例                                   |
| :--------------------------------- | :------------------------------------- |
| 访问对象的实例或则静态属性         | oref->obj_attr                         |
| 访问类的静态属性                   | class_name=>class_attr                 |
| 在类内部访问自身实例或静态属性     | me->static_attr / static_attr          |
| 对象的实例属性或静态方法           | oref->meth                             |
| 类的静态方法                       | class=>meth                            |
| 在类内部访问自身实例方法或静态方法 | me->attr / attr                        |
| 链式访问结构                       | oref1->oref2->comp / class=>oref->comp |

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

```ABAP
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

```ABAP
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

