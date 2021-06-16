---
title: "BeanUtil工具类使用"
date: 2017-10-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - javabasic

---

### BeanUtils的使用

commons-beanutils 是 Apache 组织下的一个基础的开源库，它提供了对 Java 反射和内省的 API 的包装，依赖内省，其主要目的是利用反射机制对 JavaBean 的属性进行处理。

BeanUtils 是 commons-beanutils 包下的一个工具类，如果想在我们的项目中使用这个类需要导入以下两个 jar 包拷贝到 WEB-INF/lib 下：

- [commons-beanutils.jar ](http://commons.apache.org/proper/commons-beanutils/)

- [commons-logging.jar](http://commons.apache.org/proper/commons-logging/)

#### 获取某个对象的某个属性 

- setProperty(Object bean,String name,Object value) 方法对 javaBean 属性进行设置
- getProperty(Object bean,String name) 方法获取 javaBean 的属性
- populate(Object bean,Map properties) 静态方法，Map 封装请求参数的 Map 集合；Map 中的 key 值要与 JavaBean 中的属性名称保持一致，否则封装不进去
- request.getParameterMap() 方法获取一个封装了所有请求参数的 Map 对象

