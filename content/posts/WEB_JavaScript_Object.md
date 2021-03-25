---
title: "JavaScript对象"
date: 2017-09-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JS
---

### JavaScript 对象

JavaScript的对象是一种带有属性和方法的无序的集合数据类型，它由若干键值对组成。

- 属性是与对象相关的值
- 方法是能够在对象上执行的动作，是附加在对象上的函数

所有 JavaScript 中的对象都是位于原型链顶端的 Object 的实例

JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。

`Date` 对象, `Array` 对象, 以及 `String` 对象从 `Object.prototype` 继承。

#### 给已有的对象增加属性或者方法

- 格式：**构造函数名.prototype.新属性或者新方法**

### 创建 JavaScript 对象

JavaScript 允许自定义对象。

创建新对象有两种不同的方法：

- 使用 Object 定义并创建对象的实例

  - 使用 Object 构建函数

    - ```javascript
      person=new Object();
      person.firstname="John";
      person.lastname="Doe";
      person.age=50;
      person.eyecolor="blue";
      ```

  - 使用对象字面量创建

    - var person={firstname:"John",lastname:"Doe",age:50,eyecolor:"blue"};
    - 用一个大括号内容表示一个对象，键值对以`xxx: xxx`形式申明，用逗号隔开。
    - 访问属性是通过`.`操作符完成的，要求属性名必须是一个有效的变量名。

- 使用函数来定义对象，然后创建新的对象实例

  - ```javascript
    function person(firstname,lastname,age,eyecolor){
        this.firstname=firstname;
        this.lastname=lastname;
        this.age=age;
        this.eyecolor=eyecolor;
        this.changeName=changeName;
        function changeName(name)
        {
            this.lastname=name;
        }
    }
    ```

    var john = new person("John","Doe",50,"blue");

    john.changeName("Elieen");



### JavaScript 内建对象

| 类型     | 描述           | 类型   | 描述         |
| :------- | :------------- | ------ | :----------- |
| Function | 函数(方法)对象 | Number | 包装类对象   |
| Array    | 数组对象       | String | 字符串对象   |
| Boolean  | Boolean对象    | RegExp | 正则表达对象 |
| Date     | 日期对象       | Global |              |
| Math     | 数学对象       | JSON   | json对象     |

### Function 函数(方法)对象

#### 创建

Method 1: `function method_name(形式参数列表){ 方法体 }`

Method 2: `var method_name = function(形式参数列表){ 方法体 }`

#### 属性

`arguments.length` : 代表形参的个数

#### 特点

- 方法定义时，形参的类型不用写。

- 方法是一个对象，如果定义名称相同的方法，会覆盖。

- 方法的调用只与方法的名称有关，和参数列表无关。

- 在方法声明中有一个隐藏的内置对象(数组) `arguments`，封装所有的实际参数

#### 调用

方法名称(实际参数列表);

#### Demo

```javascript
var fun1 = function(a,b){
	alert(a + b);
    alert(arguments[0] * arguments[1]);
};
fun1(3,4); // 7,12
```

### 数组对象

数组是一个用于存储不同数据类型的变量。它将不同的元素存储在一个盒子中，供以后使用。JavaScript 的数组可以包括任意数据类型；数组的元素可以通过索引来访问。

#### 创建

Method 1 : `var arr = ['AAA',2,null,true];`

Method 2: `var arr = new Array(元素列表);`

Method 3: `var arr = new Array(默认长度);`

#### 属性和方法列表

| Method            | Desc                                         | Method             | Desc                                                         |
| :---------------- | :------------------------------------------- | :----------------- | :----------------------------------------------------------- |
| length;           | 数组长度                                     | sort();            | 对数组进行排序，默认顺序                                     |
| indexOf(2);       | 搜索指定元素的索引位置                       | reverse();         | 将数组内容进行反转                                           |
| slice(0,3);       | 截取数组的部分元素，返回新的数组；左闭右开； | splice(n,m,p1,p2); | 从指定的索引开始删除若干元素，然后再从该位置添加若干元素     |
| push('A','B');    | 向数组末尾添加元素，返回新的数组长度         | concat(arrOther);  | 把当前的数组和另一个数组连接起来，生产并返回一个新的数组     |
| pop();            | 删除数组最后一个元素并返回元素值             | join(separate);    | 把当前数组的每个元素都用指定的分隔符连接起来，然后返回连接后的字符串 |
| unshift('A','B'); | 在数组头部添加若干元素，返回新的数组长度     | toString();        | 将数组转为字符串，以逗号分隔                                 |
| shift();          | 删除数组第一个元素并返回元素值               |                    |                                                              |

#### 特点

- JS中，数组元素的类型是可变的。

- JS中，数组的长度是可变的。

### Date 对象

#### 创建

Method 1: `var date = new Date();`

Method 2: `var date = new Date(2016,6,20,20,30,15,123);`

#### 方法和属性列表

| Method          | Desc                              | Method       | Desc                      |
| :-------------- | :-------------------------------- | :----------- | :------------------------ |
| toLocalString() | 根据本地时间格式，转换为字符串    | getTime()    | 返回1970.1.1~今的毫秒数   |
| getDay()        | 从Date对象返回一周中的某一天(0~6) | getMonth()   | 从Date对象返回月份(0~11)  |
| getFullYear()   | 从Date对象以思维数字返回年份      | getSeconds() | 返回Date对象的秒数(0~59)  |
| getHours()      | 返回 Date对象的小时(0~23)         | getMinutes() | 返回 Date对象的分钟(0~59) |

### Math对象

#### 创建

Math对象不用创建，直接使用 `Math.方法名();`

#### 方法列表

