---
title: " JQuery Ajax "
date: 2017-12-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - JQuery

---

### AJAX

AJAX (Asynchronous JavaScript and XML)是异步的 JavaScript 和 XML。

主要功能是在无需重新加载整个网页的情况下，使用 XMLHttpRequest 和服务器进行异步通讯，能够更新部分网页的技术。

### JS 原生 JQuery

工作流程：创建 XMLHttpRequest 对象、然后连接服务器、发送请求、接受服务器返回的数据。

#### XMLHttpRequest 对象

创建 XMLHttpRequest 对象：如果是 IE5 或者 IE6 浏览器，则使用 ActiveX 对象

- ```javascript
  var xmlHttp;
  if (window.XMLHttpRequest) {            //非IE
      xmlHttp = new XMLHttpRequest();
  } else if (window.ActiveXObject) {       //IE
      xmlHttp = new ActiveXObject("Microsoft.XMLHTTP")
  }
  ```

open(method,url,async) ： 建立前端到服务器的请求，有三个参数：

- 第一个参数定义发送请求所使用的方式(get/post)；
- 第二个参数设置文件在服务器端的位置 URL；
- 第三个参数设置是否对请求进行异步处理(true 异步 / false 同步)。

send(content) ：向服务器发送请求；当请求方式为 post 时需要设置参数；请求参数为 get 时不用设置参数

- `xmlHttp.send();`

readyState 属性：该属性代表着当前 xmlHttpRequest 的状态，每当 readystate 状态改变的时候，就会调用 onreadystatechange() 函数

- 0：请求未初始化(调用 open() 之前)
- 1：请求已经建立但是还没有发出 (调用 send() 之前)
- 2：请求已经发出
- 3：请求正在处理当中
- 4：请求已经被服务器处理完毕，相应准备就绪

回调函数：实现的功能就是接收后台处理后反馈给前台的数据，判断后台返回的信息是否正确，然后将数据显示到指定的 div 上。

- ```javascript
  xmlHttp.onreadystatechange = function() {
    if (xmlHttp.readyState == 4 && xmlHttp.status == 200) {
      var obj = document.getElementById("test");
      obj.innerHTML = xmlHttp.responseText;
    } else {
      alert("AJAX服务器返回错误！");
    }
  }
  ```

### JQuery AJAX

#### $.ajax()

```javascript
$.ajax({
  type:"post",  // 请求方式
  url:"getResource",  // 服务器的链接地址
  dataType:"json", // 传送和接受数据的格式
  data:{
    para1:"value1",
    para1:"value2"
  },
  success:function(data){ // 接受数据成功时调用的函数
  console.log(data);    // data为服务器返回的数据
  },
  error:function(request){// 请求数据失败时调用的函数
    alert("发生错误:"+request.status);
  }
});
```

#### $.get() 和 $.post()

提供两种简化的函数方式。

`$.get(url,[data],[callback],[dataType])`

`$.post(url,[data],[callback],[dataType])`

