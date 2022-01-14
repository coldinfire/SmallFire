---
title: " Web Dynpro ABAP 程序实例 "
date: 2018-10-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

### 准备数据

SAP Web Dynpro 一般都是采用调用 ERP 接口方式获取业务数据，因此输入，输出参数等结构都需要和 ERP 系统中接口的内容保持一致。在 ERP 系统中找到 Report 对应的程序，在 Web Dynpro 系统中创建对应的结构，分别对应选择屏幕和对应的 ALV 报表。

### SE80 创建 WDA

输入 WDA 的 Name 和 View Name，创建 Web Dynpro 并激活。

![Web Dynpro Create](/images/webdynproABAP/1.1.png)

![Web Dynpro Create](/images/webdynproABAP/1.png)

双击对象名 ZPR_ZPEFF，然后添加 Component Use：

![Web Dynpro Add Component Use](/images/webdynproABAP/2.png)

然后双击 COMPONENTCONTROLLER 在 Properties 中创建 Components：

![Add Components](/images/webdynproABAP/3.png)

切换页签到 Context (还是COMPONENTCONTROLLER)，创建Nodes

- Context 节点。视图使用的表，结构，全部放在这里。一般 0-1、1-1、0-n三种。
  - 0-1、1-1：相当于工作区，结构，1-1 时需要勾选 Initialization Lead Selection
  - 0-n：表示为内表类型

进到创建 Nodes 的界面，输入Node Name，Dictionary structure 然后 Cardinality 改成 0..n，然后点击 Add attribute from structure 来选择需要的字段

![Create node](/images/webdynproABAP/4.1.png)

![Add Attribute from Structure](/images/webdynproABAP/5.png)

![Result](/images/webdynproABAP/6.png)

再建一个 Node (ZOPTION) 用来放复选框

![Create Check box node](/images/webdynproABAP/7.png)

在 ZOPTION 下创建二个属性，对应 P_TOTAL 和 P_DETAIL

![Add attribute](/images/webdynproABAP/8.png)

切换到页签 Attribute，然后维护以下三个属性

![Add attribute](/images/webdynproABAP/9.png)

在页签Methods我们用到2个方法，(具体代码后面会给)：

- DATA_LOAD是手动添加的方法
- WDDOINIT是系统自动添加的方法

![Add METHODS](/images/webdynproABAP/10.png)

### View的处理

每个视图都有一个控制器负责根据用户的交互来执执行作。

#### INPUT_VIEW处理

首先进到 INPUT_VIEW 中的 Context 页签中，然后将右边的 Node 拖到左边，如下图：

![Input View Context](/images/webdynproABAP/11.png)

然后 Inbound Plugs 和 Outbound Plugs 分别维护

- 在 Inbound Plugs 维护 Plug Name：FROM_ALV_VIEW
- 在 Outbound Plugs 维护 Plug Name：TO_ALV_VIEW

**接下来我们在 Layout 设置选择界面的信息。**

首先设置一个执行按钮，右键 ROOTUIELEMENTCONTAINER，选择 Insert Element，然后输入 ID 为 TOOLBAR1，Type为 TOOLBAR 点击确定。

![Input View Layout](/images/webdynproABAP/14.png)

右键 TOOLBAR1，选择 Insert Toolbar Element，输入 ID 为 EXCUTE，Type为 TOOLBAR_BUTTON。

![Input View Layout](/images/webdynproABAP/15.png)

![Input View Layout Ele](/images/webdynproABAP/16.png)

然后设置 EXCUTE 的属性，输入图标名称，创建一个 Event，如下图所示

![Input View Layout Button](/images/webdynproABAP/17.png)

然后双击 EXCUTE 写入逻辑。当点击按钮时，跳入到 ALV_VIEW 视图：`fire_'Outbound Plugs name'_plg( )`。

```ABAP
DATA lo_componentcontroller TYPE REF TO ig_componentcontroller .
    lo_componentcontroller =   wd_this->get_componentcontroller_ctr( ).
    """"来源ComponentController中method
    lo_componentcontroller->data_load(
    ).
    wd_this->fire_to_alv_view_plg(
    ).
```

![Input View Event](/images/webdynproABAP/18.png)

然后我们继续回到 Layout，接下来创建 Group，右键 ROOTUIELEMENTCONTAINER 然后输入 ID 为 SELECTIONS1，选择 Type 为 GROUP，如下图：

