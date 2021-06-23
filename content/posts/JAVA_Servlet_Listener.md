---
title: "Listener 监听器"
date: 2017-11-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Servlet

---

### 监听器使用

基于观察者模式设计的，Listener 的设计对开发 Servlet 应用程序提供了一种快捷的手段，能够方便的从另一个纵向维度控制程序和数据。

Listener是Servlet的监听器，它可以监听客户端的请求、服务端的操作等。通过监听器，可以自动激发一些操作。

监听器的主要组件：

- 事件源：被监听的对象；主要三个域对象 request、session、 servletContext
- 监听器：监听事件源对象；事件源对象的状态的变化都会触发监听器
- 响应行为：监听器监听到事件源的状态变化时所涉及的功能代码

### 使用步骤

#### Step1：定义一个 java 类实现需要监听的接口

目前 Servlet 中提供了两类事件的观察者接口

![Listener](/images/JAVA/Listener.png)

- ServletContextListener：监听 ServletContext 对象的生命周期过程

- ServletContextAttributeListener：监听对 application 作用域中属性的操作，如增加、删除、修改属性
- ServletReuestListener：监听请求对象的生命周期
- ServletReuestAttributeListener：监听 request 作用域中属性的操作，如增加、删除、修改属性
- HttpSessionListener：监听 HttpSession 对象的生命周期过程
- HttpSessionAttributeListener：监听 session 作用域中属性的操作，如增加、删除、修改属性

#### Step2：拦截路径配置

方法一、注解方式：`@WebListener`

方法二、配置 web.xml 文件；需要完整的限定类名，也可以指定初始化上下文参数。

- ```xml
  <listener>
    <listener-class>test.ListenerDemo</listener-class>
  </listener>
  <context-param>
    <param-name>contextConfig</param-name>
    <param-value>/WEB-INF/classes/applicationConfig.xml</param-value>
  </context-param>
  ```


获取 context-param 中的配置信息：

- ServletContext servletContext = servletContextEvent.getServletContext();
- String initParameter = servletContext.getInitParameter("contextConfig");
- String realPath = servletContext.getRealPath(initParameter);

### 配置文件的加载顺序

- context-param -> listener -> filter -> servlet