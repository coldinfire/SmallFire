---
title: " Servlet Cookie&Session对象 "
date: 2017-11-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Servlet

---

### Cookie：客户端会话技术 

Cookie 是一小段文本信息，伴随着用户请求和页面在 Web 服务器和浏览器之间传递。一般用于存储少量的不太敏感的数据信息，以键值对形式存储。可以在用户不登录的情况下，完成服务器对客服端的身份识别。

基于响应头 Set-Cookie 和请求头 cookie 实现。

Cookie 特性

- Cookie有大小限制。浏览器对于单个 cookie 的大小限制为(4KB)，字符串长度超过 4KB，该属性返回空字符串
- Cookie 最终都是以文件形式放在客户端机器中，查看和修改都是很方便的，因此不建议存放重要信息
- 同一个域名下的总 cookie 数量有限制(20)
- Cookie 是存在有效期的。默认情况下，当浏览器关闭后 Cookie 数据被销毁。可以通过 setMaxAge(int second) 设置 Cookie 有效期

#### Cookie 操作

创建 Cookie 对象，绑定数据

- `new Cookie(String name, String value);`
- `cookie.setMaxAge(3600);`：设置 Cookie 存活期

发送 Cookie，会在响应头设置 set-cookie:name=value 的响应头消息。

- `response.addCookie(Cookie cookie);`
- 可以在一次响应中创建多个 Cooike 对象，使用 response 发送多个 cooike。

获取 Cookie 对象获取数据，当浏览器发出请求时，会将 Cookie 信息放在请求头 cookie 中。

- `Cookie[] request.getCookies();`

cookie 中存取中文：

- Cookie cookie = new Cookie("userName", URLEncoder.encode("中文", "UTF-8")) ：先进行转码
- response.addCookie(cookie);
- URLDecoder.decode(cookies[i].getValue(), "UTF-8")：获取数据时，进行解码

#### Cookie 对象方法

| Method                            | Description                                            |
| :-------------------------------- | :----------------------------------------------------- |
| Cookie(String name, String value) | 实例化Cookie对象，设置Cookie的名称和cookie的值         |
| String getName()                  | 取得当前cookie对象的名字                               |
| String getValue()                 | 取得当前cookie对象的值                                 |
| void setValue(String newValue)    | 设置cookie的值                                         |
| void setMaxAge(int expiry)        | 设置cookie的最大保存时间，即cookie的有效期，单位是：秒 |
| int getMaxAge()                   | 获取cookies的有效期                                    |
| void setPath(String uri)          | 设置cookie的有效路径                                   |
| String getPath()                  | 获取cookie的有效路径                                   |
| void setDomain(String pattern)    | 设置cookie的有效域                                     |
| String getDomain()                | 获取cookie的有效域                                     |

setMaxAge(int seconds)：设置 Cookie 对象的存活时间，默认值是 -1

- 正数：设置 Cookie 的存活时间
- 零：删除 Cookie 信息
- 负数：cookies auto-expire，当浏览器关闭时，自动删除

#### Cookie 共享

Cookie 有域(domain)和路径(routing)的概念。

同一个 tomcat 服务器中共享，多个 Web 项目，此时路径不同：路径就是routing，一个网页所创建的 Cookie 只能被与这个网页在同一目录或子目录下的所有网页访问，而不能被其他目录下的网页访问。

- 路径默认设置为当前的虚拟目录，此时 cookie 不能共享
- setPath(String path)：设置 cookie 的获取范围为 “/”，此时可以共享
- setSecure(true)：如果访问的是 https 网页，需要设置安全模式，否则浏览器不会发送该 Cookie

不同的 tomcat 服务器间共享，此时 domain 不同：不同的域之间是不能互相访问 Cookie。

- setDomain(String path)：如果设置一级域名相同，那么多个服务器之间 cookie 可以共享

#### Cookie 被禁用

如果客户端的浏览器禁用了 Cookie 怎么办：

-  使用一种叫做 URL 重写的技术来进行会话跟踪，即每次 HTTP 交互，URL 后面都会被附加上一个诸如 sid=xxxxx 这样的参数，服务端据此来识别用户；通过 response.encodeURL(url) 进行实现
- 使用 Token 机制，原理和 Cookie 类似；只不过令牌是存在请求头中验证

