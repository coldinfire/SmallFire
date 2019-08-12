---

title: " WebDynpro "
date: 2018-10-16T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

## 简介

------

`Web Dynpro` 在业务应用程序的主要结构和视觉设计部分的方式，在很大程度上是独立于实现语言.

- 一个Web Dynpro 组件不是一个JAVA类，而是一系列类集合所有功能一起作为一个单元。每个控制器是一个独立的JAVA类，相互之间是独立的。不可以重用一个组件中的单个控制器，可以选择一组件为单位使用，也可以选择不使用。

- 数据结构用于保存在运行时的业务数据 
- UI元素的定义和属性 
- UI元素的聚集成的观点 
- 意见聚集到视图集和窗口 
- 在响应用户操作执行商业任务活动 
- 视图窗口之间的导航链接 
- 功能可重用单元称为组件 
- 通过Web服务，企业级Java Bean使用持久后端数据和功能或ABAP远程函数调用。

​	**数据绑定**（Data Binding）：自动将数据从Controller的上下文传递到另一个Controller上下文。 

​	**映射**可以分为内部映射（同一组件内不同控制器间的映射）和外部映射（不同组件之间的控制器间的映射）；上下文节点为数据源时称为映射源节点，其他的成为映射节点。

​	**数据绑定**（Data Binding）：自动将数据从View Controller的上下文传递到其布局的元素。

![图1：Web Dynpro Explorer1](/images/webdynpro/webDyn1.png)

![图2：Web Dynpro Explorer2](/images/webdynpro/webDyn2.png)

![图3：Web Dynpro 组件架构](/images/webdynpro/webDyn3.png)

![图4：Web Dynpro 元素绑定](/images/webdynpro/webDyn4.png)

**Web Dynpro Component：**

- 在Web Dynpro组件的代码在业务流程级别的可重复使用单元，而不是技术编码的水平
- 在代码重用的性质所产生的变化产生的编码过程中的注意开发商的重心转移。它们不再是担心这么多的技术编码单元的重用; 相反，一个Web Dynpro组件的设计侧重于业务处理的原子单位的重用。
- 一个组件是WebDynpro的可执行编码时所需要的最小单位 。
- Web Dynpro Component的controller分为四类：
  - Component controller:这种类型的controller在一个Web Dynpro component中只有一个，并且没有visual interface
  - Custom controller:方便了代码的整合优化，并且其生命周期可以修改，而在Component中不能修改,封装sub-function
  - View controller:每个View有且只有一个controller和context，负责与视图有关的逻辑，如检查用户输入和处理user action
  - Windows controller:有且只有一个，用于通过inbound plugs 传输数据

**图像化开发工具，允许定义以下内容：**

- 前端和后端之间的数据流  
- 用户界面的布局 
- 用户界面元素的属性 
- 从一个视图到另一个导航 

## MVC

------

- Model：模型对象封装的界面一些后端系统。它的目的是充当分离从数据和功能在远程系统中找到的网页Dynpro应用程序的代理。封装访问业务数据和功能，驻留在远程后端系统。换句话说，在Web Dynpro应用程序不必与后端系统交互所需的特定通信技术。模型将接口封装到位于后端系统中的业务流程的某个步骤。这可以是BAPI调用，也可以是Web服务调用或Enterprise Java Bean。模型始终被视为数据生成器。接收一些数据也是为了完成业务流程的下一步。model会返回一些指示成功或失败的数据。
- View：视图用于定义业务数据的客户机中性可视化。
- Controller：在原来的MVC范例，控制器负责管理的视图（一个或多个）和模型（一个或多个）的相互作用，格式化将被显示在视图中的模型数据，并计算该视图（or views）中接下来的显示。所有的View中的Controller可以和Component Controller进行数据映射，从而达到View之间共享传递数据的功能。

![图5：MVC](/images/webdynpro/webDyn5.png)

## 无视图的 Web Dynpro

------

- Controllers having no visual interface：

![图6：Web Dynpro no visual interface](/images/webdynpro/webDyn6.png)

![图7：Dynpro no visual interface](/images/webdynpro/webDyn7.png)

## 有视图的 Web Dynpro

------

- 视图控制器
  - 视图控制器是不负责业务数据的产生，视图控制器一直被认为是数据的消费者。
  - 视图控制器从model获得数据，通过加工后将数据放置到上下文，视图可以通过上下文获取信息。
  - 视图控制器可以访问由一个称为“上下文映射”技术来在组件控制器的上下文中保存的信息。
  - 视图控制器可以被认为是UI元素的集合，并且与那些UI元素相关联是执行各种数据呈现任务所必需的编码。
  - 数据绑定将视图控制器中的编码与实际UI元素对象分离。

