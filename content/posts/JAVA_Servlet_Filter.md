---
title: " Filter 过滤器 "
date: 2017-11-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Servlet

---

### Filter 使用

Filter 是 Servlet2.3 新增加的功能，它不是 Servlet，不能处理用户请求，也不能处理用户响应。主要对 HttpServletRequest 进行预处理，也对 HttpServletResponse 进行后续处理，是典型的处理链。

Filter 的工作内容

- 在 servlet 被调用之前截获；
- 在 servlet 被调用之前检查 ServletRequest；
- 根据需要修改 request 头和 request 数据； 
- 根据需要修改 response 头和 response 数据；
- 在 servlet 被调用之后截获；

WEB 中的过滤器：它处于客户端与服务器资源文件之间的一道过滤网，当访问服务器资源之前，通过一系列的过滤器对请求进行修改、判断等，把不符合规则的请求在中途拦截或修改。也可以对响应进行过滤，拦截或修改响应。

WEB 开发人员可以通过 Filter 技术，对 web 服务器管理的所有 web 资源：例如 Jsp、Servlet、静态图片文件或静态 html 文件、字符编码转换等进行过滤，从而实现一些特殊的功能，还可以实现 URL 级别的权限访问控制、过滤敏感词汇、防止脚本攻击的过滤器、压缩响应信息等一些高级功能。

### 使用步骤

#### Step1：定义一个 java 类实现 Filter 接口

通过覆写其 doFilter 方法进行过滤功能的实现。必须执行 `chain.doFilter(request, response);` 对请求进行放行。

#### Step2：拦截路径配置

方法一、注解方式：`@WebFilter(value = "/*",dispatcherTypes = { DispatcherType.REQUEST })`

方法二、配置 web.xml 文件；在 web.xml 文件中对 Filter 类进行注册，并设置它所能拦截的资源。web 服务器根据 Filter 在 web.xml 文件中的注册顺序，决定先调用哪个Filter。

- ```xml
  <filter>
    <filter-name>demo1</filter-name>
    <filter-class>domain.FilterDemo1</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>demo1</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

- | Tag                | Describe                                                     |
  | ------------------ | ------------------------------------------------------------ |
  | `<filter>`         | Filter 对应的 xml 标签                                       |
  | `<filter-name>`    | 为 Filter 设置名称                                           |
  | `<filter-class>`   | Filter 完整的限定类名                                        |
  | `<init-param>`     | 初始化参数，可以设置值`<param-name>`,`<param-value>`         |
  | `<filter-mapping>` | 设置 Filter 所负责拦截的资源                                 |
  | `<filter-name>`    | 子元素对应的 Filter 名称，需要在前面设置过                   |
  | `<url-pattern>`    | 设置 Filter 所拦截的请求路径                                 |
  | `<servlet-name>`   | 设置 Filter 所拦截的Servlet名称                              |
  | `<dispatcher>`     | 设置拦截的资源被调用的方式：REQUEST(默认),INCLUDE,FORWARD,ERROR。 |

拦截路径配置：

- | 类型             | 示例        | 描述                                     |
  | :--------------- | :---------- | :--------------------------------------- |
  | 拦截所有资源     | /*          | 无论访问何种资源，都会别拦截             |
  | 后缀名拦截       | *.jsp       | 当访问特定后缀名资源时(.jsp)被拦截       |
  | 目录拦截         | /test/*     | 当访问 /test 下的资源时会被拦截          |
  | 具体资源路径拦截 | /index.html | 当访问服务器 index.html 资源时才会被拦截 |

拦截方式配置：

- | 拦截方式 | 参数                   | 描述                         |
  | -------- | ---------------------- | ---------------------------- |
  | REQUEST  | DispatcherType.REQUEST | 直接访问目标资源时执行过滤器 |
  | INCLUDE  | DispatcherType.INCLUDE | 转发访问资源时执行过滤器     |
  | FORWARD  | DispatcherType.FORWARD | 包含访问资源时执行过滤器     |
  | ERROR    | DispatcherType.ERROR   | 错误跳转资源时执行过滤器     |

#### 过滤器链配置

注解配置：按照类名的字符串比较规则进行比较，值小的先执行

- AFilter 和 BFilter，AFilter 先执行

web.xml：按照在 `<filter-mapping>` 中定义顺序执行

### Filter 执行流程

![Filter](/images/JAVA/Filter.png)

### Filter 生命周期

#### 初始化

在服务器启动后，会创建 Filter 对象，然后调用 init 方法。该方法只执行一次，用于加载资源。

- `public void init(FilterConfig filterConfig) throws ServletException;`

#### 执行

每次客户请求访问与过滤器关联的目标资源时，Servlet 过滤器将先执行 doFilter 方法，FilterChain 参数用于访问后续过滤器。

- `public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException;`

将 ServletRequest 请求转换成 HttpServletRequest ：

- `HttpServletRequest hrequest = (HttpServletRequest)request;`

#### 销毁

在服务器关闭后，Filter 对象被销毁。如果服务器是正常关闭，则会执行 destroy 方法。只执行一次。用于释放资源。

- `public void destroy();`

