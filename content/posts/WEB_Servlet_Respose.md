---
title: "Servlet Response对象"
date: 2017-11-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Servlet

---

### Servlet Response 对象

ServletResponse 接口表示一个 Servlet 响应，在调用 Servlet 的Service( )方法前，Servlet 容器会先创建一个 ServletResponse 对象，并把它作为返回参数传给 Service( )方法。

ServletResponse 对象封装了向浏览器发送响应的复杂过程。



#### HttpServletResponse 接口常用方法

由于 HTTP 请求消息分为状态行，响应消息头，响应消息体三部分；因此，在 HttpServletResponse 接口中定义了向客户端发送响应状态码，响应消息头，响应消息体的方法。

```java
//获取缓冲信息
  // 强制刷新缓冲区的内容到客户端，之后如果调用forward方法将报异常。
public void flushBuffer() throws IOException;
public void resetBuffer();// 只清除缓冲。
public void reset();// 清除缓冲、状态码、头信息，设置页面不缓存。
public int getBufferSize();// 返回响应使用的缓冲区使用的实际大小。
public void setBufferSize(int size);// 设置缓冲区的大小。
//设置响应信息
public void setContentLength(int len);// 设置响应体的长度。
public void setContentType(String type);// 设置响应的编码格式，并通知浏览器使用该方式解码。
public String getCharacterEncoding();// 设置响应体中MIME的编码方式。
public ServletOutputStream getOutputStream() throws IOException;// 返回使用二进制流包装的响应。
public PrintWriter getWriter() throws IOException;// 返回使用字符流包装的文本给客户端。
public boolean isCommitted();// 判断响应是否已被提交或完成。
//获取本地信息
public Locale getLocale();// 获取响应的本地信息。
public void setLocale(Locale loc);// 设置本地响应信息，包括要使用什么解码。
//设置响应头信息（HttpServletRequest独有的）
public void setIntHeader(String name, int value);// 设置整形单值的响应头的值。
public void addIntHeader(String name, int value);// 设置整形多值的响应头的值。
public void setDateHeader(String name, long date);// 设置毫秒类型单值的响应头的值。
public void addDateHeader(String name, long date);// 设置毫秒类型多值的响应头的值。
public void setHeader(String name, String value);// 设置字符串类型单值的响应头的值。
public void addHeader(String name, String value);// 设置字符串类型多值的响应头的值。
public boolean containsHeader(String name);// 返回一个布尔值，判断响应的头部是否被设置。
//得到响应头信息（HttpServletRequest独有的）
public String getHeader(String name);// 获取字符串类型多值的响应头的值。
public Collection<String> getHeaders(String name);//返回集合得到多值的响应头的值。
public Collection<String> getHeaderNames();//返回集合得到所有响应头的名称。
//设置状态信息（HttpServletResponse独有的）
public void sendError(int sc) throws IOException;// 设置错误状态返回的状态码。
public void sendError(int sc, String msg) throws IOException;// 设置错误状态返回的状态码及描述信息。
public void setStatus(int sc);// 设置状态码。
public int getStatus();// 获取状态码。
//其他设置（HttpServletResponse独有的）
public void addCookie(Cookie cookie);// 添加Cookie对象。
public String encodeRedirectURL(String url);// 通过指定的编码对URL进项编码，并携带sessionid，跨应用。
public String encodeURL(String url);// 通过指定的编码对URL进项编码，并携带sessionid，本应用。
public void sendRedirect(String location) throws IOException;// 进行重定向。
```

