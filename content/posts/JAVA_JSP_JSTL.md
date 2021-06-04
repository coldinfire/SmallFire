---
title: "JSP 标签库"
date: 2017-11-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - JSP
---

## EL表达式

表达式语言（Expression Language，EL），EL 表达式是用 `${expression}` 括起来的脚本，用来更方便的读取对象。EL 表达式主要用来读取数据，进行内容的显示。

使用 EL 表达式获取 4 个内置对象（域）中的数据，自定义对象中的数据、提交的参数、JavaBean、甚至集合。

使用 EL 表达式可以很方便地引用一些 JavaBean 以及其属性，不会抛出 NullPointerException 之类的错误。但是，EL 表达式非常有限，它不能遍历集合，做逻辑的控制。

EL 表达式允许用户开发自定义 EL 函数，以在 JSP 页面中通过 EL 表达式调用 Java 类的方法。

### 获取数据

#### 运算符

算术运算符：+、-、*、/、%

比较运算符：>、<、>=、<=、==、!=

逻辑运算符：&&、||、!

空运算符：用于判断字符串、集合、数组对象是否长度为0；

- 表达式：`${empty list}`

- EL 三元运算符: ${empty 对象 ? 表达式1 : 表达式2}

#### 获取值

EL 表达式只能从域对象中获取值，`${域名称.键名}` 从指定域中获取指定键的值。

EL 域名称:

| EL 名称          | JSP 内置对象                 |
| ---------------- | ---------------------------- |
| pageScope        | pageContext                  |
| requestScope     | request                      |
| sessionScope     | session                      |
| applicationScope | application (ServletContext) |

web 中的四个域对象（容器对象）范围：从小到大顺序：

- page < request < session < application(ServletContext)

EL 表达式从四个域中取值，就是在代替使用 PageContext 中的 getAttribute 方法取值的麻烦，底层依然在使用 PageContext 中的 getAttribute 方法。

使用 pageContext 的 getAttribute方法或者 findAttribute 方法从4个范围中取出数据的时候，如果指定的 key 不存在会得到 null，而使用 EL 表达式取出的时候指定的 key 不存在，返回 " " 空字符串。

如果不知道数据在哪个范围中，这时可以不用指定范围直接书写 key值即可：`${键名}`。依次从最小的域中查找是否有该键对应的值，知道找到为止。

#### EL 表达式获取复杂数据

在 EL 表达式中，获取对象的属性的值的时候，其实不是在看这个对象所在的类是否有这个属性，只要这个对象所在的类中有 getXxxxx 方法，就可以使用 EL 表达式获取 Xxxx 值。

- 获取数组数据：`${arr1[3]}`
- 获取集合中的值：`${list[2]}`
- 获取自定义对象的属性值：`${object.attirbute}`

#### EL 隐式对象

| 隐式对象         | 描述                                            |
| ---------------- | ----------------------------------------------- |
| pageContext      | pageContext 实例对应于当前页面的处理            |
| pageScope        | 与页面作用域属性的名称和值相关联的 Map 类       |
| requestScope     | 与请求作用域属性的名称和值相关联的 Map 类       |
| sessionScope     | 与会话作用域属性的名称和值相关联的 Map 类       |
| applicationScope | 与应用程序作用域属性的名称和值相关联的 Map 类   |
| param            | 按名称存储请求参数的主要值的 Map 类             |
| paramValues      | 将请求参数的所有值作为 String 数组存储的 Map 类 |
| Header           | 按名称存储请求头主要值的 Map 类                 |
| headerValues     | 将请求头的所有值作为 Sting 数组存储的 Map 类    |
| cookie           | 按名称存储请求附带的 cookie 的 Map 类           |
| initParam        | 按名称存储 Web 应用程序上下文初始化参数         |

## JSP 标准标签库（JSTL）

JSP 标准标签库（JSTL）是一个 JSP 标签集合，它封装了 JSP 应用的通用核心功能。只不过它不是 JSP 内置的标签，需要我们自己导包，以及指定标签库。

为什么要使用 JSTL：EL 表达式不够完美，需要 JSTL 的支持。JSTL 与 HTML 代码十分类似，遵循着 XML 标签语法，使用 JSTL 让 JSP 页面显得整洁，可读性非常好，重用性非常高，可以完成复杂的功能。使用 JSLT 标签的目的就是不希望在 jsp 页面中出现 java 逻辑代码。

