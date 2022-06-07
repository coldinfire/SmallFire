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

BADI 维护是通过 SE18、SE19、SE24 事务来来维护的。

- SE18：用于创建及维护 BADI 对象，定义接口功能，查看 BADI 的相关属性
- SE19：用于维护 BADI 对象的实例，创建实现类并编码，查看 BADI 的相关实现
- SE24：使用SE24 查看 CLASS Interface

**BADI 是基于 SAP 面向对象的 SAP 增强技术** ：SAP 预定义了 Interface，由客户来实例化相应的接口，应用程序通过调用来获得用户所定义 class 的 instance。

#### BADI 分类

- Classic BADI ：在运行时进行实例化，old BADI
- Kernel BADI ：在编译时进行实例化，new BADI

#### 命名规则

- Enhancement Spot：`Z_ES_<Spot_name>`

- BADI definition: `ZBADI_<BADI_name>>`

- Interface: `ZIF_EX_<BADI_name>`

- BADI implementation：`Z_<impl>`

- Implementing class：`ZCL_IM_<impl>`

#### 信息存储表

- SXS_INTER：Exit, Definition side (Interfaces)
- SXC_EXIT：Exit, Implementation side (Assignment: Exit - Implementation)
- SXC_CLASS：Exit, Implementation side (Class assignment (multiple))
- SXC_ATTR：Exit, Implementation side (Attributes)

### Classic BADI

对于 Classic BADI， 其定义是通过 **SE18 --> Utilities --> Create Classic BADI** 来进行的。

![SE18 Create Classic BADI](/images/ABAP/BADI_01.png)

如果直接通过 SE18 创建老版本的BADI会报错。

#### Definition Attributes

![Definition Attributes](/images/ABAP/BADI_02.png)

选项控制：

![Definition Attributes](/images/ABAP/BADI_04.png)

#### Definition Interface

双击 Interface Name 创建接口，创建完成后可以定义接口相关属性和方法。

![Definition Interface](/images/ABAP/BADI_03.png)

#### Create Implementation

![Create Implementation](/images/ABAP/BADI_05.png)

创建实现后，自动生成实现类：ZCL_IM_CL_IM_XXX

![Create Implementation](/images/ABAP/BADI_06.png)

#### BADI 调用

Classic BADI 通过 **CL_EXITHANDLER=>GET_INSTANCE** 来获取实例，然后通过实例来调用Interface 中的方法。

```ABAP
DATA: out TYPE string.
DATA: l_badi_instance TYPE REF TO ZIF_EX_BADI_TEST. "REF TO:Interface接口名"
CALL METHOD cl_exithandler=>get_instance
  CHANGING instance = l_badi_instance.
IF l_badi_instance IS NOT INITIAL.
  CALL METHOD l_badi_instance->test
    EXPORTING
      in      = 'hello'
    CHANGING
      out     = out.
  WRITE: / out.
ENDIF.
```

### Kernel BADI

对于 Kernel BAdI，通过 Enhancement Spot 进行创建，也即：先创建 Enhancement Spot，然后在 Enhancement Spot 内部创建 BADI。

Enhancement Spot 是作为一个 BADI 的容器，一个 Enhancement Spot 下可以创建多个 BADI  Definition，每个 BADI  definition 由一个 Interface 与多个增强实现组成，而每个增强实现里又可以创建多个 BADI 实现，而每个 BADI 实现里可以创建一个实现类。

#### 创建 Enhancement Spot

![Enhancement Spot](/images/ABAP/BADI_11.png)

![Enhancement Spot](/images/ABAP/BADI_12.png)

在新建立的 Enhancement spot 中创建BADI：

![Enhancement Spot](/images/ABAP/BADI_13.png)

定义 BADI 时，默认采用的是单一使用(single-use)，如果没有选中复合使用选项(Multiple Use)，单一使用的限制是只能有一个实现。

![Enhancement Spot](/images/ABAP/BADI_14.png)

双击 Interface，可以直接创建接口：

![Interface](/images/ABAP/BADI_15.png)

定义 Interface 的相关属性、方法：

![Interface Method](/images/ABAP/BADI_16.png)

 激活 interface 和 Enhancement spot, BAdI - **ZBADI_DEMO1** 创建完成。

#### BADI 的实现

由于一个 BADI 的实现可以有多个类，这些实现类需要组织在一起（与多个 BADI 放在一个 Enhancement Spot 容器中是相同的概念），所以需要先创建一个新的 BADI 增强实现容器：(Enhancement Implementation)

右键 Create BADI Implementation：

![Create BADI Implementation](/images/ABAP/BADI_17.png)

先要创建 Enhancement Implementation：

![Enhancement Implementation](/images/ABAP/BADI_18.png)

然后创建 BADI Implementation：

![BADI Implementation](/images/ABAP/BADI_19.png)

一个增强实现（Enhancement Implementation）可以有多个 BADI Implementations（相当于多个版本，每个 BADI Implementations 只与一个且仅一个实现类对应），但起作用的 Implementations 同时只能有一个，有多个版本时需要进行设置：

![Enhancement Implementation](/images/ABAP/BADI_21.png)

如果想要达到像 Java 中多态的话，需要创建多个不同的 Enhancement Implementation 增强实现，BADI 中的多态就是通过不同的 Enhancement Implementation 增强实现来实现的。

#### SE19 实现 BADI

Edit Implementation：输入 Enhancement Implementation 即可查找包含的 BADI Implementations

![Enhancement Spot](/images/ABAP/BADI_20.png)

Create Implementation：输入 Enhancement Spot，点击创建实现 Create Implementation

![Enhancement Spot](/images/ABAP/BADI_22.png)

定义相关的 BADI Implementation和相关的实现类，点击确定：

![Enhancement Spot](/images/ABAP/BADI_23.png)

双击实现类的名称，创建实现类。

#### 调用

```ABAP
parameters: filter(2) type c.
DATA: handle TYPE REF TO ZBADI_DEMO1, "ZBADI_DEMO1为BADI定义名,不是接口也不是类"
      sum TYPE p,
      vat TYPE p,
      percent TYPE p.
sum = 50.
GET BADI handle.
CALL BADI handle->get_vat
  EXPORTING
    im_amount      = sum
  IMPORTING
    ex_amount_vat  = vat
    ex_percent_vat = percent.
```



### 参考文档

- [https://www.cnblogs.com/jiangzhengjun/p/4265513.html#_Toc410467160](https://www.cnblogs.com/jiangzhengjun/p/4265513.html#_Toc410467160)