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

Node.js 应用程序在单个进程中运行，无需为每个请求创建新的线程。 Node.js 在其标准库中提供了一组异步的 I/O 原语，以防止 JavaScript 代码阻塞，通常，Node.js 中的库是使用非阻塞范式编写的，使得阻塞行为成为异常而不是常态。

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

#### 实例代码

```javascript
const http = require('http')
const hostname = '127.0.0.1'
const port = 3000
const server = http.createServer((req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'text/plain')
  res.end('Hello World\n')
})
server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`)
});
```

要运行此代码片段，需要将其另存为 JS 文件并在终端中运行 `node xxx.js`；当运行命令时，请确保位于包含 `app.js` 文件的目录中。

#### 代码结构解析

- 请求（require）Node.js 自带的 http 模块，并且把它赋值给 http 变量。
- 接下来调用 http 模块提供的函数： createServer 。这个函数会返回 一个对象，这个对象有一个叫做 listen 的方法，这个方法有一个数值参数， 指定这个 HTTP 服务器监听的端口号。

- 每当接收到新请求时，都会调用 `request` 事件，其提供两个对象：请求（`http.IncomingMessage` 对象）和响应（`http.ServerResponse`对象）。这两个对象对于处理 HTTP 调用是必不可少的。
  - 第一个提供请求的详细信息。可以访问请求头和请求数据。
  - 第二个用于向调用者返回数据。