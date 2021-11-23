---
title: " Mybatis 使用 "
date: 2018-01-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Mybatis

---

Mybatis 官方文档：[https://mybatis.org/mybatis-3/zh/configuration.html](https://mybatis.org/mybatis-3/zh/configuration.html)

### MyBatis 简介

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

采用 ORM (Object Relational Mapping) 思想解决了实体和数据库映射的问题，对 JDBC 进行了封装，屏蔽了 JDBC API 底层的访问细节。

ORM 把对 SQL 的操作转换为对象的操作，这种转换时通过对象和表之间的元数据映射实现的。由于类和表之间以及属性和字段之间建立起了映射关系，所以，通过 sql 对表的操作就可以转换为对象的操作，程序员从此无需编写 sql 语句，由框架根据映射关系自动生成，这就是 ORM 思想。

#### 使用 Maven 构建

将 mybatis 依赖代码，SQL 驱动依赖代码等置于 pom.xml 文件中。

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>3.4.0</version>
</dependency>
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>5.1.36</version>
</dependency>
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
</dependency>
```

### MyBatis 的执行流程

Mybatis 配置文件，包括 Mybatis 全局配置文件和 Mybatis 映射文件，其中全局配置文件配置了数据源、事务等信息；映射文件配置了SQL执行相关的 
信息。

Mybatis 通过读取配置文件信息（全局配置文件和映射文件），构造出 SqlSessionFactory，即会话工厂。

通过 SqlSessionFactory，可以创建 SqlSession 即会话。Mybatis 是通过 SqlSession 来操作数据库的。

SqlSession 本身不能直接操作数据库，它是通过底层的Executor 执行器接口来操作数据库的。Executor 接口有两个实现类，一个是普通执行器，一个是缓存执行器（默认）。

Executor 执行器要处理的 SQL 信息是封装到一个底层对象 MappedStatement 中。该对象包括：SQL 语句、输入参数映射信息、输出结果集映射信息。其中输入参数和输出结果的映射类型包括java的简单类型、HashMap 集合对象、POJO 对象类型。

### MyBatis 配置

MyBatis 的配置文件(mybatisConfig.xml)包含了会深深影响 MyBatis 行为的设置和属性信息。

#### 数据库连接属性：db.properties

```properties
#jdbc.driverClassName=com.mysql.jdbc.Driver
#jdbc.url=jdbc:mysql://localhost:3306/web_learn?characterEncoding=utf-8
jdbc.driverClassName=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/web_learn?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
jdbc.username=xxx
jdbc.password=xxx
```

#### 主配置文件：SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <!-- 加载数据库连接属性配置文件 -->
  <properties resource="database.properties" />
  <!-- 配置环境 -->
  <environments default="development">
    <!-- 配置MySQL环境 -->
    <environment id="development">
      <!-- 配置事务类型 -->
      <transactionManager type="JDBC" />
      <!-- 配置数据源 -->
      <dataSource type="POOLED">
        <property name="driver" value="${jdbc.driverClassName}" />
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.user}" />
        <property name="password" value="${jdbc.password}" />
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="User.xml" />
  </mappers>
</configuration>
```

### MyBatis 映射器

MyBatis 的真正强大在于它的语句映射(xxxMapper.xml)，这是它的魔力所在。由于它的异常强大，映射器的 XML 文件就显得相对简单。



#### Mapper 代理开发模式

Mapper 代理的开发方式，只需要编写 mapper 接口（相当于 dao 接口）即可，而不同再写 dao 实现类啦。Mybatis会自动的为 mapper 接口生成动态代理实现类。不过要实现 mapper 代理的开发方式，需要遵循一些开发规范。

接口的包名，类名，参数，返回值分别对应着映射文件的 namespace、id、parameterType、resultType：

- mapper 接口的全限定名要和 mapper 映射文件的 namespace 的值相同。
- mapper 接口的方法名称要和 mapper 映射文件中的 statement 的 id 相同。
- mapper 接口的方法参数只能有一个，且类型要和 mapper 映射文件中 statement 的 parameterType 的值保持一致。
- mapper 接口的返回值类型要和 mapper 映射文件中 statement 的 resultType 值或 resultMap 中的 type 值保持一致。

Mapper 接口：

```java
package com.smallfire.dao;
import java.util.List;
import com.smallfire.domain.User;
public interface UserMapper {
    public User findUserById(int id);
    public List<User> findUserAll();
    public void insertUser(User user);
    public void deleteUserById(int id);
    public void updateUserPassword(User user);
}
```

Mapper 配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- mapper标签要指定namespace属性，不然会报错，且mapper开发时设置为Mapper接口的全限定名-->
<mapper namespace="com.smallfire.dao.UserMapper">
  <!--
    id:给标签体内的sql操作命名,方便调用。
    parameterType:传入参数的类型.传入java类型,转化为sql类型,添加到sql语句上.
    resultType:返回结果类型.sql结果集转化为java对象并返回.
  -->
  <select id="findUserById" parameterType="int" resultType="com.smallfire.domain.User">
    select * from user4 where id = #{id}
  </select>
  <select id="findUserAll" resultType="com.smallfire.domain.User">
    select * from user4 
  </select>
  <insert id="insertUser" parameterType="com.smallfire.domain.User">
    insert into user4(username,password,age) values(#{username},#{password},#{age})
  </insert>
  <delete id="deleteUserById" parameterType="int">
    delete from user4 where id=#{id}
  </delete>
  <update id="updateUserPassword" parameterType="com.smallfire.domain.User">
    update user4 set password=#{password} where id=#{id}
  </update>
</mapper>
```

### MyBatis 日志

Mybatis 通过使用内置的日志工厂提供日志功能。内置日志工厂将会把日志工作委托给下面的实现之一：

- SLF4J
- Apache Commons Logging
- Log4j 2
- Log4j
- JDK logging

MyBatis 内置日志工厂会基于运行时检测信息选择日志委托实现。它会（按上面罗列的顺序）使用第一个查找到的实现。当没有找到这些实现时，将会禁用日志功能。

#### log4j.properties

```properties
# 全局日志配置
log4j.rootLogger=ERROR, debug, CONSOLE, LOGFILE
# MyBatis 日志配置
log4j.logger.org.mybatis.example.BlogMapper=TRACE
log4j.logger.org.apache.axis.enterprise=FATAL, CONSOLE
# 控制台输出
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %c - %m\n
# LOGFILE 设置文件输出
log4j.appender.LOGFILE=org.apache.log4j.FileAppender
log4j.appender.LOGFILE.File=d:\axis.log
log4j.appender.LOGFILE.Append=true
log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.LOGFILE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %c - %m\n
```

#### 日值配置

```xml
<configuration>
  <settings>
    ...
    <setting name="logImpl" value="LOG4J"/>
    ...
  </settings>
</configuration>
```