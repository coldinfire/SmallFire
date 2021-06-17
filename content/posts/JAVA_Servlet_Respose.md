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

![Response](/images/Tomcat/HTTPResponse.png)

由于 HTTP 请求消息分为状态行，响应消息头，响应消息体三部分；因此，在 HttpServletResponse 接口中定义了向客户端发送响应状态码，响应消息头，响应消息体的方法。

| 响应头              | 描述                                                         |
| :------------------ | :----------------------------------------------------------- |
| Allow               | 指定服务器支持的request方法（GET，POST等等）                 |
| Cache-Control       | 指定响应文档能够被安全缓存的情况。通常取值为 public,private或no-cache等。 Public意味着文档可缓存，Private意味着文档只为单用户服务并且只能使用私有缓存。No-cache意味着文档不被缓存。 |
| Connection          | 命令浏览器是否要使用持久的HTTP连接。close : 命令浏览器不使用持久HTTP连接，keep-alive : 使用持久化连接。 |
| Content-Disposition | 让浏览器要求用户将响应以给定的名称存储在磁盘中               |
| Content-Encoding    | 指定传输时页面的编码规则                                     |
| Content-Language    | 表述文档所使用的语言，比如en， en-us,，ru等等                |
| Content-Length      | 表明响应的字节数。只有在浏览器使用持久化 (keep-alive) HTTP 连接时才有用 |
| Content-Type        | 表明文档使用的MIME类型                                       |
| Expires             | 指明什么时候过期并从缓存中移除                               |
| Last-Modified       | 指明文档最后修改时间。客户端可以 缓存文档并且在后续的请求中提供一个 **If-Modified-Since**请求头 |
| Location            | 在300秒内，包含所有的有一个状态码的响应地址，浏览器会自动重连然后检索新文档 |
| Refresh             | 指明浏览器每隔多久请求更新一次页面。                         |
| Retry-After         | 与503 (Service Unavailable)一起使用来告诉用户多久后请求将会得到响应 |
| Set-Cookie          | 指明当前页面对应的cookie                                     |

设置响应行

| Method                                                       | Desc                               |
| :----------------------------------------------------------- | :--------------------------------- |
| public int getStatus();                                      | 获取状态码                         |
| public void setStatus(int statuscode);                       | 设置状态码                         |
| public void sendError(int statuscode) throws IOException;    | 设置错误状态返回的状态码           |
| public void sendError(int statuscode, String msg) throws IOException; | 设置错误状态返回的状态码及描述信息 |

设置响应头

| Method                                             | Desc                                     |
| :------------------------------------------------- | :--------------------------------------- |
| public void setIntHeader(String name, int value);  | 设置整形单值的响应头的值                 |
| public void addIntHeader(String name, int value);  | 设置整形多值的响应头的值                 |
| public void setDateHeader(String name, long date); | 设置毫秒类型单值的响应头的值             |
| public void addDateHeader(String name, long date); | 设置毫秒类型多值的响应头的值             |
| public void setHeader(String name, String value);  | 设置字符串类型单值的响应头的值           |
| public void addHeader(String name, String value);  | 设置字符串类型多值的响应头的值           |
| public boolean containsHeader(String name);        | 返回一个布尔值，判断响应的头部是否被设置 |

设置响应体

| Method                                         | Desc                                           |
| :--------------------------------------------- | :--------------------------------------------- |
| public void setContentLength(int len);         | 设置响应体的长度                               |
| public void setContentType(String type);       | 设置响应的编码格式，并通知浏览器使用该方式解码 |
| public String setCharacterEncoding();          | 设置响应体中MIME的编码方式                     |
| public ServletOutputStream getOutputStream() ; | 返回使用二进制流包装的响应                     |
| public PrintWriter getWriter();                | 返回使用字符流包装的文本给客户端               |
| public boolean isCommitted();                  | 判断响应是否已被提交或完成                     |

其他设置

| Method                                       | Desc                                                         |
| -------------------------------------------- | :----------------------------------------------------------- |
| public void flushBuffer();                   | 强制刷新缓冲区的内容到客户端，之后如果调用forward方法将报异常 |
| public void resetBuffer();                   | 只清除缓冲                                                   |
| public void reset();                         | 清除缓冲、状态码、头信息，设置页面不缓存                     |
| public int getBufferSize();                  | 返回响应使用的缓冲区使用的实际大小。                         |
| public void setBufferSize(int size);         | 设置缓冲区的大小                                             |
| `public void addCookie(Cookie cookie);`      | 添加Cookie对象                                               |
| public String encodeURL(String url);         | 通过指定的编码对URL进项编码，并携带sessionid，本应用         |
| public String encodeRedirectURL(String url); | 通过指定的编码对URL进项编码，并携带sessionid，跨应用         |

#### 重定向

重定向方法：`public void sendRedirect(String location);`

重定向特点：

- 地址栏发生变化
- 重定向可以访问其他站点(服务器)的资源
- 重定向会发生两次 HTTP 请求

路径写法：

- 相对路径：通过相对路径不可以确定唯一资源
  - 不以 `/` 开头，以`.`开头路径
  - 规则：找到当前资源和目标资源之间的相对位置关系
  - `./`：当前目录，可以省略不写
  - `../`：后退一级目录，上级目录

- 绝对路径：通融过绝对路径可以确定唯一资源
  - 以`/` 开头的路径
  - 规则：判断定义的路径给谁使用，判断请求将来从哪里发出
    - 给客户端浏览器使用：需要加虚拟目录(项目的访问路径)
      - 建议虚拟目录动态获取：request.getContextPath()
    - 给服务器使用：不需要加虚拟目录
      - 转发路径

#### 输出数据

字符流

- `PrintWriter pw = response.getWrite();`
- 乱码问题
  - 获取默认编码是 ISO-8859-1；设置流的默认编码；告知浏览器响应体使用的编码
  - 获取流之前设置编码：`response.setContentType("text/html;charset=utf-8");`