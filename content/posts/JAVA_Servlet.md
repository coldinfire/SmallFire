---
title: "Servlet 解析"
date: 2017-11-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Servlet

---

### Servlet 简介

Servlet 是 Java Web 的一种实现技术，可以接收浏览器发送过来的请求并给出响应。

### Servlet 的创建

#### Method 1 : 实现 Servlet 接口

Step1：创建类并实现 Servlet 接口

```java
public class TestServlet implements Servlet {
    @Override
    public ServletConfig getServletConfig() {
        return null;
    }
    @Override
    public String getServletInfo() {
        return null;
    }
    @Override
    public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
        System.out.println("service()方法被执行……");
    }
    @Override
    public void init(ServletConfig config) throws ServletException {
        System.out.println("init()方法被执行……");
    }
    @Override
    public void destroy() {
        System.out.println("destroy()方法被执行……");
    }
}
```

Step2：配置 web.xml 文件

```XML
<servlet>
  <servlet-name>demo1</servlet-name>
  <servlet-class>Servlet.TestServlet</servlet-class>
  <load-on-startup>3</load-on-startup>
</servlet>
<servlet-mapping>
  <servlet-name>demo1</servlet-name>
  <url-pattern>/demo1</url-pattern>
</servlet-mapping>
```

#### Method 2：继承 HttpServlet 抽象类

HttpServlet 是能够处理 HTTP 请求的 servlet，它在原有 Servlet 接口上添加了一些与 HTTP 协议处理方法。它比 Servlet 接口的功能更为强大。

由于开发的项目一般遵循 HTTP 协议，所以经常使用的是继承 HttpServlet类，而避免直接去实现 Servlet 接口。

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

### Servlet 的运行过程

![servlet excute](/images/Tomcat/tomcat_parse.png)

第一步：客户端向Web服务器(Tomcat)发出 HTTP 请求，服务器接收到请求后转给容器。

第二步：容器根据请求 URL 及 web.xml 里面的配置（或者通过注解）判断对应的Servlet是否存在，如果不存在则返回404，存在则执行第三步。

第三步：容器根据请求 URL 及 web.xml 里面的配置（或者通过注解）判断对应的 Servlet 是否已经被实例化。若是相应的 Servlet 没有被实例化，则容器将会加载相应的 Servlet 到 Java 虚拟机并实例化。

第四步：创建一个用于封装 HTTP 请求消息的 HttpServletRequest 对象和一个代表 HTTP 响应消息的 HttpServletResponse 对象，然后调用实例对象的 service() 方法并将请求和响应对象作为参数传递进去，然后开启一个新的线程去执行相关处理。

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

### Servlet API 解析

![](/images/Tomcat/Servlet_Interface.png)

#### Servlet接口

在 Servlet 接口中定义了5个方法，其中3个方法都是由 Servlet 容器来调用的，容器会在 Servlet 的生命周期的不同阶段调用特定的方法。

#### GenericServlet 抽象类

GenericServlet 抽象类为 Servlet 接口提供了通用实现，它与任何网络应用层协议无关。

#### ServletRequest 接口

ServletRequest 表示来自客户端的请求；当 Servlet 容器接收到客户端要求访问特定 Servlet 的请求时，容器先解析客户端的原始请求数据，把它包装成一个 ServletRequest 对象。

#### ServletResponse 接口

ServletResponse 对象封装了响应数据，可以获取到响应信息。

#### HttpServletRequest 接口

HttpServletRequest 接口扩展于 javax.servlet.ServletRequest接口，当客户端通过 HTTP 协议访问时，HttpServletRequest 对象封装了来自客户端的 HTTP 请求，该对象提供了用于读取 HTTP 请求中的相关信息的方法。

#### HttpServletResponse 接口

HttpServletResponse 接口扩展于 javax.servlet.servletResponse接口，HttpServletResponse 对象封装了来自服务端的 HTTP 响应。HttpServletResponse 接口提供了与 HTTP 协议相关的一些方法，Servlet 可通过这些方法来设置 HTTP 响应头或向客户端写 Cookie。

#### HttpServlet 抽象类

HttpServlet 抽象类是继承于 GenericServlet 抽象类而来的。使用HttpServlet 抽象类时，还需要借助请求对象 HttpServletRequest 和响应对象 HttpServletResponse。

HttpServlet 抽象类覆盖了 GenericServlet 抽象类中的 Service( )方法，并且添加了一个自己独有的 Service(HttpServletRequest req，HttpServletResponse resp)方法。

在 HttpServlet 的 service(HttpServletRequest req, HttpServletResponse resp) 方法会去判断当前请求是 GET 还是 POST，如果是 GET 请求，那么会去调用本类的 doGet() 方法，如果是 POST 请求会去调用doPost() 方法，这说明在子类中去重写doGet()或doPost()方法即可。

