---
title: "Servlet Request对象"
date: 2017-11-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Servlet

---

### Servlet Request 对象

Servlet 容器对于接受到的每一个 Http 请求，都会创建一个ServletRequest 对象，并把这个对象传递给 Servlet 的 Sevice( )方法。其中，ServletRequest 对象内封装了关于这个请求的许多详细信息。

#### HTTP 请求信息

![](/images/Tomcat/HTTPRequest.png)

#### HttpServletRequest 接口常用方法

获取常用信息

| Method                                       | Desc                                                         |
| :------------------------------------------- | :----------------------------------------------------------- |
| public String getMethod();                   | 得到HTTP请求方式                                             |
| `public String getContextPath();`            | 获取项目的根目录名。返回指定上下文(web应用)的URL的前缀       |
| public String getServletPath();              | URL中调用Servlet的那一部分，不包含附加路径信息               |
| public String getQueryString();              | 获取URL后面全部的查询字符串参数                              |
| `public String getRequestURI();`             | 获取请求URI，不包含请求内容，不包含域名                      |
| public StringBuffer getRequestURL();         | 获取URL，包含域名，不包含请求内容。未被Servlet服务器Decode过 |
| public String getProtocol();                 | 获取协议信息的版本号，如HTTP/1.1                             |
| public String getScheme();                   | 获取协议名称，如HTTP、FTP、HTTPS                             |
| public String getRequestedSessionId();       | 返回这个请求相应的Session Id                                 |
| public HttpSession getSession();             | 获取Session对话，用于与网页通信                              |
| public Cookie[] getCookies();                | 返回使用数组存储的Cookie对象                                 |
| `public ServletContext getServletContext();` | 获取 ServletContext 对象                                     |

获取请求头信息

| Method                                                | Desc                           |
| :---------------------------------------------------- | :----------------------------- |
| `public String getHeader(String name);`               | 得到字符串类型单值的请求头的值 |
| public int getIntHeader(String name);                 | 得到整形单值的请求头的值       |
| public long getDateHeader(String name);               | 得到毫秒类型单值的请求头       |
| `public Enumeration<String> getHeaders(String name);` | 返回枚举得到多值的请求头的值   |
| `public Enumeration<String> getHeaderNames();`        | 返回枚举得到所有请求头的名称   |

获取请求体信息（Post 方法）

| Method                                                       | Desc                             |
| :----------------------------------------------------------- | :------------------------------- |
| public String getCharacterEncoding();                        | 获取请求体中Servlet的编码方式    |
| `public void setCharacterEncoding(String env);`              | 设置请求体中Servlet的编码方式    |
| public int getContentLength();                               | 获取请求体的长度                 |
| public String getContentType();                              | 获取请求体的MIME类型，包括编码   |
| `public ServletInputStream getInputStream() throws IOException;` | 将请求体封装为二进制的字节流文件 |
| `public BufferedReader getReader() throws IOException;`      | 将请求体封装为字符类型的流文件   |
| public boolean isSecure();                                   | 是否使用的安全协议，如HTTPS      |

获取请求参数：get 和 post 都可以使用下列方法获取请求参数

| Method                                            | Desc                                |
| :------------------------------------------------ | :---------------------------------- |
| `public String getParameter(String name);`        | 通过参数名获取参数值                |
| `public Map<String, String[]> getParameterMap();` | 将所有参数名和参数值以Map的形式返回 |
| public Enumeration`<String>` getParameterNames(); | 获取全部参数名                      |
| public String[] getParameterValues(String name);  | 通过参数名获取数组类型的参数值。    |

获取服务器和客户端信息

| Method                                     | Desc                                                        |
| :----------------------------------------- | :---------------------------------------------------------- |
| public int getServerPort();                | 获取服务器的端口号                                          |
| public String getServerName();             | 获取服务器的主机名                                          |
| public Locale getLocale();                 | 获取本地位置信息，若ZHCN等等                                |
| public Enumeration`<Locale> `getLocales(); | 使用枚举获取服务器的多个位置信息                            |
| public String getLocalAddr();              | 获取本地地址，根据访问方法不同而不同，为127.0.0.1或者公网ip |
| public String getLocalName();              | 获取本地IP的名称，如127.0.0.1就是localhost                  |
| public int getLocalPort();                 | 获取端口号，如：8090                                        |
| public String getRemoteAddr();             | 获取客户端的IP地址                                          |
| public String getRemoteHost();             | 获取客户端的主机名                                          |
| public int getRemotePort();                | 获取客户端的端口号                                          |

#### Request 乱码问题解决

在 service 中使用的编码解码方式默认为：ISO-8859-1编码，但此编码并不支持中文，因此会出现乱码问题，所以我们需要手动修改编码方式为 UTF-8 编码，才能解决中文乱码问题。

解决get提交的方式的乱码：

- parameter = newString(parameter.getbytes("iso8859-1"),"utf-8");

解决post提交方式的乱码：

- request.setCharacterEncoding("UTF-8");

返回数据的乱码

- response.setContentType("text/html;charset=utf-8");

#### 请求转发

一种在服务器内部的资源跳转方式。

实现是通过 request 对象获取请求转发器对象，使用 RequestDispatcher 对象进行转发

- `public RequestDispatcher getRequestDispatcher(String path);`

- `forward(ServletRequest request, ServletResponse response);`

特点：

- 浏览器地址栏路径不会发送变化
- 只能转发到当前服务器内部资源中
- 转发只发生了一次 HTTP 请求

#### 共享数据

域对象：一个有作用范围的对象，可以在范围内共享数据

request 域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据

操作属性值方法：

| Method                                                | Desc                   |
| :---------------------------------------------------- | :--------------------- |
| public void setAttribute(String name, Object object); | 设置属性名和属性对象   |
| public Object getAttribute(String name);              | 通过属性名获取属性对象 |
| public Enumeration`<String>` getAttributeNames();     | 得到全部的属性名       |
| public void removeAttribute(String name);             | 通过属性名删除属性对象 |