- 窗口控制器
  - ​	与视图控制器处理UI元素聚合的处理的方式相同，因此窗口控制器处理视图聚合的处理。

- 接口控制器
  - 接口控制器是定义组件的编程接口的地方。 就像常规Java接口一样，您可以在接口控制器中定义方法，然后由组件控制器实现。
  - 除了能够在接口控制器中定义方法之外，您还可以定义事件和数据结构（这些数据结构的正确Web Dynpro术语是上下文节点）。

- 组件控制器
  - 组件控制器总是充当组件内的视图控制器数据（或数据生成器）的供应商; 因此，我们知道，组件控制器是存储这些信息的正确位置。

​    除了能够在 Interface Controller 中定义方法之外，还可以定义事件和数据结构（这些数据结构的正确 Web Dynpro 术语是上下文节点）。

![图8：Web Dynpro having visual interface](/images/webdynpro/webDyn8.png)

![图9：Web Dynpro having visual interface](/images/webdynpro/webDyn9.png)

## Context

------

​	(1)上下文中保存的数据仅存在于控制器的生命周期中。 控制器实例终止后，其上下文中保存的所有数据都将丢失。

​	(2) 控制器通常不能够访问彼此的数据或功能。

​	(3) 通过称为上下文映射的技术，可以使另一个控制器（视图或自定义）的上下文可以访问在自定义控制器的上下文中保存的信息。
使用此技术，两个或多个控制器可以访问相同的运行时数据。 

​	(4) 上下文映射是在单个组件内的控制器之间共享数据的主要机制。

​	(5) 视图控制器不允许共享其上下文数据。不负责生成显示的数据，不应该在上下文映射关系中充当数据源。

​	(6) 参数：

- 映射：通过引用不同控制器中的相应节点来获取节点的结构。

- 模型绑定：节点的结构将从先前创建的模型对象中获取

- 结构绑定：节点的结构将从Java Dictionary中先前创建的结构中获得

- 手动：节点的结构将通过手动定义创建

​	(7)创建VIEW的CONTEXT，CONTEXT须和VIEWS上的某些控件的特定属性绑定，这样当我们改变了CONTEXT的某个节点的数据，那么相应控件的值也会跟着改变。
​	建立CONPONENT CONTROLLER中的CONTEXT节点并且和VIEW CONTEXT相应节点映射，这样就相当于把全局变量和局部变量相关联。

​	(8) context是一个包含node和attribute的结构。每一个context都有一个默认的root node，一个node连同其子元素被合称为一个element。

## 构建上下文

------

​	(1) 节点基数

- 0......1：零个或一个元件允许

- 0......N：零个或更多个元件允许

- 1......1：一个且只允许一个元件允许

- 1......N：一个或多个元件允许	

​	< 对于那些它们的基数最低设置为1的节点，那么节点集合将被实例化，使得它已经包含了一个单一的空元素。这个元素被称为默认元素。

​	< 具有其基数的最低设置为0，则该节点将一个空的集合实例化。Web Dynpro开发人员必须先编写代码来创建，然后插入第一个元素到集合。

​	< 现在将Collection Cardinality值从默认的0..n更改为1..1。 这告诉Web Dynpro Framework在运行时，此上下文节点将包含一个且仅包含一个元素。 这就像说数据库表只能包含一行。

​	< 编辑器窗口左侧的“创建数据链接”工具，然后将一行从FirstView拖到组件控制器。此操作的目的是声明视图控制器将从组件控制器充当数据使用者。

​	< 从视图控制器指向组件控制器 - 从不相反。 这表示视图控制器充当数据消费者，组件控制器充当数据生成器.

(2) singleton 属性

- true|false：默认为true，不管有多少个元素存在于节点的父集合中; 只会永远是子节点LineItem的一个实例。

(3) 上下文映射机制

- 建立映射关系，以下两个条件必须满足：
  - 必须有可以充当映射原点合适的节点 
  - 上下文节点必须存在于使用控制器 

​	< 所有的上下文节点是包含，除其他事项外，收集运行时对象。当的映射关系被声明，映射的节点的节点集合由在映射起源节点的参考节点集合代替。以这种方式，上下文映射永远不会导致数据被复制。只有一个数据副本，它的生命属于映射源节点的收藏

## UI Element

