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



#### HttpServletRequest 接口常用方法

```java
//获取协议信息
public String getProtocol();// 获取协议信息的版本号，如HTTP/1.1。
public String getScheme();// 获取协议名称，如HTTP、FTP。
//获取转发器
public RequestDispatcher getRequestDispatcher(String path);// 获取指定地址的转发器。
//操作属性
public void setAttribute(String name, Object object);// 设置属性名和属性对象。
public Object getAttribute(String name);// 通过属性名获取属性对象。
public Enumeration<String> getAttributeNames();// 得到全部的属性名。
public void removeAttribute(String name);// 通过属性名删除属性对象。
//获取参数
public String getParameter(String name);// 通过参数名获取参数值。
public Map<String, String[]> getParameterMap();// 将所有参数名和参数值以Map的形式返回。
public Enumeration<String> getParameterNames();// 获取全部参数名。
public String[] getParameterValues(String name);// 通过参数名获取数组类型的参数值。
//操作请求体
public String getCharacterEncoding();// 获取请求体中Servlet的编码方式。
  // 设置请求体中Servlet的编码方式。
public void setCharacterEncoding(String env) throws java.io.UnsupportedEncodingException;
public int getContentLength();// 获取请求体的长度。
public String getContentType();// 获取请求体的MIME类型，包括编码。
public ServletInputStream getInputStream() throws IOException;// 将请求体封装为二进制的流文件。
public BufferedReader getReader() throws IOException;// 将请求体封装为字符类型的流文件。
public boolean isSecure();// 是否使用的安全协议，如HTTPS。
//获取服务器信息
public int getServerPort();// 获取服务器的端口号。
public String getServerName();// 获取服务器的主机名。
public Locale getLocale();// 获取本地位置信息，若ZHCN等等。
public Enumeration<Locale> getLocales();// 使用枚举获取服务器的多个位置信息。
public String getLocalAddr();// 获取本地地址，根据访问方法不同而不同，为127.0.0.1或者公网ip。
public String getLocalName();// 获取本地IP的名称，如127.0.0.1就是localhost。
public int getLocalPort();// 获取端口号，本应用就是8081。
//获取客户端信息
public String getRemoteAddr();// 获取客户端的IP地址。
public String getRemoteHost();// 获取客户端的主机名。
public int getRemotePort();// 获取客户端的端口号。
//得到请求头信息（HttpServletRequest独有的）
public int getIntHeader(String name);// 得到整形单值的请求头的值。
public long getDateHeader(String name);// 得到毫秒类型单值的请求头。
public String getHeader(String name);// 得到字符串类型单值的请求头的值。
public Enumeration<String> getHeaders(String name);// 返回枚举得到多值的请求头的值。
public Enumeration<String> getHeaderNames();// 返回枚举得到所有请求头的名称。
//得到常用信息（HttpServletRequest独有的）
public String getMethod();// 得到请求方式。
public String getContextPath();// 获取项目的根目录名。返回指定上下文(web应用)的URL的前缀，比如/BookStore。
public String getServletPath();// URL中调用Servlet的那一部分，不包含附加路径信息(/BookServlet)
public String getQueryString();// 获取URL后面全部的查询字符串参数。
public String getRequestURI();// 获取请求URI，不包含请求内容，不包含域名。
public StringBuffer getRequestURL();// 获取URL，包含域名，不包含请求内容。未被Servlet服务器Decode过。
public String getRequestedSessionId();// 返回这个请求相应的SessionId。
public HttpSession getSession();// 获取Session对话，用于与网页通信。
public Cookie[] getCookies();// 返回使用数组存储的Cookie对象。
```

