---
title: "JSON和JAVA对象的转换"
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

[下载 jackson jar 包](https://repo1.maven.org/maven2/com/fasterxml/jackson/core/)

### JAVA 对象和 JSON 互相转换

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

        // json 轉 Map<String, User>
        Map<String, User> urMap = objectMapper.readValue(mapjson, new TypeReference<HashMap<String, User>>() {});
    }
}
```

