---
title: " JDBC连接 "
date: 2017-12-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - JDBC

---

### JDBC 

JDBC (Java DataBase Connectivity )，是利用 Java 语言或程序连接并且访问数据库的一门技术，是 Java 程序访问数据库的标准接口，通常是面向关系型数据库的。各个数据库厂商自己去实现 JDBC 接口，并提供数据库驱动 jar 包。

使用JDBC的好处

- 各数据库厂商使用相同的接口，Java代码不需要针对不同数据库分别开发
- Java程序编译期仅依赖java.sql包，不依赖具体数据库的jar包
- 可随时替换底层数据库，访问数据库的Java代码基本不变

### JDBC 连接数据库

创建一个以JDBC连接数据库的程序，包含以下步骤。

- 导入MySQL 驱动包，下载地址 [mysql-connector-java.jar ](https://dev.mysql.com/downloads/connector/j/) 。
- 获取：user，password，url，driverClass
  - url：`jdbc:子协议://ip地址:端口号/数据库名` ：MySQL — jdbc:mysql://localhost:3306/mysql
    - 8.0 以上：`?characterEcoding=utf-8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true`
  - driverClass：`com.mysql.jdbc.Driver`，8.0 以上：`com.mysql.cj.jdbc.Driver`
- 注册驱动
  - `Class.forName(String driverClass);`
- 获取连接
  - `Connection conn = DriverManager.getConnection(String url, String user, String password);`
- 获取传输器，并输入准备好的 SQL 语句
  - `PreparedStatement pstmt = connection.prepareStatement(String sql);`
- 通过传输器执行 MySQL
  - `ResultSet resultSet = pstmt.executeQuery();`
- 结果集数据处理
- 释放资源：越晚获取的资源越先关闭，代码放在 finally 块中
  - 依序关闭使用的对象连接：ResultSet -> Statement -> Connection

JDBC需要用到的类和接口有:

- DriverManager：注册 JDBC 驱动
- Connection：创建 JDBC 连接
- PreparedStatement：Statement 的子类接口，比 Statement 更加安全，并且能够提高执行效率。增加SQL预编译生成骨架，保证语义的准确性不会发生改变，防止 SQL 注入攻击。
- ResultSet：结果集对象，用于封装 SQL 语句查询的结果，该对象提供了遍历数据以及获取数据的方法。 

### JDBC 连接数据库实例

#### JDBCUtil

```java
package test.jdbc;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
public class JDBCUtil {
    // 获取连接
    public static Connection getConnection(String dbName,String user, String password) {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver"); //注册驱动
            Connection conn = DriverManager.getConnection(
                    "jdbc:mysql://localhost:3306"+ dbName + "?characterEncoding=utf-8&serverTimezone=GMT%2B8&useServerPrepStms=true&cachePrepStms=true",
                    user,
                    password
            );//获取数据库连接
            return conn;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
    // 释放资源
    public static void releaseSource(Connection conn, Statement stat, ResultSet rs) {
        if( conn != null ) {
            try {
                conn.close();
            } catch (Exception e) {
                e.printStackTrace();
            }finally {
                conn = null;
            }
            if( stat != null ) {
                try {
                    stat.close();
                } catch (Exception e) {
                    e.printStackTrace();
                }finally {
                    stat = null;
                }
            }
            if( rs != null ) {
                try {
                    rs.close();
                } catch (Exception e) {
                    e.printStackTrace();
                }finally {
                    rs = null;
                }
            }
        }
    }
}
```

#### JDBC 增删改查操作

```java
package test.jdbc;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import org.junit.Test;
import test.jdbc.JDBCUtil;
public class testJDBCUtil {
    //插入数据
    @Test
    public void testInsert() {
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        int rows = 0;
        try {
            //1.注册驱动，2.获取连接
            conn = JDBCUtil.getConnection('jdbctest','test','test123');
            //3.获取传输器
            String sql = "insert into account (id,name,age) values(?,?,?)";
            pstmt = (PreparedStatement) conn.prepareStatement(sql);
            //4.通过传输器发送SQL语句到服务器执行并返回结果
            pstmt.setInt(1,NULL);
            pstmt.setString(2,"zhangsan");
            pstmt.setInt(3,23);
            rows = pstmt.executeUpdate();
            //5.处理结果
            System.out.println("影响的行数是：" + rows);
            //6.关闭连接，释放资源
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            JDBCUtil.close(conn,stat,rs);
        }
    }
    //删除数据
    @Test
    public void testDelete() {
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        int rows = 0;
        try {
            //1.注册驱动，2.获取连接
            conn = JDBCUtil.getConnection('jdbctest','test','test123');
            //3.获取传输器
            String sql = "delete from account where name = ?";
            pstmt = (PreparedStatement) conn.prepareStatement(sql);
            //4.通过传输器发送SQL语句到服务器执行并返回结果
            pstmt.setString(1,'zhangsan');
            rows = pstmt.executeUpdate();
            //5.处理结果
            System.out.println("影响的行数是：" + rows);
            //6.关闭连接，释放资源
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            JDBCUtil.close(conn,stat,rs);
        }
    }
    //修改数据
    @Test
    public void testUpdate() {
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        int rows = 0;
        try {
            //1.注册驱动，2.获取连接
            conn = JDBCUtil.getConnection('jdbctest','test','test123');
            //3.获取连接
            String sql = "update account set age = ? where name = ?";
            pstmt = (PreparedStatement) conn.prepareStatement(sql);
            //4.通过传输器发送SQL语句到服务器执行并返回结果
            pstmt.setInt(1,23);
            pstmt.setString(2,'zhangsan');
            rows = pstmt.executeUpdate();
            //5.处理结果
            System.out.println("影响的行数是：" + rows);
            //6.关闭连接，释放资源
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            JDBCUtil.close(conn,stat,rs);
        }
    }
    //查询数据
    @Test
    public void testSelect() {
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            //1.注册驱动，2.获取连接
            conn = JDBCUtil.getConnection('jdbctest','test','test123');
            //3.获取连接
            String sql = "select * from account";
            pstmt = (PreparedStatement) conn.prepareStatement(sql);
            //4.通过传输器发送SQL语句到服务器执行并返回结果
            rs = pstmt.executeQuery();
            //5.处理结果
            while( rs.next() ) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                int age = rs.getInt("age");
                System.out.println(id + ":" + name + ":" + age );
            }
            //6.关闭连接，释放资源
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            JDBCUtil.close(conn,stat,rs);
        }
    }     
}
```

