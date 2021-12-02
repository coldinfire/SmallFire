---
title: " JSON&JAVA 对象的转换 "
date: 2017-10-30
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - JSON

---

### JSON 解析器

常见的解析器：Gson、jackson、fastjson、Jsonlib

jackson 优点

- 解析大文件的速度比较快；
- 运行时占用的内存比较少，性能更佳；
- API 很灵活，容易进行扩展和定制

jackson 提供以下 jar 包：

- [jackson-core.jar 核心包，提供基于"流模式"解析的API](https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-core/)
- [jackson-databind.jar 数据绑定包，提供基于对象绑定和树模型的相关API](https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-databind/)
- [jackson-annotations.jar 注解包，提供注解功能](https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-annotations/)

### JAVA 对象和 JSON 互相转换

Jackson 核心类：`ObjectMapper`，该类定义了 java 对象和 JSON 互换的方法。

ObjectMapper 有多个 JSON 序列化的方法，可以把对象转换为 JSON 字符串并保存 File、OutputStream 等不同的介质中。

- writeValue(File file, Object obj)：把对象转成 json 序列，并保存到指定文件中。
- writeValue(OutputStream ops, Object obj)：把对象转成 json 序列，并保存到指定输出流中。
- writeValue(Writer writer, Object obj)：把对象转成 json 序列，并保存到写出字节流。
- writeValueAsBytes(Object obj)：把对象转成 json 序列，并把结果输出成字节数组。
- writeValueAsString(Object obj)：把对象转成 json 序列，并把结果输出成字符串。

ObjectMapper 同时也有多个 JSON 反序列化的方法，可以将 JSON 字符串转换成 JAVA 对象。

- readValue(File,Class)：将指定文件内容转换成指定的对象类型。
- readValue(String,TypeReference)：将字符串转换成指定的对象类型。
- readValue(InputStream,Class)：将字节流转换成指定的对象类型。

```java
import java.util.*;
import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.ObjectMapper;

public class JacksonTest {
    public static void main(String[] args) throws Exception {
        ObjectMapper objectMapper = new ObjectMapper();
        // User Object 转 json
        User user1 = new User(23, "John");
        String json = objectMapper.writeValueAsString(user1);
        // {"name":"John","age":23}
        // json 转 User Object
        User user2 = objectMapper.readValue(json, User.class);
        
        // List<User> 转 json
        List<User> userList = new ArrayList<User>();
        User user3 = new User(23, "John");
        userList.add(user3);
        String userjson = objectMapper.writeValueAsString(userList);

        // json 转 List<User>
        List<User> urlist = objectMapper.readValue(userJson, new TypeReference<List<User>>() {});
        
        // Map<String, User> 转 json
        HashMap<String, User> map = new HashMap<>();
        User user4 = new User(23, "John");
        map.put("John", user4);
        String mapjson = objectMapper.writeValueAsString(map);

        // json 转 Map<String, User>
        Map<String, User> urMap = objectMapper.readValue(mapjson, new TypeReference<HashMap<String, User>>() {});
    }
}
```

### Jackson 注解

Jackson 提供了一系列注解，方便对 JSON 序列化和反序列化进行控制。

- `@JsonIgnore` 此注解用于属性上，作用是进行 JSON 操作时忽略该属性。
- `@JsonIgnoreProperties(value={"val1","val2"})` 用于过滤多个字段
- `@JsonFormat(pattern)` 此注解用于属性上，作用是把 Date 类型数据转化为想要的格式
- `@JsonProperty("name")` 此注解用于属性上，作用是把该属性的名称序列化为另外一个名称