---
title: " SAP Adobe Form "
date: 2020-04-09 
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

SAP 中关于 Form 的设计有三种工具：Script Form，Smart Form 和 Interactive Form(Adobe Form).

要在 SAP 系统中展示 Adobe Form 需要 SAP 版本在 **ECC6.0以上** ，而且要开发自定义的 Adobe Form 还需要在 PC 上安装  **Adobe Life Cycle Desinger**  的工具。在满足以上两个条件后就可以开发自己想要使用的 Adobe Form。

Adobe Form 相对于 Smartforms 可以让 Form 程序的开发设计更简单：

- Adobe Live Cycle 直接集成在 SAP 中，画 Form 更方便
- 图片处理更方便
- 可以导入既有的 PDF 或则 Word
- Layout 可以重用
- 字体支持更加强大
- Barcode 等使用更加方便

## 设计Adobe Form

#### Form 设计好的方法

- 尽量不要只用一个 Master Page 去管理整个 Form(除非非常简单)
- 在使用嵌套 subform 的时候注意，不要嵌套在不同页的两个 subform
- 页面有不同的内容就添加一个 body page ，而不要选择 goto
- Keep with prev ones 和 Keep with next 仅使用于普通的 subform 而不适用于 body page

### Interface

这里的 Interface 的概念其实比较好解释，它就是相当于一个后台数据的接口，可以为前台各种 Adobe Form 提供对应的数据。不同形式的 Adobe Form 可以调用相同的 Interface。

在 Interface 中可以定义一些参数和进行一些数据处理。包括import、export、tables 以及 exceptions，有点像Function Module 的定义。当然在里面也可以定义一些全局变量，以及可以写些初始化代码以及 sub routine。

#### 创建 Interface

进入事务代码：SFP，输入 Interface 名字，点击创建。

![SFP Interface](/images/ABAP/ABAP_SFP0.png)

#### 接口类型

不同的接口类型适用于不同业务场景。

![Interface](/images/ABAP/ABAP_SFP3.png)

- ABAP Dictionary-Based Interface：ABAP程序调用
- Smart Forms-Compatible Interface：兼容Smart Form
- XML Schema-Based Interface：Web Dynpro 调用
  - 对于输入参数，不能更改，只有一个 XSTRING 类型的参数(除却标准的 Print Parameter)
  - XML Schema: 这个一般是自动生成，也可以选择手工上传一个 **XSD** 文件

#### 接口参数

在接口中添加需要使用的自定义参数。

![Interface](/images/ABAP/ABAP_SFP1.png)

*Form Interface：传递参数*

注意：参数定义用 TYPE，只有兼容 Smart Form 的接口类型才能用 LIKE。

- Import
- Export
- Tables (Only Smart Form Type)
- Exceptions

*Global Definitions：全局变量* ：定义 Form 中需要使用到的全局变量

- Global Data
- Types
- Field Symbols

*Initialization：初始化*

- Code Initialization：如果不是 Smart Form 兼容的接口类型，那么这里是你唯一可以写 ABAP 代码的地方，在未真正调用 Form 之前执行

- Form Routines：这里可以写子程序供 Code 初始化调用

*Currency/Quantity Fields：货币和单位字段的设置*

- 通过这里的设置，决定了表单中数字或货币的显示方式。

### Form

#### 创建 Form

进入事务代码：SFP，在 Form 中输入要创建的 form 名字，点击创建，在弹出框中输入之前创建的 Interface。

![SFP Form](/images/ABAP/ABAP_SFP2.png)

#### Form Properties

![Form properties](/images/ABAP/ABAP_SFP5.png)

Layout Type : 需要使用脚本做交互式报表时使用

- xACF Layout：现在基本使用 ZCI 而不是使用 xACF
- Standard Layout：标准的 Layout type

- ZCI Layout：对 Adobe Reader 的版本有要求，note 号：955795

Interface : 需要调用的接口名字

#### Context

在这里使用 Interface 中定义的内容，可以有选择的使用。在 Context 中也可以自己添加一些文本模块或则图像。

![Context](/images/ABAP/ABAP_SFP4.png)

- Interface：包括我们设计接口的字段以及系统字段(System Fields)


- Interface Property：接口字段的属性
- Context：在 Form 中用到的字段列表

- Context Property：Context 字段属性

Context 主工作区可以创建各种元素类型，也可以决定是否激活某个元素(Set to Active/Deactivate)。

![Context](/images/ABAP/ABAP_SFP7.png)

#### Layout 设置

Layout 进行 Form 主体的绘制，主要在于控件的应用，它可以决定该 Form 的整体格式与相关的页面设置 (翻页设置，页面结构，页码，页边间距等等)。其中最关键的是数据绑定的内容，在 Layout 进行数据绑定就可以将 Interface 中的数据展示到该 Form 中对应的字段中。

使用 Adobe LiveCycle Designer：

- 静态元素控件
  - 图片容器
  - 文本
  - 几何图形
- 动态元素控件：引用自 Context
  - Text 字段
  - 图像
  - 数量字段
  - 时间和日期字段
- Table
  - 动态表：打印表的行数在设计时未知
  - 静态表：设计时确定记录数据内容

![Context](/images/ABAP/ABAP_SFP6.png)

**Live Cycle 设计器**：主要设计内容 Master Page、Context Area、Body Pages、SubForms。

![Context](/images/ABAP/ABAP_SFP10.png)

Master Pages：主页面，可以放置一些静态元素，在每个打印页面都可以看到这些静态元素

- Name：Master page 名字
- Paper type：纸张类型
- Restrict Page Occurrence：打印的页数

Content Area：放置动态元素，例如表内容等。Context Area 只能包含在 Master Page 中

Body Page：包含在 Context Area 之中，是动态元素的内容，顶级的 Subform

Subforms：用来组织 Form 元素，使 Form 的设计逻辑更加清晰



