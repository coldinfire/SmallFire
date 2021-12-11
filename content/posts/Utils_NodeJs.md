---
title: " NodeJS 安装使用 "
date: 2018-02-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 前端

tags: 
  - NodeJs

---

### Node.js 使用

简单的说 Node.js 就是运行在服务端的 JavaScript。

Node.js 是一个基于 Chrome JavaScript 运行时建立的一个平台。

Node.js 是一个事件驱动 I/O 服务端 JavaScript 环境，基于 Google 的 V8 引擎，V8 引擎执行 Javascript 的速度非常快，性能非常好。

#### Windows 安装 Node.js

下载安装包：[https://nodejs.org/en/download/](https://nodejs.org/en/download/)

![NodeJS Download](/images/WEB/NodeJs_Downloads.png)

使用下载的安装包执行并安装，安装过程中选择将安装目录添加到 PATH 中，检测 PATH 环境变量是否配置了 Node.js，打开 cmd 输入 `node --version` 检查 Node.js 版本。

![NodeJS Version](/images/WEB/NodeJs_Version.png)

### 创建 Node.js 应用

使用 Node.js 时，我们不仅仅 在实现一个应用，同时还实现了整个 HTTP 服务器。事实上，我们的 Web 应用以及对应的 Web 服务器基本上是一样的。

Node.js 应用组成部分：

- 引入 required 模块：可以使用 require 指令来载入 Node.js 模块。

- 创建服务器：服务器可以监听客户端的请求，类似于 Apache 、Nginx 等 HTTP 服务器。

- 接收请求与响应请求：服务器很容易创建，客户端可以使用浏览器或终端发送 HTTP 请求，服务器接收请求后返回响应数据。

```javascript
var http = require("http");
http.createServer(function (request,response){
	response.writeHead(200,{'Content-Type': 'text/plain'});
	response.end('Hello World\n');
} ).listen(8081);
console.log('Server running at http://127.0.0.1:8081/');
```

- 请求（require）Node.js 自带的 http 模块，并且把它赋值给 http 变量。
- 接下来调用 http 模块提供的函数： createServer 。这个函数会返回 一个对象，这个对象有一个叫做 listen 的方法，这个方法有一个数值参数， 指定这个 HTTP 服务器监听的端口号。