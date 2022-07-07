---
title: " Web Dynpro ABAP:Structure "
date: 2018-10-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

### 一般的程序框架

![Web Dynpro Role](/images/webdynpro/webdynproABAP/Portal24.png)

#### Component Controller

组件控制器是定义全局的组建，与视图相似，组件控制器是一个程序对外的部分，是整个程序最开始执行的环节 ，也是控制多个视图间数据交互的纽带，一般考虑到程序的扩展性会优先使用组件控制器，然后关联各视图。

#### Component Interface

组件接口是用来引入一些外部组件接口的。引入的组件接口可添加到相应的视图窗口中使用

#### Views

视图是一个DYNPRO程序显示的部分，可有多个视图，视图间可进行跳转，每个视图中需要显示的字段结构表等信息需要单独定义在该视图的节点中（CONTEXT）

注意：组件控制器中也可以添加节点，作为全局节点属性，如果将它与某视图中的节点进行MAPPING，则可以在视图结束后，程序没结束的时候保存节点属性。一般界面跳转进行如此操作。

#### Windows

窗口与视图相似，只是每个程序每次显示只能有一个单独的窗口，可定义多个窗口，窗口间跳转，与视图跳转相似，都是在Inbound Plugs（入站）和Outbound Plugs（出站）里做对应的绑定。

#### Application

应用程序，单独的执行程序。

### Others 

#### Web Dynpro Component 的 controller 分类

Component controller：这种类型的 controller 在一个 web dynpro component 内只存在一个，并且没有 visual interface。
Custom controller：这类 controller 是可选的，用于封装 component controller 的 sub-function。
View controller：每一个 view 都有一个对应的 view controller，负责与视图有关的逻辑，如检查用户的输入和处理 user action。
Window controller：一个 window 内只存在一个 window controller，用于通过 inbound plugs 传递数据。

#### Context Mapping

每个 controller 内部都会有一个 context，用于存储 controller 所用的数据。

Context 是一个包含 node 和 attribute 的结构。

- Attribute: 用于存储单个值，这些值类似于表或结构中的字段。
- Note: 是属性的集合，它类似于结构或内部表。

每个 context 都有一个默认的 root node，这个 root node 不能被修改或删除。一个 node 可以包含子元素（node 和 attribute），而 attribute 只能依附于其他 node 或 context root node 而存在。在同一个 context 内，每个 node 的名字必须是唯一的。一个 node 连同其子元素被合称为一个 element。

Context 中的每个 node 都有两个重要的 property：

- Cardinality：这个属性给出了当前 node collection 在运行时包含的元素数目的最小值和最大值。这个属性的取值包括:
  - 0..1（仅允许 0 或 1 个元素）
  - 0..n（允许 0 或更多元素）
  - 1..1（仅允许一个元素）
  - 1..n（允许一个或多个元素）
- Singleton：这个属性决定了子节点在实例化时是 singleton 还是不是 singleton，因此它的值就是布尔值。Singleton 和 Supply function，两者常在一起使用以实现 lazy data instantiation。比如在加载一个包含多行的表时，每一行包含的更详细数据不会被首先加载，而只有在用户选中并查特定行时，与该行相关的数据在会被读取。

Context mapping 提供了一种机制，供不同的 controller 之间交换数据。context mapping 分为 internal 和 external 两种。

- 如果同一个 component 内的不同 controller 之间共享数据，这被成为 internal context mapping
- 共享数据的 controller 不在同一个 component 之内，这被称为 external context mapping。

要注意的是 view controller 一类不能作为 context mapping 的源，否则就违背了 MVC 的设计原则。

#### Data Binding

在上下文和UI元素之间自动传输数据，对上下文的任何更改都将自动传输到链接的UI元素。

- 将 UI 元素数据(例如：输入字段，复选框，表等) 绑定到相应的节点或属性称为数据绑定。
- 数据自动从视图控制器上下文传输到 UI 元素
- 通过使用数据绑定概念，我们可以控制 UI 元素的多个属性，例如可见性，启用 / 禁用等。
- 可以使用数据绑定通过上下文来操作 UI 元素属性。