| Method   | Desc                        | Method   | Desc                   |
| :------- | :-------------------------- | :------- | :--------------------- |
| abx(x)   | 返回 x 的绝对值             | max(x,y) | 返回 x 和 y 中的最大值 |
| ceil(x)  | 对 x 进行上设入             | min(x,y) | 返回 x 和 y 中的最小值 |
| floor(x) | 对 x 进行下舍入             | pow(x,y) | 返回 x 的 y 次幂       |
| round(x) | 把 x 四舍五入为最接近的整数 | random() | 返回[0~1)之间的随机数  |

### String 对象

JavaScript 为字符串提供了一些常用方法。注意：调用这些方法本身不会改变原有字符串的内容，而是返回一个新字符串。

**字符串是不可变的** ，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果。

`var s = 'Hello world';`

| Method                   | Desc                                               | Method            | Desc                                                         |
| ------------------------ | -------------------------------------------------- | ----------------- | ------------------------------------------------------------ |
| s.length;                | 获取字符串的长度                                   | substring(0,5);   | 截取指定索引区间的字串                                       |
| toUpperCase();           | 将字符串转为大写                                   | toLowerCase();    | 将字符串转为小写                                             |
| s[n];                    | n是索引号，获取字符串指定位置字符，索引号从 0 开始 | indexOf('world'); | 搜索指定字符出现的首字母位置，如果没有找到返回 -1            |
| split(separator, limit); | 将一个字符串分成子串并将它们作为数组返回           | match('XX');      | 查找字符串中特定的字符，如果找到则返回匹配到字符，否则返回 null |
| replace();               | 在字符串中用某些字符替换另一些字符                 | concat()          | 拼接字符串                                                   |

### RegExp对象

正则表达式（regular expression）: 定义字符串的组成规则

#### 创建

Method 1: `var reg = new RegExp("^\\w{6,12}$","i");`

Method 2: `var reg = /^\w{6,12}$/gi;`

RegExp 修饰符 : 修饰符用于执行不区分大小写和全文的搜索。

- **i** - 修饰符是用来执行不区分大小写的匹配。

- **g** - 修饰符是用于执行全文的搜索（而不是在找到第一个就停止查找,而是找到所有的匹配）。

#### 方法

| Method | Desc                                                         |
| :----- | ------------------------------------------------------------ |
| test() | 验证指定的字符是否符合正则定义的规范，根据结果并返回true或false |
| exec() | 检索字符串中的指定值。返回值是被找到的值。如果没有发现匹配，则返回 null。 |

#### 正则表达式：方括号

用于查找某个范围内的字符：

| 表达式 | 描述                     | 表达式  | 描述                         |
| :----- | :----------------------- | :------ | :--------------------------- |
| [abc]  | 查找方括号之间的任何字符 | [A-Z]   | 查找从大写 A 到 Z 的字符     |
| [^abc] | 查找不在方括号之间的字符 | [A-z]   | 查找从大写 A 到小写 z 的字符 |
| [0-9]  | 查找从0至9的数字         | [adgk]  | 查找给定集合内的任意字符     |
| [a-z]  | 查找从小写 a 到 z 的字符 | [^adgk] | 查找给定集合外的任意字符     |

#### 正则表达式：特殊符号

| 单个字符   | 描述                             | 量词符号 | 描述                     |
| :--------- | :------------------------------- | :------- | :----------------------- |
| \D、\d     | 查找(非)单个数字字符[0-9]        | n？      | 匹配 n 出现 0 次或 1 次  |
| \W、\w     | 查找(非)单个单词字符[a-zA-Z0-9_] | n*       | 匹配 n 出现 0 次或多次   |
| \S、\s     | 查找(非)空白字符                 | n+       | 匹配 n 出现 1 次或多次   |
| \0、\n、\f | 查找NULL,换行,换页符             | n{m,n}   | 表示 m<= 数量 <= n       |
| \r、\t、\v | 查找回车、制表、垂直制表符       | ^n、n$   | 匹配以n开头/结尾的字符串 |

### Global对象

特点：全局对象，全局属性和函数可用于所有内建的 JavaScript 对象。

#### 方法

| Method               | Desc                   | MethodDesc           |                                    |
| -------------------- | ---------------------- | -------------------- | ---------------------------------- |
| encodeURI()          | url编码                | decodeURI()          | url解码                            |
| encodeURIComponent() | url编码，编码字符更多  | decodeURIComponent() | url解码                            |
| escape()             | 对字符串进行编码       | unescape()           | 对编码的字符串进行解码             |
| Number()             | 将对象的值转换为数字   | parseInt()           | 解析一个字符串并返回一个整数       |
| String()             | 将对象的值转换为字符串 | parseFloat()         | 解析一个字符串并返回一个浮点数     |
| isNaN()              | 判断某个值是否是 数字  | eval()               | 将 JavaScript 字符串转成脚本来执行 |

### JSON 对象

#### JSON数据类型

number、boolean、string、null、array、object



### 对象操作

#### Map & Set

`Map`是一组键值对的结构，具有极快的查找速度。

`Set`和`Map`类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在`Set`中，没有重复的key。

#### 迭代：Iterable

ES6标准引入了新的`iterable`类型，Array、Map 和 Set都属于 iterable类型。

具有iterable类型的集合可以使用 iterable 内置的`forEach`方法，它接收一个函数，每次迭代就自动回调该函数。循环来遍历。

```javascript
'use strict';
var a = ['ele1', 'ele2', 'ele3'];
// element: 指向当前元素的值
// index: 指向当前索引
// array: 指向Array对象本身
a.forEach(function (element, index, array) {
    console.log(element + ', index = ' + index);
});
```
