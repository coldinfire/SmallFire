---
title: "JSP 简介"
date: 2017-11-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - JSP
---



### JSP 简介

JSP 全称 Java Server Pages，是一种动态网页开发技术。它使用 JSP 标签在 HTML 网页中插入 Java 代码。

JSP 基于Java Servlet API，JSP 网页就是用另一种方式来编写 Servlet 。JSP页面可以与处理业务逻辑的 Servlet 一起使用，这种模式被 Java servlet 模板引擎所支持。

JSP 是服务器端的，它的局限性在于数据必须在返回给客户端之前就“装载”完毕。

#### JSP 优势

- 与纯 Servlet 相比：JSP可以很方便的编写或者修改 HTML 网页而不用去面对大量的 println 语句
- 与 JavaScript 相比：虽然 JavaScript 可以在客户端动态生成 HTML，但是很难与服务器交互，因此不能提供复杂的服务，比如访问数据库和图像处理等等
- 与静态HTML相比：静态HTML不包含动态信息

#### JSP 解析过程

Web 服务器使用 JSP 创建网页过程

- 浏览器发送一个 HTTP 请求给服务器。
- Web 服务器识别出这是一个对 JSP 网页的请求(通过使用 URL 或者 .jsp 文件来完成)，并且将该请求传递给 JSP 引擎。
- JSP 引擎从磁盘中载入 JSP 文件，然后将它们转化为 Servlet。这种转化只是简单地将所有模板文本改用 println() 语句，并且将所有的 JSP 元素转化成 Java 代码。
- JSP 引擎将 Servlet 编译成可执行类，并且将原始请求传递给 Servlet 引擎。
- Web 服务器的某组件将会调用 Servlet 引擎，然后载入并执行 Servlet 类。在执行过程中，Servlet 产生 HTML 格式的输出并将其内嵌于 HTTP response 中上交给 Web 服务器。
- Web 服务器以静态 HTML 网页的形式将 HTTP response 返回到浏览器中。
- 最终，Web 浏览器处理 HTTP response 中动态产生的HTML网页，就好像在处理静态网页一样。

#### JSP 生命周期

![JSP Life Cycle](/images/JAVA/jsp_life_cycle.jpg)

编译阶段：当浏览器请求 JSP 页面时，JSP 引擎会首先去检查是否需要编译这个文件。如果这个文件没有被编译过，或者在上次编译后被更改过，则编译这个 JSP 文件。

- 解析 JSP 文件
- 将 JSP 文件转为 servlet
- 编译 servlet

初始化阶段：加载与 JSP 对应的 servlet 类，创建其实例，并调用它的初始化方法。

- 在为请求提供任何服务前调用 `jspInit()` 方法

执行阶段：调用与 JSP 对应的 servlet 实例的服务方法

- 当 JSP 网页完成初始化后，JSP 引擎将会调用 `_jspService()` 方法，_jspService()方法需要一个HttpServletRequest对象和一个HttpServletResponse对象作为它的参数

销毁阶段：调用与 JSP 对应的 servlet 实例的销毁方法，然后销毁 servlet 实例

- `jspDestroy()` 方法在 JSP 中等价于 servlet 中的销毁方法

### JSP 语法

#### JSP 注释

JSP注释主要有两个作用：为代码作注释以及将某段代码注释掉。

JSP注释的语法格式：`<%-- 注释内容，不会在网页中显示 --%> `

#### JSP 常量

JSP语言定义了以下几个常量：

- 布尔值(boolean)：true 和 false；
- 整型(int)：与Java中的一样;
- 浮点型(float)：与Java中的一样;
- 字符串(string)：以单引号或双引号开始和结束；
- Null：null。 

#### JSP 声明

一个声明语句可以声明一个或多个变量、方法，供后面的 Java 代码使用。在 JSP 文件中，您必须先声明这些变量和方法然后才能使用它们。

JSP声明的语法格式：`<%! declaration; [ declaration; ]+ ... %>`

- 在 jsp 转换后的 java 类的成员位置
- <%! int i = 0; Circle a = new Circle(2.0); %> 

#### JSP 表达式

一个 JSP 表达式中包含的脚本语言表达式，先被转化成 String，然后插入到表达式出现的地方。

表达式元素中可以包含任何符合 Java 语言规范的表达式，但是不能使用分号来结束表达式。

JSP表达式的语法格式：`<%= 表达式 %>`

- 输出到页面上，输出语句可以定义的内容在这里都可以定义

#### JSP 脚本程序

脚本程序可以包含任意量的 Java 语句、变量、方法或表达式，只要它们在脚本语言中是有效的。

脚本程序的语法格式：`<% 代码片段 %>`

- 在 service 方法在，service 方法中可以定义的内容在这里都可以定义

#### JSP 指令

JSP 指令用来设置与整个 JSP 页面相关的属性。如网页的编码方式和脚本语言。

JSP 指令语法格式：`<%@ directive attribute="value" %>`

