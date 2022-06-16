---
title: " BADI 维护 "
date: 2019-07-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Enhance

---

### BADI 基于类的增强

**BADI 是基于面向对象的 SAP 增强技术**：SAP 预定义了 Interface，由客户来实例化相应的接口并重写其方法，应用程序通过调用客户所定义 class 的 instance 来实现增强效果。

BADI 通过事务码 SE18、SE19、SE24 来维护。

- SE18：用于创建及维护 BADI 对象。创建增强点、维护接口/类（Interface）、维护方法，维护方法的参数、维护实施 Implementation。
- SE19：用于维护 BADI 对象的实例，BADI 功能的实现。
- SE24：查看 CLASS Interface、查找相关事务代码的BADI增强出口。

#### BADI 分类

- Classic BADI ：在运行时进行实例化，old BADI。
- Kernel BADI ：在编译时进行实例化，new BADI。

Classic BADI 和 new BADI 的不同：

- BADI Object
  - 对于 Classic BADI，BADI 对象是通过调用工厂方法创建的，并通过 BADI 接口类型的引用变量进行引用。
  - 对于 New BADI，通过 ABAP 语句 `GET BADI` 创建一个 BADI 对象作为 BADI 方法调用的句柄，并通过 BADI 类型的引用变量进行引用。 BADI 对象是内部 BADI 类的实例，否则对外部是不可见的。
- 为过滤器传递比较值
  - 对于 Classic BADI，过滤器值存储在一个结构中，并通过 BADI 方法的调用传递。
  - 对于 New BADI，过滤器的比较值在使用 GET BADI 创建 BADI 对象时传递。
- 调用 BADI 方法
  - Classic BADI 只能调用一次，调用位置集中注册。
  - New BADI 可以进行多次调用，调用位置不会集中注册。

#### 命名规则

- Enhancement Spot：`Z_ES_<Spot_name>`

- BADI definition: `ZBADI_<BADI_name>`

- Interface: `ZIF_EX_<BADI_name>`

- BADI implementation：`Z<impl>`

- Implementing class：`ZCL_IM_<impl>`

#### 信息存储表

- SXS_INTER：Exit, Definition side (Interfaces)
- SXC_EXIT：Exit, Implementation side (Assignment: Exit - Implementation)
- SXC_CLASS：Exit, Implementation side (Class assignment (multiple))
- SXC_ATTR：Exit, Implementation side (Attributes)

### Classic BADI

#### BADI Create

对于 Classic BADI，如果直接通过 SE18 创建会报错， 定义方法：

-  **SE18 --> Utilities --> Create Classic BADI** 

![Create Classic BADI](/images/ABAP/BADI_01.png)

![Definition name](/images/ABAP/BADI_02.png)

选项控制：

![Definition Attributes](/images/ABAP/BADI_03.png)

- Program 增强：通过 interface methods 来实现的，SAP 程序来调取生成的 BADI class 的 interface methods。
- Menu 增强：同 customer exit 一样，可以在 BADI 中维护 function code。这些 menu entry 在 GUI definition 中定义后就可以在 implemented BADI 看见。
- Screen 增强：同 customer exit 一样，可以在 BADI 中维护 Screen enhancements。

SAP BADI 的使用分类：

- 非 Multiple use 的 BADI 同时只能有一个 Active Implementation，要 Active 新生成的需要先 Inactive 旧的。
- Multiple use 的 BADI 则可同时有多个 Active Implementation，且所有的 Implementation 在没有  Filter 的情况下都会被遍历执行。

#### Definition Interface

双击 Interface Name 创建接口，创建完成后可以定义接口相关属性和方法。

![Definition Interface](/images/ABAP/BADI_04.png)

#### Create Implementation

BADI 是使用面向对象语言的接口技术，增强其实就是实现 BADI 接口的方法，然后在实现的方法内写业务代码。

- **SE19** 选择 Create Implementation 中的 Classic BADI  输入 Implementation name：`Z_<impl>`

![Create Impl](/images/ABAP/BADI_05.png)

创建实现后，自动生成实现类：`ZCL_IM_<impl>`。在实现类中根据业务需求，实现接口中的方法。

![Class Method](/images/ABAP/BADI_08.png)

查看 BADI 的 Implementing Class：

![BADI Overview](/images/ABAP/BADI_07.png)

![BADI Overview](/images/ABAP/BADI_06.png)

#### BADI 调用

Classic BADI 通过 **CL_EXITHANDLER=>GET_INSTANCE** 来获取实例，然后通过实例来调用 Interface 中的方法。

```ABAP
REPORT ZBADI_TEST1.
DATA: l_badi_instance TYPE REF TO zif_ex_badi_test. "REF TO Interface接口"
DATA: lv_imp_exist TYPE c.
DATA: out TYPE string.
START-OF-SELECTION.
  CALL METHOD cl_exithandler=>get_instance
    EXPORTING
      exit_name        = 'ZBADI_TEST'
    IMPORTING
      act_imp_existing = lv_imp_exist
    CHANGING
      instance         = l_badi_instance.
  IF lv_imp_exist IS NOT INITIAL.
    l_badi_instance->test(
      EXPORTING
       in = 'hello'
      IMPORTING
        out = out ).
    WRITE: / out.
  ENDIF.
```

