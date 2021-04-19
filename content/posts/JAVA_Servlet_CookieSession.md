---
title: "Servlet Cookie&Session对象"
date: 2017-11-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Servlet
---



### Cookie：客户端会话技术 

将数据保存在客户端的会话技术。一般用于存储少量的不太敏感的数据信息。可以在用户不登录的情况下，完成服务器对客服端的身份识别。

基于响应头 set-cookie 和请求头 cookie 实现。

默认情况下，当浏览器关闭后，Cookie 数据被销毁。可以通过设置 Cookie 保存时间，设置其生命周期。

浏览器限制

- 浏览器对于单个 cookie 的大小有限制(4KB)
- 同一个域名下的总 cookie 数量有限制(20)

#### Cookie 操作步骤

创建 Cookie 对象，绑定数据

- new Cookie(String name, String value)：创建 Cookie 对象，并保存数据值

发送 Cookie，会在响应头设置 set-cookie:name=value 的响应头消息。

- response.addCookie(Cookie cookie);
- 可以在一次响应中创建多个 Cooike 对象，使用 response 发送多个 cooike。

获取 Cookie 对象获取数据，当浏览器发出请求时，会将 Cookie 信息放在请求头 cookie 中。

- Cookie[] request.getCookies();

#### Cookie 对象方法

- setMaxAge(int seconds)：设置 Cookie 对象的存活时间，默认值是 -1
  - 正数：设置 Cookie 的存活时间
  - 零：删除 Cookie 信息
  - 负数：cookies auto-expire，当浏览器关闭时，自动删除
- getMaxAge()：获取 Cookie 对象的存活时间

#### Cookie 共享

同一个 tomcat 服务器中共，多个 Web 项目

- 默认设置为当前的虚拟目录，此时 cookie 不能共享
- setPath(String path)：设置 cookie 的获取范围为 “/”，此时可以共享

不同的 tomcat 服务器间共享

- setDomain(String path)：如果设置一级域名相同，那么多个服务器之间 cookie 可以共享

### Session：服务器端会话技术