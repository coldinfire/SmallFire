---
title: " JSON 详解 "
date: 2017-10-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - JSON

---

### JSON 简介

[JSON](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON)（JavaScript Object Notation）是一种轻量级的用于数据交换的文本格式，2001年由 Douglas Crockford 提出，目的是取代繁琐笨重的 XML 格式。与 XML 对比，它更小、更快，更易解析。易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

在浏览器和服务器之间交换数据时，数据只能是文本。由于JSON 仅是文本，因此可以轻松地与服务器之间进行发送和发送，并且可以通过任何编程语言将其用作数据格式。

一个 JSON 对象可以被储存在它自己的文件中，这基本上就是一个文本文件，扩展名为 `.json`， 还有特定的 MIME type：`application/json`.

### JSON 语法

#### 基本语法规则

JSON 语法是 JavaScript 对象表示法语法的子集。

- [json 数据是由键值对构成的](http://www.json.org/json-zh.html)，键/值分别用引号包含
- 数据由逗号分隔：多个键值对由逗号分隔
- 花括号保存对象：使用 { } 定义 json 格式
- 方括号保存数组：使用 [ ] 保存数组数据

#### 值的取值类型

- 数字（整数或浮点数）
- 字符串
- 逻辑值（true 或 false）
- 数组
- 对象
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

### JavaScript 获取 JSON 数据

可以链式访问 JSON 数据对象。

- json对象.键名


- json对象["键名"]


- json数组对象[索引]

#### 遍历 JSON 数据

```javascript
for(var key in jsonObj){
  // key 值为"key"格式
  alert(key + ":" + jsonObj[key])
}
```