*page 指令*：为容器提供当前页面的使用说明，比如脚本语言、error 页面、缓存需求等等。一个 JSP 页面可以包含多个 page 指令。

- 语法：`<%@ page attribute="value" %>`

- | 属性               | 描述                                                         |
  | :----------------- | :----------------------------------------------------------- |
  | buffer             | 指定out对象使用缓冲区的大小，默认 8KB                        |
  | autoFlush          | 控制out对象的 缓存区                                         |
  | contentType        | 指定当前JSP页面的MIME类型和字符编码                          |
  | errorPage          | 指定当JSP页面发生异常时需要转向的错误处理页面                |
  | isErrorPage        | 指定当前页面是否可以作为错误处理页面，true可使用 exception 对象 |
  | extends            | 指定servlet从哪一个类继承                                    |
  | import             | 导入要使用的Java类                                           |
  | info               | 定义JSP页面的描述信息                                        |
  | isThreadSafe       | 指定对JSP页面的访问是否为线程安全                            |
  | language           | 定义JSP页面所用的脚本语言，默认是Java                        |
  | session            | 指定JSP页面是否使用session                                   |
  | isELIgnored        | 指定是否执行EL表达式                                         |
  | isScriptingEnabled | 确定脚本元素能否被使用                                       |

*include 指令*：包含其他文件，被包含的文件可以是JSP文件、HTML文件或文本文件。包含的文件就好像是该 JSP 文件的一部分，会被同时编译执行。

- 语法：`<%@ include file="文件相对url地址" %>`
- include 指令中的文件名实际上是一个相对的 URL 地址。如果没有给文件关联一个路径，JSP 编译器默认在当前路径下寻找

*taglib 指令*：引入一个自定义标签集合的定义，包括库路径、自定义标签。

- 语法：`<%@ taglib uri="uri" prefix="prefixOfTag" %>`
- uri 属性确定标签库的位置
- prefix 属性指定标签库的前缀

#### JSP 行为

JSP 行为标签使用 XML 语法结构来控制 servlet 引擎。它能够动态插入一个文件，重用JavaBean 组件，引导用户去另一个页面，为 Java 插件产生相关的 HTML 等等。

行为标签只有一种语法格式，它严格遵守XML标准：`<jsp:action_name attribute="value" />`

行为标签基本上都是预定义的函数，JSP 规范定义了一系列的标准动作，它用 jsp 作为前缀，可用的标准动作元素如下：

| 语法            | 描述                                           |
| :-------------- | :--------------------------------------------- |
| jsp:include     | 用于在当前页面中包含静态或动态资源             |
| jsp:useBean     | 寻找和初始化一个JavaBean组件                   |
| jsp:setProperty | 设置 JavaBean组件的属性                        |
| jsp:getProperty | 输出某个 JavaBean 组件的属性                   |
| jsp:forward     | 把请求转到一个新的页面                         |
| jsp:plugin      | 用于在生成的HTML页面中包含Applet和JavaBean对象 |
| jsp:element     | 动态创建一个XML元素                            |
| jsp:attribute   | 定义动态创建的XML元素的属性                    |
| jsp:body        | 定义动态创建的XML元素的主体                    |
| jsp:text        | 在JSP页面和文档中使用写入文本的模板            |

常见的属性：

所有动作要素都有两个属性：id 属性和 scope 属性

- id 属性：id 属性是动作元素的唯一标识，可以在 JSP 页面中引用。动作元素创建的id值可以通过 PageContext 来调用
- scope 属性：该属性用于识别动作元素的生命周期。 id 属性和 scope 属性有直接关系，scope 属性定义了相关联 id 对象的寿命。 scope 属性有四个可能的值：page、request、session、application。

#### JSP 隐含对象

JSP 隐式对象是 JSP 容器为每个页面提供的 Java 对象，开发者可以直接使用它们而不用显式声明。JSP 隐式对象也被称为预定义变量。

JSP 所支持的九大隐式对象：

| 对象        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| request     | HttpServletRequest 接口的实例，一次请求访问的多个资源        |
| response    | HttpServletResponse 接口的实例                               |
| out         | JspWriter 类的实例，用于把结果输出至网页上，优先级低于 response |
| session     | HttpSession 类的实例，和Java Servlets中的session对象有一样的行为 |
| application | ServletContext 类的实例，所有用户间共享数据                  |
| config      | ServletConfig 类的实例，直接包装了servlet 的ServletConfig类的对象 |
| pageContext | PageContext 类的实例，当前页面共享数据，提供对JSP页面所有对象以及命名空间的访问 |
| page        | 类似于Java类中的this关键字，它可以被看做是整个JSP页面的代表  |
| exception   | Exception 类的对象，包装了从先前页面中抛出的异常信息，被用来产生对出错条件的适当响应 |

JSP 重定向：

```jsp
<%
   // 重定向到新地址
   String site = new String("url");
   response.setStatus(response.SC_MOVED_TEMPORARILY);
   response.setHeader("Location", site); 
%>
```

