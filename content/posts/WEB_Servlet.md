---
title: "Servlet 解析"
date: 2017-11-02
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Servlet
---



### Servlet 简介

Servlet是Java Web的一种实现技术，可以接收浏览器发送过来的请求并给出响应。

### Servlet 的创建

#### Method 1 : 实现 Servlet 接口

Step1：创建类并实现 Servlet

```java
public class ServletDemo1 implements Servlet { ... }
```

Step2：重写 service() 方法

```java
@Override
public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("Hello World");
}
```

Step3：配置 web.xml 文件

```XML
<servlet>
  <servlet-name>demo1</servlet-name>
  <servlet-class>Servlet.ServletDemo1</servlet-class>
  <load-on-startup>3</load-on-startup>
</servlet>
<servlet-mapping>
  <servlet-name>demo1</servlet-name>
  <url-pattern>/demo1</url-pattern>
</servlet-mapping>
```

#### Method 2：继承 HttpServlet 抽象类

HttpServert 抽象类对 http 协议进行封装，简化操作。

```java
@WebServlet("/demo2")
public class HelloServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
    /**
     * @see HttpServlet#HttpServlet()
     */
    public HelloServlet() {
        super();
        // TODO Auto-generated constructor stub
    }
	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		// 使用 GBK 设置中文正常显示
		response.setCharacterEncoding("GBK");
		response.getWriter().append("Served at: ").append(request.getContextPath());
        response.getWriter().write("Test Servlet");
	}
	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}
}
```

#### 通过注解的方式配置 Servlet 访问资源路径

`@WebServlet(urlpartten)`

一个Servlet 可以定义多个访问路径： @Webservlet({"/a","/b"})

路径定义规则

- /xxx：单层路径
- /xxx/xxx：多层路径，目录结构
- *.do

### Servlet的运行过程

第一步：浏览器向Web服务器(Tomcat)发出请求，服务器接收到请求后转给容器。

第二步：容器根据请求 URL 及 web.xml 里面的配置（或者通过注解）判断对应的Servlet是否存在，如果不存在则返回404，存在则执行第三步。

第三步：容器根据请求 URL 及 web.xml 里面的配置（或者通过注解）判断对应的 Servlet 是否已经被实例化。若是相应的 Servlet 没有被实例化，则容器将会加载相应的 Servlet 到 Java 虚拟机并实例化；如果已经实例化了，则执行第四步。

第四步：调用实例对象的 service() 方法，并开启一个新的线程去执行相关处理，并将执行结果反馈给浏览器。

第五步：service() 方法执行完后，会执行Servlet中的destroy()，销毁线程或被放在线程池中。

### Servlet 生命周期

Servlet 在容器中经历如下几个过程：

第一步：http 请求到达服务器，容器先加载 Servlet 类

第二步：容器实例化 Servlet (Servlet无参构造函数执行)

第三步：执行 Servlet 中的 init() 方法（在 Servlet 生命周期中，只执行一次，且在 service() 方法执行前执行），Servlet 实例化

第四步：执行 Servlet 中的 service() 方法（每一次 Servlet 被访问时执行），处理客户请求；doPost() 或 doGet()

第五步：服务器正常关闭时，执行 Servlet 中的 destroy()，销毁线程

#### init() 方法

对 Servlet 实例化，可以在配置文件中指定其创建时机。

- 第一次被访问时，创建；`load-on-startup`值为负数，默认为 -1
- 在服务器启动时，创建；`load-on-startup`值为0或正整数

### Servlet 原理，继承关系