### Session：服务器端会话技术

由于 HTTP 协议是无状态的协议，所以服务端需要记录用户的状态时，就需要用某种机制来识具体的用户，这个机制就是 Session。Session 是在服务端保存的一个数据结构，可以存储任意类型，任意大小的数据，用来跟踪用户的状态。

Session 是基于唯一 ID 识别用户身份的。每个用户第一次访问服务器后，会自动获得一个 sessionID 并且把这个 ID 保存到客户端的 Cookie 中，保存形式是以`JSESSIONID` 来保存的。下次访问时 Cookie 会带着该 JSESSIONID 访问服务器。如果用户在一段时间内没有访问服务器(默认失效时间 30 分钟)，那么 Session 会自动失效，下次即使带着上次分配的 sessionID 访问，服务器也认为这是一个新用户，会分配新的 sessionID。

![JSESSIONID](/images/JAVA/JAVA_Session.png)

在服务端保存 Session 的方法很多，内存、数据库、文件都有。使用 Session 时，由于服务器把所有用户的 Session 都存储在内存中，如果遇到内存不足的情况，就需要把部分不活动的 Session 序列化到磁盘上，这会大大降低服务器的运行效率，因此，放入 Session 的对象要小。

#### Session 操作

- Session在用户第一次访问服务器的时候自动创建，只有访问 JSP、Servlet 等程序时才会创建Session

- `HttpSession request.getSession();`：强制生成 Session，同时服务器会为该 Session 生成唯一的 sessionID，这个 sessionID 在随后的请求中会被用来重新获得已经创建的 Session

- `setMaxInactiveInterval(long interval);`：设置 session 的有效时间

- 选择性配置修改 Session 有效时间：在tomcat -> conf -> web.xml 修改配置

  - ```xml
    <session-config>
        <session-timeout>30</session-timeout>
    </session-config>
    ```

#### Session 对象方法

HttpSession 方法列表：

| Method                                         | Description                         |
| :--------------------------------------------- | :---------------------------------- |
| boolean isNew()                                | 判断该 Session 对象是否是新的       |
| void invalidate()                              | 设置整个 Session 无效               |
| String getId()                                 | 返回 SessionID                      |
| long getCreationTime()                         | 获取 Session 创建时间               |
| long getLastAccessedTime()                     | 获取最后访问时间                    |
| void setMaxInactiveInterval(int interval)      | 设置 session 的有效时间，单位：分钟 |
| int getMaxInactiveInterval()                   | 获取 session 的有效时间             |
| `Object getAttribute(String name)`             | 根据属性名获取 Session 属性值       |
| `void setAttribute(String name, Object value)` | 设置 Session 中键值对形式的属性     |
| `void removeAttribute(String name)`            | 根据属性名删除键值对属性            |

#### Session 细节

客户端关闭后，服务器不关闭，两次获取的 session 是否为同一个

- 默认情况下：不是。因为客户端关闭后，cookie 的有效期就截止了

- 如果需要保持相同，可以创建 Cookie，键为 JSESSIONID，设置最大存活时间，将 Cookie 持久化保持。

  ```java
  Cookie cookie = new Cookie("JSESSIONID",session.getId());
  cookie.setMaxAge(60*60);
  response.addCookie(cookie);
  ```

客户端不关闭，服务器关闭后，两次获取的 session 是否为同一个

- 不是同一个，但是要确保数据不丢失。tomcat 自动完成以下工作
- session 的钝化
  - 在服务器正常关闭之前，将 session 对象序列化到硬盘上
- session 的活化
  - 在服务器启动后，将 session 文件转化为内存中的 session 对象

### Cookie 和 session 结合使用

SessionID 是连接 Cookie 和 Session 的一道桥梁。

持久化 sessionid：将 sessionid 保存到 cookie 中，访问时浏览器会把对应的 cookie 发给服务器，服务器获取 cookie 中的 sessionid，然后从内存中获取对应的 session 做登录判断即可。

#### Cookie 和 Session 的区别

- session 存储数据在服务器端，Cookie 在客户端
- session 没有数据大小限制，Cookie 有限制
- session 数据是安全的，Cookie 相对于 session 不安全