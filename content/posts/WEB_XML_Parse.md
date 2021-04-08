---
title: "XML文档解析"
date: 2017-10-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - XML
---



### 解析 XML 文档

将文档中的数据读取到内存中。

#### DOM 解析

DOM 解析原理：xml 解析器一次性把整个 xml 文档加载进内存，然后在内存中构建一颗 Document 的对象树，通过 Document 对象，得到树上的节点对象，通过节点对象访问（操作）到 xml 文档的内容

- 优点：操作方便，可以对文档进行 CRUD 的所有操作
- 缺点：占用内存，如果XML文件比较大，容易影响解析性能且可能会造成内存溢出

#### SAX 解析

逐行读取，基于事件驱动。SAX 的工作原理简单地说就是对文档进行顺序扫描，当扫描到文档开始与结束、元素开始与结束、文档（document）结束等地方时通知事件处理函数，由事件处理函数做相应动作，然后继续同样的扫描，直至文档结束。

- 优点：内存消耗小，解析速度快，SAX解析器是对文档的解析过程是一种边解析边执行的过程
- 缺点：只能读取，不能增删改；不能随机访问；必须实现事件处理程序

#### DOM4J 解析器

与利用 DOM、SAX 来解析 xml 相比，[DOM4J](https://dom4j.github.io/) 表现更优秀，具有性能优异、功能强大和极端易用使用的特点，只要懂得 DOM 基本概念，就可以通过 dom4j 的  [api 文档](https://dom4j.github.io/javadoc/2.0.3/)  来解析 xml。dom4j 是一套开源的 api。

Step1 : 下载并导入 JAR 包

- Dom4j 需下载相应的 jar 文件：[https://dom4j.github.io/](https://dom4j.github.io/)。

Step2：获取 document 对象

Document：文档对象，代表内存中的 DOM 树

| Method                             | Code                                                         |
| :--------------------------------- | :----------------------------------------------------------- |
| 读取XML文件,获得document对象       | SAXReader reader = new SAXReader();                          |
|                                    | Document document = reader.read(url);                        |
|                                    | String encoding = document.getXMLEncoding();                 |
| 解析XML形式的文本,得到document对象 | String text = "XML_String";                                  |
|                                    | Document document = DocumentHelper.parseText(text);          |
| 主动创建document对象.              | Document document = DocumentHelper.createDocument();         |
|                                    | Element root = document.addElement("members");               |
|                                    | root.addElement("author").addAttribute("name","value").addText("text"); |

Step3：节点对象操作

Element：节点元素对象

| Method                       | Code                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| 获取文档的根节点             | Element root = document.getRootElement();                    |
| 获取某个节点的子节点         | Element element = root.element("student"); element.element("name"); |
| 获取节点的文字               | String text = element.getText();                             |
| 设置节点文字                 | element.setText("text");                                     |
| 取得某节点下相同名称的子节点 | List nodes = root.elements("member");                        |
| 在某节点下添加子节点         | Element agetElement = element.addElement("age");             |
| 删除某节点                   | parentElement.remove(childElement);                          |

Step4：节点对象属性操作

Attribute：节点元素的属性值对象

| Method               | Code                                          |
| :------------------- | :-------------------------------------------- |
| 取得某节点下的某属性 | Attribute attribute = root.attribute("size"); |
| 获取某节点的所有属性 | List attributes = root.attributes();          |
| 取得属性的文字       | String text = attribute.getValue();           |
| 设置属性的文字       | attribute.setValue("text");                   |
| 设置节点属性和文字   | element.addAttribute("name","value");         |
| 删除某属性           | root.remove(attribute);                       |

### 写入 XML 文档

将内存中的数据保存到 xml 文档中。持久化存储。

```java
import org.dom4j.Document;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.XMLWriter;

public class Foo {
    public void write(Document document) throws IOException {
        // lets write to a file
        FileWriter fileWriter = new FileWriter("output.xml"); 
        XMLWriter writer = new XMLWriter(fileWriter);
        writer.write( document );
        writer.close();
        // Pretty print the document to System.out
        OutputFormat format = OutputFormat.createPrettyPrint();
        format.setEncoding("GBK");
        writer = new XMLWriter(System.out, format);
        writer.write( document );
        // Compact format to System.out
        format = OutputFormat.createCompactFormat();
        writer = new XMLWriter(System.out, format);
        writer.write(document);
        writer.close();
    }
}
```

