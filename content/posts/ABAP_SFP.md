---
title: " SAP Adobe Form "
date: 2020-04-09 
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  ABAP

tags: 
  - abaputils

---

## Adobe Form 介绍

SAP中关于Form的设计有三种工具：Script Form，Smart Form和Adobe Form.

要在SAP系统中展示Adobe Form需要SAP版本在 **ECC6.0以上** ，而且要开发自定义的Adobe Form还需要在PC上安装 **Adobe Life Cycle Desinger** 的工具。在满足以上两个条件后就可以开发自己想要使用的Adobe Form。

Adobe Form相对于Smartforms可以让Form程序的开发设计更简单：

- Adobe Live Cycle 直接集成在SAP中，画Form更方便
- 图片处理更方便
- 可以导入既有的PDF或则Word
- Layout 可以重用
- 字体支持更加强大
- Barcode等使用更加方便

## 设计Adobe Form

Form设计好的方法：

- 尽量不要只用一个Master Page去管理整个Form(除非非常简单)
- 在使用嵌套subform的时候注意，不要嵌套在不同页的两个subform
- 页面有不同的内容就添加一个body page，而不要选择goto
- Keep with prev ones 和 Keep with next仅使用于普通的subform而不适用于body page

设计Adobe From时使用的主要设计工具

### Interface

这里的 Interface 的概念其实比较好解释，它就是相当于一个后台数据的接口，可以为前台各种 Adobe Form 提供对应的数据。不同形式的Adobe Form可以调用相同的Interface。

在Interface中可以定义一些参数和进行一些数据处理。

#### **接口类型** : 不同的接口类型适用于不同业务场景

![Interface](/images/ABAP/ABAP_SFP3.png)

- ABAP Dictionary-Based Interface：ABAP程序调用
- Smart Forms-Compatible Interface：兼容Smart Form
- XML Schema-Based Interface：Web Dynpro 调用
  - 对于输入参数，不能更改，只有一个XSTRING类型的参数(除却标准的Print Parameter)
  - XML Schema: 这个一般是自动生成，也可以选择手工上传一个 **XSD** 文件

#### 接口参数

![Interface](/images/ABAP/ABAP_SFP1.png)

*传递参数*

注意：参数定义用TYPE,只有兼容Smart Form的接口类型才能用LIKE。

- Import
- Export
- Tables (Smart Form Type)
- Exceptions

*全局变量* : 定义Form中需要使用到的全局变量

- Global Data
- Types
- Field Symbols

*初始化*

- Code Initialization：如果不是Smart Form兼容的接口类型，那么这里是你唯一可以写ABAP代码的地方，在未真正调用Form之前执行

- Form Routines：这里可以写子程序供Code初始化调用

*货币和单位字段的设置* : 通过这里的设置，决定了表单中数字或货币的显示方式

### Form

#### Form属性

![Form properties](/images/ABAP/ABAP_SFP5.png)

Layout Type : 需要使用脚本做交互式报表时使用

- ZCI Layout：对Adobe Reader的版本有要求，note 号：955795
- Standard Layout：标准的Layout type
- xACF Layout：现在基本使用ZCI而不是使用xACF

Interface : 需要调用的接口名字

#### Context

在这里使用Interface 中定义的内容，可以有选择的使用。在Context中也可以自己添加一些文本模块或则图像。

![Context](/images/ABAP/ABAP_SFP4.png)

*1、Interface* : 包括我们设计接口的字段以及系统字段(System Fields)

*2、Interface Property* : 接口字段的属性

*3、Context Property* : Context字段属性

*4、Context* : 在Form中用到的字段列表

![Context](/images/ABAP/ABAP_SFP7.png)

Context主工作区可以创建各种元素类型，也可以决定是否激活某个元素(Set to Active/Deactivate)

#### Layout

Layout 进行Form主体的绘制，主要在于控件的应用，它可以决定该 Form 的整体格式与相关的页面设置 (翻页设置，页面结构，页码，页边间距等等)。其中最关键的是数据绑定的内容，在Layout进行数据绑定就可以将Interface中的数据展示到该Form中对应的field中。

- 静态元素控件
  - 图片容器
  - 文本
  - 几何图形
- 动态元素控件：引用自Context
  - Text字段
  - 图像
  - 数量字段
  - 时间和日期字段
- Table
  - 动态表：打印表的行数在设计时未知
  - 静态表：设计时确定记录数据内容

![Context](/images/ABAP/ABAP_SFP6.png)

**Live Cycle 设计器**  ： 主要设计内容 Master Page、Context Area、Body Pages、SubForms。

![Context](/images/ABAP/ABAP_SFP10.png)

Master Pages:主页面，可以放置一些静态元素，在每个打印页面都可以看到这些静态元素

- Name:Master page 名字
- Paper type:纸张类型
- Restrict Page Occurrence:打印的页数

Content Area:放置动态元素，例如表内容等。Context Area只能包含在Master Page中

Body Page:包含在Context Area之中，是动态元素的内容，顶级的Subform

Subforms:用来组织Form元素，使Form的设计逻辑更加清晰



