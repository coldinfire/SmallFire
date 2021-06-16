---
title: "XML 基础知识"
date: 2017-10-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - XML

---

### XML 简介

#### 什么是 XML

- XML 指可扩展标记语言（EXtensible Markup Language）。
- XML 是一种很像HTML的标记语言。
- XML 的设计宗旨是传输数据，而不是显示数据。
- XML 标签没有被预定义。您需要自行定义标签。
- XML 被设计为具有自我描述性。

#### XML 树结构

XML 文档形成了一种树结构，它从"根部"开始，然后扩展到"枝叶"。

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<notes>
  <note number="n001">
    <to>Tove</to>
    <from>Jani</from>
    <heading>Reminder</heading>
    <body>Don't forget me this weekend!</body>
  </note>
</notes>
```

#### XML 语法规则

- XML 声明文件的可选部分，如果存在需要放在文档的第一行

- XML 文档必须包含 **根元素**。该元素是所有其他元素的父元素
- 所有的 XML 元素都必须有一个关闭标签
- XML 标签对大小写敏感
- XML 必须正确嵌套
- XML 属性值必须使用引号包含起来
- XML 中特殊字符需要使用 **实体引用** 来代替

#### XML 命名规则

XML 元素必须遵循以下命名规则：

- 名称可以包含字母、数字以及其他的字符
- 名称不能以数字或者标点符号开始
- 名称不能以字母 xml（或者 XML、Xml 等等）开始
- 名称不能包含空格

使名称具有描述性，名称应简短和简单：使用下划线的名称。

#### 属性

元数据（有关数据的数据）应当存储为属性，而数据本身应当存储为元素。

属性（Attribute）提供有关元素的额外信息。属性通常提供不属于数据组成部分的信息。

属性难以阅读和维护。请尽量使用元素来描述数据。而仅仅使用属性来提供与数据无关的信息。

- id 属性值唯一

#### 文本

CDATA 区：在该区域中的数据会被原样展示

- 格式：`<![CDATA[ 数据内容 ]]>`

CDATA 部分不能包含字符串 "]]>"。也不允许嵌套的 CDATA 部分。

标记 CDATA 部分结尾的 "]]>" 不能包含空格或换行。

### XML 验证

#### DTD 约束

DTD 的目的是定义 XML 文档的结构。它使用一系列合法的元素来定义文档结构：

内部 DTD：将约束规则定义在 xml 文档中

- ```xml
  <!DOCTYPE notes
  [
  <!ELEMENT notes (note*)>
  <!ELEMENT note (to,from,heading,body)>
  <!ELEMENT to (#PCDATA)>
  <!ELEMENT from (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
  ]>
  ```

外部 DTD：将约束的规则定义在外部的 DTD 文件中

- ```xml-dtd
  <!ELEMENT notes (note*)>
  <!ELEMENT note (to,from,heading,body)>
  <!ELEMENT to (#PCDATA)>
  <!ELEMENT from (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
  <!ALLLIST note number ID #REQUIRED>
  ```

XML 引入 DTD：

- 外部引入：`<!DOCTYPE 根标签名 SYSTEM "dtd文件的位置">`
- 网络资源：`<!DOCTYPE 根标签名 PUBLIC "dtd文件名字" "dtd文件的位置URL">`

#### Schema: 基于 XML 的 DTD 替代

作用是定义 XML 文档的合法构建模块，类似 DTD

- 定义可出现在文档中的元素
- 定义可出现在文档中的属性
- 定义哪个元素是子元素
- 定义子元素的次序
- 定义子元素的数目
- 定义元素是否为空，或者是否可包含文本
- 定义元素和属性的数据类型
- 定义元素和属性的默认值以及固定值

note.xsd ：文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="note">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="to" type="xs:string"/>
         <xs:element name="from" type="xs:string"/>
         <xs:element name="heading" type="xs:string"/>
         <xs:element name="body" type="xs:string"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>
</xs:schema>
```

XML 引入 Schema

- 填写 XML 文档的根元素
- 引入 xsi 前缀
  -  `xmlns:xs="http://www.w3.org/2001/XMLSchema-instance"`
- 引入 xsd 文件命名空间
  - `xs:schemaLocation="http://www.w3school.com.cn note.xsd"`
- 为每一个 xsd 约束声明一个前缀，作为标识
  - `xmlns="http://www.w3school.com.cn"`

```xml
<note xmlns="http://www.w3school.com.cn"
xmlns:xs="http://www.w3.org/2001/XMLSchema-instance"
xs:schemaLocation="http://www.w3school.com.cn note.xsd">
```

