---
title: " Servlet ServletContext 对象 "
date: 2017-11-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Servlet

---

### ServletContext 对象

ServletContext 代表整个WEB应用，可以和程序的容器(服务器)通讯

#### 获取 ServletContext 对象

通过 request 对象获取

- `request.getServletContext()`

通过 HttpServlet 获取

- this.getServletContext();

#### 功能

获取 MIME 类型

- MIME 类型：在互联网通信过程中定义的一种文件数据类型
  - 格式：大类型/小类型 (text/html)
- 获取：String getMimeType(String file);

域对象：共享数据

- 范围：所有用户所有请求的数据

获取文件的服务器路径

- `String getRealPath(String fileName)` ：根据文件名返回该文件在项目中的服务器路径
- src 目录下的文件会被放到 `/WEB-INF/classes/file_name `路径下

#### 下载文件

设置超链接功能：

使用响应头设置资源的打开方式：content-disposition:attachment;filename=XXX