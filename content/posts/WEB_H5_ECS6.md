---
title: "ECMAScript 6 知识了解"
date: 2021-03-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JS

---

### 新增变量声明

#### let 命令

`let`命令，用来声明变量。它的用法类似于 var，但是所声明的变量，只在 let 命令所在的代码块内有效。

```javascript
var arr = [1,'a','b'];
for (let i = 0; i < arr.length; i++) {
    console.log(i);
}
console.log(i);
```

*不允许重复声明*

let 不允许在相同作用域内，重复声明同一个变量。

*暂时性死区*

变量一定要在声明后使用。

ES6明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

#### const 命令

`const`声明一个只读的常量。一旦声明，常量的值就不能改变。因此 const 一旦声明变量，就必须立即初始化。

### 变量的解构赋值