------

​	(1) 所有的UI元素有一个属性集，其中有许多可以接受硬编码值。然而，当在屏幕上要显示的数据在视图控制器的上下文中被保持，

​	(2) 简单的声明性关系可以适当UI元素属性和保持所需要的数据视图控制器的上下文中的属性之间进行。

​	(3) 这是被称为数据绑定关系，并且是用于呈现数据给用户的标准机制。

​	(4) 在Web Dynpro开发人员只需要编写代码用于填充上下文属性。一旦数据是存在于该上下文中，Web Dynpro框架确保UI元素

​	(5) 属性的值是从相关联的上下文属性获得。因此，一个Web Dynpro程序通常可以写一个不包含涉及渲染UI的任何代码！

​	(6) UI元素对象仅仅是用于呈现在上下文中找到的数据的工具

​	(7) UI元素是否运行在Web Dynpro框架，实际上具体决定需要编码来呈现在屏幕上。

​	(8) 每个视图控制器中创建UI元素时，这些UI元素只能从视图控制器的上下文中获取它们的信息。 

​	(9) 使用模板向导使用上下文中找到属性添加表单。选择上下文属性后，不要在模板向导中单击“完成”，而是单击“下一步”，然后将用于显示数据的UI元素类型从InputField更改为TextView。现在，屏幕上的字段将是只读的。

- 创建一个Action对象，并为其指定文本值“Back”。

- 创建Button UI元素，通过更改layoutData将其放置在新行的开头。

​	(10) 按钮的功能回应

![图10：事件绑定和响应](/images/webdynpro/webDyn12.png)

## Windows

------

​	(1) 窗口定义了两件事：

- 可以为组件的一个特定可视界面显示的所有可能视图的超集
- 这些视图之间存在的导航路径

​	(2) 视图一旦被创建，直到它第一次被嵌入到一个窗口它不会是前端装置上可见的。一旦一个视图被声明为任何特定的窗口中的一员，它可以在前端客户端设备上是否可见。

​	(3) **UI元素ViewContainer：**此UI元素，当添加到视图的布局，作为用于任何其它视图的容器。ViewContainers可以实现在屏幕上所需的布局被布置在大量的各种方式。

- 可嵌入到一个ViewContainer UI元素的条件:
  - 当前组件的任何视图
  - 子Web Dynpro组件的任何可视化界面 
  - 空视图（由Web Dynpro运行时自动地供给） 

​	(4) 持久数据应该总是被存储在组件控制器，或者是定制控制器，因为这种控制器持续整个组件的寿命。视图控制器的数据在器不是当前视图组件的一部分就会将其数据丢弃掉。

​	(5) 简单窗口中，任何时候都只能看到其中一个视图。

## 插件和导航链接

------

​	**在每个视图中定义导航插头，并将它们与一个导航链路链接在一起。导航插头有两种：入站和出站**

​	**(1) 出站：**

​	触发出站插件时，会向Web Dynpro框架发送“导航请求”消息。 
​	Web Dynpro框架接受此请求并将其放在堆栈中，该堆栈仅在当前视图程序集中的所有视图都响应用户事件后才会被处理

​	**(2) 入站：**

​	入站插件是目标视图中的一种方法，它能够响应出站插件的“导航请求”。 一旦当前视图程序集中的所有视图都完成了处理，Web Dynpro Framework就会将注意力转移到导航堆栈上的请求。为了成功处理每个“导航请求”，必须已经在出站插件和入站插件之间声明了关联。 此关联称为导航链接。

​	**(3) 导航链接：**

​	是一个视图向另一个，每个出站插头都必须在目标目标视图的入站插头相关联。这种关联是已知的，并且能够仅在设计时定义。通过声明导航链接，您可以在窗口中的两个视图之间创建固定关联。 一旦您触发出站插件，“导航请求”将无条件地放置在导航堆栈上。在这个时间点之后，关于导航处理的唯一选项是：

- 处理导航堆栈中的所有请求
- 中止整个导航堆栈的处理

​	一旦将导航请求添加到导航堆栈，就不可能有选择地处理导航请求。 您要么处理整个堆栈，要么完全中止导航处理。因此，在编写导航编码时，在决定触发出站插件之前应该小心。

​	**(4) 创建**

​	“创建链接”工具。单击此工具，然后首先从FirstView拖动一行到SecondView。这将为您创造三件事：

- FirstView中的出站导航插件
- SecondView中的入站导航插件
- 从FirstView的出站插件开始并在SecondView的入站插件处完成的导航链接

