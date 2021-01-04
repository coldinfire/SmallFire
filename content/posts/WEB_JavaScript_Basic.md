---
title: "JavaScript学习与总结"
date: 2017-09-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JS

---

JavaScript 是一个脚本语言。它是一个轻量级，但功能强大的编程语言。

### JavaScript 书写规范

1. 文件编码统一为 **utf-8**, 书写过程过，每行代码结束必须有分号；

2. 库引入：原则上仅引入 jQuery 库，若需引入第三方库，须与团队其他人员讨论决定；

3. 变量命名：驼峰式命名。要求变量集中声明，避免全局变量

   - 原生 JavaScript 变量要求是纯英文字母，首字母须小写，如 iJavaScript;

   - jQuery 变量要求首字符为’_’, 其他与原生 JavaScript 规则相同，如: _iJaveScript;

4. 类命名：首字母大写，驼峰式命名。如 IJaveScript;

5.  函数命名：首字母小写驼峰式命名。如 iJaveScript ();

6. 命名语义化，尽可能利用英文单词或其缩写；

7. 尽量避免使用存在兼容性及消耗资源的方法或属性，比如 eval () & innerText;

8. 后期优化中，JavaScript 非注释类中文字符须转换成 unicode 编码使用，以避免编码错误时乱码显示；

9. 代码结构明了，加适量注释。提高函数重用率；

10. 注重与 html 分离，减小 reflow, 注重浏览器性能。

### JavaScript 脚本执行顺序

第一步定义： 分为变量定义和函数定义

第二步执行： 先从上往下定义完所有的内容后，会从上往下执行；除了变量和函数定义外的其他内容都会执行；如：赋值、函数调用

第三步查找：能在栈里面找到的，就不去堆里面找，因为栈空间小，就近原则会在栈里面开辟，就能找到堆里面的地址

### JavaScript显示数据

JavaScript 可以通过不同的方式来输出数据：

- 使用 **window.alert()** 弹出警告框。
- 使用 **document.write()** 方法将内容写到 HTML 文档中。
  - 绝对不要在文档(DOM)加载完成之后使用 document.write()。这会覆盖该文档。
- 使用 **.innerHTML** 写入到 HTML 元素。
- 使用 **console.log()** 写入到浏览器的控制台。