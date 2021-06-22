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





### 使用步骤

#### Step1：定义一个 java 类实现需要监听的接口



#### Step2：拦截路径配置

方法一、注解方式：`@WebListener`

方法二、配置 web.xml 文件；需要完整的限定类名，也可以指定初始化参数。

- ```xml
  <listener>
    <listener-class>test.ListenerDemo</listener-class>
  </listener>
  <context-param>
    <param-name>contextConfig</param-name>
    <param-value>/WEB-INF/classes/applicationConfig.xml</param-value>
  </context-param>
  ```

  