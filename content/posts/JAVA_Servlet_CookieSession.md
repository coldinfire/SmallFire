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

将数据保存到客户端的会话技术。

#### 创建 Cookie 对象，绑定数据

- new Cookie(String name, String value)：创建 Cookie 对象，并保存数据值

#### 发送 Cookie

- response.addCookie(Cookie cookie);

#### 获取 Cookie 对象获取数据

- Cookie[] request.getCookies();

### Session：服务器端会话技术