JSTL 支持通用的、结构化的任务，比如迭代，条件判断，XML 文档操作，国际化标签，SQL 标签。 除了这些，它还提供了一个框架来使用集成 JSTL 的自定义标签。

### JSTL 使用

#### JSTL 库安装

下载 [ jakarta-taglibs-standard-1.1.2.zip](http://archive.apache.org/dist/jakarta/taglibs/standard/binaries/) 包并解压，将 jakarta-taglibs-standard-1.1.2/lib/ 下的两个 jar 文件：**standard.jar** 和 **jstl.jar** 文件拷贝到 **/WEB-INF/lib/** 下。不要忘了把 jar 包选中—右键—Build Path—Add to Build Path。这样做可以让jar包随着项目走，绑在了一起。

#### JSTL 库使用

使用任何库，必须在每个 JSP 文件中的头部包含`<taglib>`标签。

在页面中使用 JSTL 定义的 EL 函数：`<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>`

### JSTL 标签分类

根据 JSTL 标签所提供的功能，可以将其分为5个类别。

- 核心标签 ：`<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>`
- JSTL 函数：`<%@ taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn"%>`
- 格式化标签：`<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt"%>`
- SQL 标签：`<%@ taglib uri="http://java.sun.com/jsp/jstl/sql" prefix="sql"%>`
- XML 标签：`<%@ taglib uri="http://java.sun.com/jsp/jstl/xml" prefix="x"%>`

#### 核心标签

| 标签                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [c:out](https://www.runoob.com/jsp/jstl-core-out-tag.html)   | 用于在JSP中显示数据，就像<%= ... >，通过 escapeXml 控制是否忽略 XML 特殊字符 |
|                                                              | <c:out value="输出内容 X" default="输出默认值" escapeXml="<true\|false>"></c:out> |
| [c:set](https://www.runoob.com/jsp/jstl-core-set-tag.html)   | 相当于<jsp:setProperty >，用于存值到scope中(page,request,session,application)；存值到 JavaBean 的属性中 |
|                                                              | <c:set    var="信息变量"    value="存储值"    target="对象"    property="对象属性"    scope="作用域"></c:set> |
| [c:remove](https://www.runoob.com/jsp/jstl-core-remove-tag.html) | 用于删除数据,只能remove掉某个变量,var  属性必选，scope 可选  |
|                                                              | <c:remove var="要移除的变量名称 X" scope="变量所属的作用域"></c:remove> |
| [c:catch](https://www.runoob.com/jsp/jstl-core-catch-tag.html) | 用来处理产生错误的异常状况，并且将错误信息储存起来           |
|                                                              | <c:catch var="用来储存错误信息的变量"> ... </c:catch>        |
| [c:if](https://www.runoob.com/jsp/jstl-core-if-tag.html)     | 与我们在一般程序中用的if一样,用来实现分支条件控制            |
|                                                              | <c:if test="条件 X" var="存储条件结果的变量" scope="变量所属作用域">    ... </c:if> |
| [c:choose](https://www.runoob.com/jsp/jstl-core-choose-tag.html) | 本身只当做<c:when>和<c:otherwise>的父标签                    |
| [c:when](https://www.runoob.com/jsp/jstl-core-choose-tag.html) | <c:choose>的子标签，用来判断条件是否成立                     |
| [c:otherwise](https://www.runoob.com/jsp/jstl-core-choose-tag.html) | <c:choose>的子标签，接在<c:when>标签后，当<c:when>标签判断为false时被执行 |
|                                                              | <c:choose> <c:when test="条件 X"> ... </c:when><c:when test="条件">...</c:when>...<c:otherwise>...</c:otherwise> </c:choose> |
| [c:import](https://www.runoob.com/jsp/jstl-core-import-tag.html) | 检索一个绝对或相对 URL，然后将其内容暴露给页面；动态包含     |
|                                                              | <c:import url="URL X"    var="变量"    scope="作用域"    varReader="Reader对象"    context="相对路径资源名"    charEncoding="字符集编码"></c:import> |
| [c:forEach](https://www.runoob.com/jsp/jstl-core-foreach-tag.html) | 基础迭代标签，接受多种集合类型，索引从 0 开始                |
|                                                              | <c:forEach     items="要被循环的信息"     begin="开始的元素"     end="最后一个元素"     step="迭代的步长"     var="变量"     varStatus="循环状态变量"></c:forEach> |
| [c:forTokens](https://www.runoob.com/jsp/jstl-core-foreach-tag.html) | 根据指定的分隔符来分隔内容并迭代输出，索引从 0 开始          |
|                                                              | <c:forTokens     items="要被循环的信息"     delims="分隔符 X"     begin="开始的元素"     end="最后一个元素"     step="迭代的步长"     var="变量"     varStatus="循环状态变量"></c:forTokens> |
| [c:param](https://www.runoob.com/jsp/jstl-core-param-tag.html) | 用来给包含或重定向的页面传递参数                             |
|                                                              | <c:param name="URL中要设置的参数的名称 X" value="参数的值"></c:param> |
| [c:redirect](https://www.runoob.com/jsp/jstl-core-redirect-tag.html) | 重定向至一个新的URL.                                         |
|                                                              | <c:redirect url="目标URL X" context="本地网络应用程序的名称"></c:redirect> |
| [c:url](https://www.runoob.com/jsp/jstl-core-url-tag.html)   | 使用可选的查询参数来创造一个URL                              |
|                                                              | <c:url   var="代表URL的变量名 X"   scope="var属性的作用域"   value="基础URL"   context="本地网络应用程序的名称"></c:url> |

#### JSTL 函数

JSTL包含一系列标准函数，大部分是通用的字符串处理函数。

| 函数                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [fn:contains()](https://www.runoob.com/jsp/jstl-function-contains.html) | 测试输入的字符串是否包含指定的子串—fn:contains(原始字符串, '要查找的子字符串') |
| [fn:containsIgnoreCase()](https://www.runoob.com/jsp/jstl-function-containsignoreCase.html) | 测试输入的字符串是否包含指定的子串，大小写不敏感—fn:containsIgnoreCase(原始字符串, '要查找的子字符串') |
| [fn:startsWith()](https://www.runoob.com/jsp/jstl-function-startswith.html) | 测试输入字符串是否以指定的前缀开始—fn:startsWith(原始字符串, '搜索的前缀') |
| [fn:endsWith()](https://www.runoob.com/jsp/jstl-function-endswith.html) | 测试输入的字符串是否以指定的后缀结尾—fn:endsWith(原始字符串, '搜索的后缀') |
| [fn:escapeXml()](https://www.runoob.com/jsp/jstl-function-escapexml.html) | 跳过可以作为XML标记的字符                                    |
| [fn:indexOf()](https://www.runoob.com/jsp/jstl-function-indexof.html) | 返回指定字符串在输入字符串中出现的位置—${fn:indexOf(原始字符串,"子字符串")} |
| [fn:join()](https://www.runoob.com/jsp/jstl-function-join.html) | 将数组中的元素合成一个字符串然后输出—${fn:join([数组], '分隔符')} |
| [fn:length()](https://www.runoob.com/jsp/jstl-function-length.html) | 返回字符串长度—${fn:length(collection \| string)}            |
| [fn:replace()](https://www.runoob.com/jsp/jstl-function-replace.html) | 将输入字符串中指定的位置替换为指定的字符串然后返回 — ${fn:replace(原始字符串, '被替换的字符串', '要替换的字符串')} |
| [fn:split()](https://www.runoob.com/jsp/jstl-function-split.html) | 将字符串用指定的分隔符分隔然后组成一个子字符串数组并返回—${fn:split(待分隔的字符串, '分隔符')} |
| [fn:substring()](https://www.runoob.com/jsp/jstl-function-substring.html) | 返回字符串的子集— ${fn:substring(待分隔的字符串,开始索引, 结束索引)} |
| [fn:toLowerCase()](https://www.runoob.com/jsp/jstl-function-tolowercase.html) | 将字符串中的字符转为小写 — ${fn.toLowerCase(待转换字符串)}   |
| [fn:toUpperCase()](https://www.runoob.com/jsp/jstl-function-touppercase.html) | 将字符串中的字符转为大写 — ${fn.toUpperCase(待转换字符串)}   |
| [fn:trim()](https://www.runoob.com/jsp/jstl-function-trim.html) | 移除首尾的空白符 — ${fn:trim(待处理字符串)}                  |

#### 格式化标签

JSTL格式化标签用来格式化并输出文本、日期、时间、数字。

| 标签                                                         | 描述                                     |
| :----------------------------------------------------------- | :--------------------------------------- |
| [fmt:formatNumber](https://www.runoob.com/jsp/jstl-format-formatnumber-tag.html) | 使用指定的格式或精度格式化数字           |
| [fmt:parseNumber](https://www.runoob.com/jsp/jstl-format-parsenumber-tag.html) | 解析一个代表着数字，货币或百分比的字符串 |
| [fmt:formatDate](https://www.runoob.com/jsp/jstl-format-formatdate-tag.html) | 使用指定的风格或模式格式化日期和时间     |
| [fmt:parseDate](https://www.runoob.com/jsp/jstl-format-parsedate-tag.html) | 解析一个代表着日期或时间的字符串         |
| [fmt:bundle](https://www.runoob.com/jsp/jstl-format-bundle-tag.html) | 绑定资源                                 |
| [fmt:setLocale](https://www.runoob.com/jsp/jstl-format-setlocale-tag.html) | 指定地区                                 |
| [fmt:setBundle](https://www.runoob.com/jsp/jstl-format-setbundle-tag.html) | 绑定资源                                 |
| [fmt:timeZone](https://www.runoob.com/jsp/jstl-format-timezone-tag.html) | 指定时区                                 |
| [fmt:setTimeZone](https://www.runoob.com/jsp/jstl-format-settimezone-tag.html) | 指定时区                                 |
| [fmt:message](https://www.runoob.com/jsp/jstl-format-message-tag.html) | 显示资源配置文件信息                     |
| [fmt:requestEncoding](https://www.runoob.com/jsp/jstl-format-requestencoding-tag.html) | 设置request的字符编码                    |

#### SQL 标签

JSTL SQL标签库提供了与关系型数据库（Oracle，MySQL，SQL Server等等）进行交互的标签。

| 标签                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [sql:setDataSource](https://www.runoob.com/jsp/jstl-sql-setdatasource-tag.html) | 指定数据源                                                   |
|                                                              | <sql:setDataSource var="代表数据库的变量" scope="var属性的作用域"  dataSource='事先准备好的数据库' driver="JDBC驱动"      url="数据库连接的JDBC URL"      user="数据库用户名"  password="数据库密码"></sql:setDataSource> |
| [sql:query](https://www.runoob.com/jsp/jstl-sql-query-tag.html) | 运行SQL查询语句                                              |
|                                                              | <sql:query   var="存储查询结果(ResultSet对象)的变量"   scope="属性的作用域"   sql="SQL命令"   dataSource="所使用的数据库连接"   startRow="开始记录的结果的行数"   maxRows="最大结果数"></sql:query> |
| [sql:update](https://www.runoob.com/jsp/jstl-sql-update-tag.html) | 运行SQL更新语句                                              |
|                                                              | <sql:update var="用来存储所影响行数的变量" scope="属性的作用域" sql="SQL命令" dataSource="所使用的数据库连接"></sql:update> |
| [sql:param](https://www.runoob.com/jsp/jstl-sql-param-tag.html) | 将SQL语句中的参数设为指定值 — <sql:param value="需要设置的参数值"/> |
| [sql:dateParam](https://www.runoob.com/jsp/jstl-sql-dateparam-tag.html) | 将SQL语句中的日期参数设为指定的java.util.Date 对象值         |
|                                                              | <sql:dateParam value="需要设置的日期参数(java.util.Date)" type="DATE \| TIME \| TIMESTAMP"></sql:dateParam> |
| [sql:transaction](https://www.runoob.com/jsp/jstl-sql-transaction-tag.html) | 在共享数据库连接中提供嵌套的数据库行为元素，将所有语句以一个事务的形式来运行 |
|                                                              | <sql:transaction dataSource="所使用的数据库" isolation="事务隔离等级 (READ_COMMITTED \| READ_UNCOMMITTED \| REPEATABLE_READ \|  SERIALIZABLE)"></sql:transaction> |

## 自定义标签

要创建自定义的JSP标签，你首先必须创建处理标签的Java类。

可以继承SimpleTagSupport类并重写的doTag()方法来开发一个最简单的自定义标签。