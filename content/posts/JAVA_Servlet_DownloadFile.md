---
title: "Servlet 下载文件"
date: 2017-11-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Servlet
---



### 文件下载

#### HTML File

```html
<body>
	<a href="/Test/DownloadFile?filename=pvp.png">download picture</a><br/>
	<a href="/Test/DownloadFile?filename=1.avi">download video</a>
</body>
```

#### 中文文件名

```java
public class DownloadUtils{
    public static String getFileName(String agent,String filename) throws UnsupportedEncodingException{
        if(agent.contains("MSIE")){
            // IE 浏览器
            filename = URLEncoder.encode(filename,"utf-8");
            filename = filename.replace("+"," ");
        } else if (agent.contains("Firefox")){
            BASE64Encoder base64Encoder = new BASE64Encoder();
            filename = "=?utf-8?B?" + base64Encoder.encode(filename.getBytes("utf-8")) } "?=";
        } else{
            filename = URLEncoder.encode(filename, "utf-8");
        }
        return filename;
    }
}
```

#### Servlet File

```java
@WebServlet("/DownloadFile")
public class DownloadFile extends HttpServlet {
	private static final long serialVersionUID = 1L;
	public DownloadFile() {
		super();
	}
	protected void doGet(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
		// 设置统一编码
		request.setCharacterEncoding("utf-8");
		response.setContentType("text/html;charset=utf-8");
		// 获取文件名称
		String file = request.getParameter("filename");
		// 找到文件服务器路径
		ServletContext servletContext = request.getServletContext();
		String realPath = servletContext.getRealPath(file);
		// 字节流加载文件
		InputStream fileInputStream = new FileInputStream(realPath);
		// 设置响应类型 content-type
		String mimeType = servletContext.getMimeType(file);
		response.setHeader("content-type", mimeType);
		// 使用响应头设置请求方式
        // 中文问题
        Stirng agent = request.getHeader("user-agent");
		String fileName = DownloadUtils.getFileName(agent,file);
        response.setHeader("content-disposition", "attachment;filename=" + fileName);
		// 输入流数据写到输出流
		ServletOutputStream outputStream = response.getOutputStream();
		int len;
		byte[] buff = new byte[1024];
		while ((len = fileInputStream.read(buff)) != -1) {
			// response 输出流
			outputStream.write(buff, 0, len);
		}
		fileInputStream.close();
		outputStream.close();
	}
	protected void doPost(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
		doGet(request, response);
	}
}

```