​	**(5) 按Ctrl-Shift-S保存所有更改**

​	**(6) 编码**

- ​	告诉Web Dynpro，当在FirstView中调用动作处理程序方法以响应客户端中的按钮时， 我们需要触发出站导航插头。这是我们需要在FirstView的动作处理程序方法中编写单行代码的地方。
- 两个特殊注释标记：// @@ begin和// @@ end。 您只能在这些注释标记之间放置编码。
- (wdThis.wd...)Ctrl-Space，代码完成弹出窗口
- 当我们创建导航链接时，“创建导航链接”工具会自动为我们创建出站和入站插件。默认情况下，这些插件将被称为“Out”和“In”。通常，只要创建了任何出站插件，NWDS就会自动为您生成一个名为wdFirePlug $ {pout}（）的方法，其中$ {pout}是出站插件的名称。
  要使导航发生所需要做的就是调用此方法，Web Dynpro Framework将为您处理剩下的工作。在这里，我们需要编写将调用出站导航插件的代码行。

​	**(7) 交互**

- 用户单击按钮UI元素
- 按钮UI元素引发一个名为onAction的客户端事件
- 客户端事件导致往返服务器
- onAction事件已与名为DataEntered的运行时操作相关联，这又导致视图控制器中的onActionDataEntered（）方法被调用
- onActionDataEntered（）方法包含一行代码，用于触发出站导航插件
- Web Dynpro Framework然后处理删除FirstView所需的所有处理从屏幕上用SecondView替换它。**

## 用户访问

>< 我们需要一些允许用户访问此组件功能的方法。 
>
>< 这是通过创建一个名为Application的东西来实现的。
>
>< 应用程序只是URL与Web Dynpro组件的可视界面的关联。 
>
>< 一旦创建了应用程序，您就已经创建了一种方法，来自客户端设备（例如浏览器）的用户可以通过该方式调用某些Web Dynpro功能。
>
>< 右键单击刚刚创建的应用程序的名称，然后从菜单中选择“Deploy new Archive and Run”。



## 显示数据

------

​	(1) 表格显示的数据是从已知的作为上下文视图控制器的数据存储区域获得的。视图控制器反过来，从组件控制器的上下文获取数据。

​	(2) 填充组件控制器需要写入代码控制（Context mapping relationship<上下文映射关系>, Data Binding relationship<视图控制器与显示界面进行绑定>）

​	(3) 数据提供给UI元素：web Dynpro有用于控制UI元素的弯管和行为的一个更简单，更优雅的机制=》UI元件结合。

​	(4) UI元素绑定：几乎所有UI元素属性可以是“绑定”到在视图控制器的上下文适当的属性。

​	(5) 用户界面的行为可以控制如下： 

- 在设计时，创建适当的数据类型的上下文属性持有UI元素的属性值。  

- 声明在视图布局UI元素和结合要控制到上下文属性在步骤1中声明的属性。

- 现在，在视图控制器的编码不需要精确关心正在使用的用户界面元素。

  所有这是需要关注的，设置适当的值到右键属性，UI元素属性将自动接收值。

​	(6)设计优势：

- 通常，需要以控制它们的外观和/或行为的UI元素对象没有直接访问。
- 在Web Dynpro开发人员需要以控制用户界面的行为写显著更少的代码。 
- 编码仍然不可知的客户端。无需更改应用程序代码，以考虑不同客户端设备中的技术差异。

![图11：UI元素](/images/webdynpro/webDyn11.png)

## Others

------

​	(1)可以使用提供的工具生成大部分Web Dynpro应用程序，而无需创建自己的源代码。这适用于本申请的以下部分：

- 前端和后端之间的数据流

- 用户界面的布局

- 用户界面元素的属性

  Web Dynpro工具使您可以在生成的源文本中手动创建源文本区域。如果重新生成源代码，则不会更改这些区域。

​	**(2)业务分离与应用逻辑:**

​	使用Web Dynpro可以清晰地分离业务逻辑和显示逻辑。 Web Dynpro应用程序在前端运行，并通过服务对后端系统进行本地或远程访问。这意味着显示逻辑包含在Web Dynpro应用程序中，而业务逻辑和业务对象的持久性在后端系统中运行。以下选项目前可用于连接Web Dynpro应用程序和后端系统：

- 使用自适应RFC生成的接口，用于调用SAP系统的BAPI

- 调用企业服务模式 

- 调用使用SOAP协议的Web服务 