![Input View Layout Group](/images/webdynproABAP/19.png)

然后修改 Group 属性，Layout 选择 MatrixLayout 然后 width 维护 100%

![Input View Layout property](/images/webdynproABAP/20.png)

右键 SELECTIONS1 选择 Insert Element 输入 ID 和 Type

![Input View Layout selection](/images/webdynproABAP/22.png)

接下来我们以同样的方式创建 GROUP，名称为 SELECTIONS2；右键 SELECTIONS2，然后选择 Create Container Form，选择字段，确定。

![Input View Layout ](/images/webdynproABAP/21.png)

这里我们回到 COMPONENTCONTROLLER 的 Methods，将用到的2个方法写入逻辑。

- **WDDOINIT** – 初始化选择界面
  - [Component Controller: WDDOINIT](https://coldinfire.github.io/2018/SAPWebDynproABAP_P3/)
- **DATA_LOAD** – 加载数据
  - [ALV_INPUT: DATA_LOAD](https://coldinfire.github.io/2018/SAPWebDynproABAP_P2/)

#### ALV_VIEW处理

将两个 ALV 报表要显示的结果的结构添加到 Component Controller 中的上下文中;

右键 Views 然后创建新的 View 用来展示 ALV 报表：ALV_VIEW，添加 Context 和 Properties/Component Use

![ALV View Context ](/images/webdynproABAP/23.png)

![ALV View Properties ](/images/webdynproABAP/23.1.png)

然后 Inbound Plugs 和 Outbound Plugs 分别维护 

- 在 Inbound Plugs 维护 Plug Name : FROM_INPUT_VIEW
- 在 Outbound Plugs 维护 Plug Name : TO_INPUT_VIEW

然后切换到页签 Layout，新建一个返回按钮 BACK，然后双击 BACK 写入返回逻辑。

![ALV View Layout back](/images/webdynproABAP/26.png)

![ALV View Layout back](/images/webdynproABAP/27.png)

我们再回到 Layout 创建一个 HORIZONTAL_GUTTER 类型是 HORIZONTAL GUTTER 将按钮和 ALV 输出数据隔开。

![View Layout ](/images/webdynproABAP/24.png)

然后创建一个 DISPLAY_DATA 类型是`ViewContainerUIElement`。

![View Layout ](/images/webdynproABAP/25.png)

再切换到 Methods 中，在 WDDOINIT 中写实现 ALV 逻辑。如果在 ALV View 界面需要进行功能处理，则创建对应的 Method 并写入功能代码。

- [ALV_VIEW: WDDOINIT](https://coldinfire.github.io/2018/SAPWebDynproABAP_P1/)
- [ALV_VIEW: Print_Function](https://coldinfire.github.io/2018/SAPWebDynproABAP_P4/)

### Windows 处理

windows 只是一种容器，在一个 component 内一个 window 可以包含任意多个 view。

打开 Windows 并双击视图，然后将 ALV_VIEW 拖到 ZWDA_DEMO 下面，如图：

![Windows Add](/images/webdynproABAP/28.png)

右键 INPUT_VIW 中的 SELECT_OPTIONS，将 select_options 绑定：

![Windows Input View Select Options](/images/webdynproABAP/29.1.png)

![Windows Input View Select Options Embed](/images/webdynproABAP/29.2.png)

然后右键 INPUT_VIEW 下的 TO_ALV_VIEW，选择 Create Navigation Link：

![Windows Input View OUT](/images/webdynproABAP/29.3.png)

![Windows Input View OUT](/images/webdynproABAP/29.4.png)

再右键 ALV_VIEW 下的 DISPLAY_DATA，进行绑定：

![Windows Input View OUT](/images/webdynproABAP/30.png)

然后右键 ALV_VIEW 中的 TO_INPUT_VIEW，Create Navigation Link：

![Windows Input View OUT](/images/webdynproABAP/31.png)

最后效果如下图所示： 

![Windows Result](/images/webdynproABAP/35.png)

### 创建Web Dynpro Component(Interface)

右键 Object Name -> Create -> Web Dynpro Application

![Windows Result](/images/webdynproABAP/32.png)

可以在浏览器通过产生的 URL 进行测试

![Windows Result](/images/webdynproABAP/33.png)





