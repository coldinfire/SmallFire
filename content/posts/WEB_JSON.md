---
title: "JSON 详解"
date: 2017-12-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - JSON

---

### JSON 简介

[JSON](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON)（JavaScript Object Notation）是一种轻量级的用于数据交换的文本格式，2001年由 Douglas Crockford 提出，目的是取代繁琐笨重的 XML 格式。

JSON 格式的文本文件易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

在浏览器和服务器之间交换数据时，数据只能是文本。由于JSON 仅是文本，因此可以轻松地与服务器之间进行发送和发送，并且可以通过任何编程语言将其用作数据格式。

### JSON 语法

#### 基本语法规则

- json 数据是由键值对构成的，键/值分别用引号包含
- 数据由逗号分隔：多个键值对由逗号分隔
- 花括号保存对象：使用 { } 定义 json 格式
- 方括号保存数组：使用 [ ] 保存数组数据

#### 值的取值类型

- 数字（整数或浮点数）
- 字符串（在双引号中）
- 逻辑值（true 或 false）
- 数组（在方括号[ ]中）
- 对象（在花括号中{ }）
- null

示例代码：

```json
{
  "browsers": {
    "firefox": {
      "name": "Firefox",
      "pref_url": "about:config",
      "releases": {
        "1": {
          "release_date": "2004-11-09",
          "status": "retired",
          "engine": "Gecko",
          "engine_version": "1.7"
        }
      }
    }
  }
}
```

### 获取 JSON 数据

json对象.键名

json对象["键名"]

数组对象[索引]