- 一个调用的Enterprise Java Bean  通过远程方法调用（RMI）协议 

- 一个自生成的界面

  连接Web Dynpro应用程序所需的源代码可以从Web Dynpro接口的UML定义生成。可以将UML定义作为XML文件导入Web Dynpro工具。

​	(3)**模型 - 视图 - 控制器编程模型的转换：**

​		每个Web Dynpro应用程序都是根据Model View Controller编程模型构建的：

- 该模型构成了后端系统的接口，从而使Web Dynpro应用程序能够访问数据。

- 该视图负责在浏览器中表示数据。

- 控制器位于视图和模型之间。控制器格式化要在视图中显示的模型数据，处理用户创建的用户条目，并将它们返回到模型。

​	所有四种Web Dynpro控制器类型（组件，自定义，窗口和视图控制器）的类和接口参考基于以下结构：

- 所有控制器类或控制器接口通用的所有冗余部件都记录在两个单独的部分中。

- 公共控制器参考的控制器特定添加内容记录在附加部分中。

- 控制器类和控制器接口分开描述。

​	Common Controller Class Reference包含所有Web Dynpro控制器类共有的部分。

​	附加部件（取决于特定的控制器类型）记录在四个Web Dynpro控制器类型组件，自定义，窗口和视图控制器的单独部分中。

​	(4)**预定义的钩子方法：**

- public void wdDoExit（）在销毁控制器实例以进行清理之前，由Web Dynpro Runtime调用。

- public void wdDoInit（）由Web Dynpro Runtime调用以初始化控制器实例

​	< 基于声明的其他公共方法:

​		`public <type> <method name>（[parameter {“，”parameter}]）[throws {checked exception class type}]`

​	具有任意签名的控制器方法（参数和返回类型）。可以为应用程序开发人员声明的所有控制器方法定义已检查或编译器强制执行的异常。在Web Dynpro Tools中，已检查的异常被添加到方法定义中，类似于添加方法参数。调用公共控制器方法必须通过在自己的方法定义中捕获或添加它们来处理这些异常（在其throws子句中声明它们）在View Controllers中，基于声明的其他方法的类型为public。然而，它们无法从其他控制器调用，因为视图控制器不公开IPublic -API。

`public void <事件处理程序名称>（IWDCustomEvent wdEvent，[“，”parameter {“，”parameter}]）`

​	事件处理程序方法。参数必须与所描述事件的参数兼容。事件处理程序的名称应以前缀on开头。

​	< 其他私人方法:

`private <type> <method name>([parameter {“，”parameter}])`

​	可以在最终用户编码区域中添加其他私有方法：// @@ begin others - // @@ end code。这些方法不会添加到控制器的IPublic -API中，因此即使使用公共可见性语句指定它们，它们也不会向其他控制器公开。

​	**(5) 成员变量**

预定义的快捷方式变量，

​		`private final IPrivate <controller nam> wdThis`

​		`private final IPrivate <controller name> .IContextNode wdContext`

​	引用控制器上下文中的根节点。不仅提供对根节点元素的类型访问，还提供对上下文中所有节点（`method note<node name>（）`）及其当前所选元素（`method current <node name> Element（）`）的类型访问。它还有助于为所有节点创建新元素（`method create <node name> Element（）`）。

​	引用内部控制器类生成的IPrivate接口

​	`private final com.sap.tc.webdynpro.progmodel.api.IWDComponent wdComponentAPI wdThis.wdGetAPI（）`getComponent（）的快捷方式。

​	表示此控制器所属的Web Dynpro组件的通用API。可用于访问与IWDComponent -API关联的其他Web Dynpro Runtime API，如消息管理器，窗口管理器或动态添加/删除事件处理程序。

​	`private final <generic controller API> wdControllerAPI`

​	wdThis.wdGetAPI（）的快捷方式。表示此控制器的通用Web Dynpro对应的通用控制器API（IWDController，IWDComponent，IWDViewController和IWDWindowController）。

​	**(6)预定义变量:**

- 记录器记录位置
  - serialVersionUID此变量与应用程序代码开发无关。 SAP NetWeaver序列化框架需要检查序列化对象的二进制兼容性。

- 私人会员变量
  - private <type> <variable name>可以在最终用户编码区域中添加任意数量的私有成员变量：// @@ begin others - // @@ end code
  - 对于此上下文节点，生成两个接口IContextNode和IContextElement，以允许在应用程序代码中进行上下文编程和动态上下文修改（添加属性或内部节点）。