#### Menu 增强

同 customer exit 一样 BADI 中也有 Menu enhancements，如果相应的 enhancement 的 badi implementation 被激活，那么 menu 就会显示出来。但是必须满足以下两个条件：

- 必须预留了menu enhancement
- 必须在 BADI 的 implementation 中实现

Menu enhancements 的 function codes 必须以`+`开始。如果用户在程序中选择了相应的以 + 开始的 function code，那么系统就会调用相应的 method。

只能为 single use badi 创建 function code，而且 BADI 不能是 filter dependent。这样就保证了一个或多个 BADI 不出现矛盾。

Method call 和 Menu enhancement 是不可分离的，它们只能属于同一个 BADI。

### Kernel BADI

对于 Kernel BAdI，通过 Enhancement Spot 进行创建，也即：先创建 Enhancement Spot，然后在 Enhancement Spot 内部创建 BADI。

Enhancement Spot 是作为一个 BADI 的容器，一个 Enhancement Spot 下可以创建多个 BADI  Definition，每个 BADI  Definition 由一个 Interface 与多个 Enhancement Implementation 组成，每个 Enhancement Implementation 里又可以创建多个 BADI Implementation，每个 BADI Implementation 里可以创建一个 Implementing Class。

#### 创建 Enhancement Spot

![Create Enhancement Spot](/images/ABAP/BADI_11.png)

在新建立的 Enhancement spot 中创建 BADI  Definition。

![Create BADI Definition](/images/ABAP/BADI_12.png)

定义 BADI 时，默认采用的是 Multiple Use，取消后只能单一使用(single-use：只能有一个实现) 。

![Enhancement Spot Detail](/images/ABAP/BADI_13.png)

双击 Interface，可以直接创建：

![Interface](/images/ABAP/BADI_14.png)

定义 Interface 的 Attributes、Methods 等内容：

![Interface](/images/ABAP/BADI_15.png)

 激活 interface 和 Enhancement spot，BADI - **ZBADI_DEMO1** 创建完成。

#### BADI 的实现

由于一个 BADI 的实现可以有多个类，这些实现类需要组织在一起（与多个 BADI 放在一个 Enhancement Spot 容器中是相同的概念），所以需要先创建一个 BADI 增强实现容器：(Enhancement Implementation)

右键 Create BADI Implementation：

![Create BADI Implementation](/images/ABAP/BADI_16.png)

创建 Enhancement Implementation：ZBADI_DEMO_IMP

![Enhancement Implementation](/images/ABAP/BADI_17.png)

然后弹出创建 BADI Implementation 对话框：

![BADI Implementation](/images/ABAP/BADI_18.png)

一个增强实现（Enhancement Implementation）可以有多个 BADI Implementation（相当于多个版本），但起作用的 BADI Implementation 同时只能有一个，有多个版本时需要进行设置：

![Enhancement Implementation Detail](/images/ABAP/BADI_19.png)

每个 BADI Implementation 只与一个且仅一个 Implementing Class 对应：

![Implementing Class](/images/ABAP/BADI_20.png)

双击 Implementing Class 实现接口方法：

![Implementing Class](/images/ABAP/BADI_21.png)

在实现方法中实现具体的业务并激活：

![Implementing Class Method](/images/ABAP/BADI_22.png)

如果想要达到像 Java 中多态的话，需要创建多个不同的 Enhancement Implementation 增强实现，BADI 中的多态就是通过不同的 Enhancement Implementation 增强实现来实现的。

![Polymorphism](/images/ABAP/BADI_26.png)

#### SE19 操作 New BADI

Create Implementation

- 输入 Enhancement Spot，点击 Create Impl 并输入 Enhancement Implementation 名称

  ![Create Implementation](/images/ABAP/BADI_23.png)

- 定义 BADI Implementation、Implementation Class 选择 BADI Definition，点击确定：

  ![Choose Detail](/images/ABAP/BADI_24.png)

- 在实现方法中实现具体的业务并激活：

  ![Implementing Class](/images/ABAP/BADI_25.png)

Edit Implementation

- 输入 Enhancement Implementation 即可查找包含的 BADI Implementations

  ![Edit Implementation](/images/ABAP/BADI_09.png)

- 显示结果

  ![Edit Implementation](/images/ABAP/BADI_10.png)

#### 调用

Kernel BADI 通过 **GET BADI** 来获取实例，并调用 **CALL BADI** 来调用 interface 中的方法。

```ABAP
REPORT ZBADI_TEST2.
DATA: lo_badi_demo TYPE REF TO ZBADI_DEMO, "ZBADI_DEMO为BADI定义名,不是接口也不是类"
      out TYPE string.
GET BADI lo_badi_demo.
IF lo_badi_demo IS BOUND.
  CALL BADI lo_badi_demo->test
    EXPORTING
      in = 'hello'
    IMPORTING
       out = out.
  WRITE: / out.
ENDIF.
```



### 参考文档

- [https://www.cnblogs.com/jiangzhengjun/p/4265513.html#_Toc410467160](https://www.cnblogs.com/jiangzhengjun/p/4265513.html#_Toc410467160)