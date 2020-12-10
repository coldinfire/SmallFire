---

title: "JavaScript学习与总结"
date: 2017-09-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JS

---

## JavaScript 书写规范

1. 文件编码统一为 **utf-8**, 书写过程过，每行代码结束必须有分号；原则上所有功能均根据 XXX 项目需求原生开发，以避免网上 down 下来的代码造成的代码污染 (冗余代码 || 与现有代码冲突 || …);

2. 库引入：原则上仅引入 jQuery 库，若需引入第三方库，须与团队其他人员讨论决定；

3. 变量命名：驼峰式命名。要求变量集中声明，避免全局变量.

   - 原生 JavaScript 变量要求是纯英文字母，首字母须小写，如 iJavaScript;

   - jQuery 变量要求首字符为’_’, 其他与原生 JavaScript 规则相同，如: _iJaveScript;

4. 类命名：首字母大写，驼峰式命名。如 IJaveScript;

5.  函数命名：首字母小写驼峰式命名。如 iJaveScript ();

6. 命名语义化，尽可能利用英文单词或其缩写；

7. 尽量避免使用存在兼容性及消耗资源的方法或属性，比如 eval () & innerText;

8. 后期优化中，JavaScript 非注释类中文字符须转换成 unicode 编码使用，以避免编码错误时乱码显示；

9. 代码结构明了，加适量注释。提高函数重用率；

10. 注重与 html 分离，减小 reflow, 注重性能。