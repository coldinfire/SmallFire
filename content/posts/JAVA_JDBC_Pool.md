---
title: " 数据库连接池 "
date: 2017-12-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - MySQL
---

### 数据库连接池的应用

每次访问数据库都会创建一个连接，初始化连接，关闭连接，会执行访问数据库的所有操作，由于每次创建初始化连接和关闭连接会花费大量的时间，会导致整个程序效率低下，因此为解决在访问数据库时要不断的初始化连接和关闭连接带来的时间消耗问题，我们采取了连接池的方式。

#### 连接池优点

资源重用

- 由于数据库连接得以重用，避免了频繁创建，释放连接引起的大量性能开销。在减少系统消耗的基础上，另一方面也增加了系统运行环境的平稳性。

提升系统执行效率

- 数据库连接池在初始化过程中，已经创建了一定的数据库连接置于连接池中，此时连接的初始化工作均已完成。对于业务请求处理而言，直接利用现有可用连接，避免了数据库连接初始化和释放过程的时间开销，从而减少了系统的响应时间。

新的资源分配手段

- 对于多应用共享同一数据库的系统而言，可在应用层通过数据库连接池的配置，实现某一应用最大可用数据库连接数的限制，避免某一应用独占所有的数据库资源。

统一的连接管理，避免数据库连接泄露

- 在较为完善的数据库连接池实现中，可根据预先的占用超时设定，强制回收被占用连接，从而避免了常规数据库连接操作中可能出现的资源泄露。

### 连接池工作原理

JDBC 的数据库连接池使用 javax.sql.DataSource 来表示，DataSource 只是一个接口，用来进行规范约束访问数据库的流程。该接口通常由驱动程序供应商提供实现。

DataSource 通常被称为数据源，它包含连接池和连接池管理两个部分。

三种类型实现：

- 基本实现：生成标准的 Connection 对象
- 连接池实现：生成将自动参与连接池的 Connection 对象。此实现与中间层连接池管理器配合使用
- 分布式事务实现：生成可以用于分布式事务的 Connection 对象，并且总是参与连接池。

#### 连接池的建立

在系统初始化时，连接池会根据系统配置建立，并在池中创建了几个连接对象，以便使用时能从连接池中获取。连接池中的连接不能随意创建和关闭，这样避免了连接随意建立和关闭造成的系统开销。

#### 连接池中连接的使用管理

连接池管理策略是连接池机制的核心，连接池内连接的分配和释放对系统的性能有很大的影响。

当客户请求数据库连接时，首先查看连接池中是否有空闲连接，如果存在空闲连接，则将连接分配给客户使用；如果没有空闲连接，则查看当前所开的连接数是否已经达到最大连接数，如果没达到就重新创建一个连接给请求的客户；如果达到就按设定的最大等待时间进行等待，如果超出最大等待时间，则抛出异常给客户。

当客户释放数据库连接时，先判断该连接的引用次数是否超过了规定值，如果超过就从连接池中删除该连接，否则保留为其他客户服务。

该策略保证了数据库连接的有效复用，避免频繁的建立、释放连接所带来的系统资源开销。

#### 连接池的关闭

当应用程序退出时，关闭连接池中所有的连接，释放连接池相关的资源，该过程正好与创建相反。

### Druid 连接池

Druid 是类似 dbcp，c3p0 的一个数据库连接池框架，性能比这两者好，同时自带监控页面，可以实时监控应用的连接池情况以及其中性能差的 sql，方便我们找出应用中连接池方面的问题。

Druid 包括三部分：

- DruidDriver 代理 Driver，能够提供基于 Filter — Chain 模式的插件体系
- DruidDataSource 高效可管理的数据库连接池
- SQLParser 解析SQL语句

#### Druid 连接池的使用

Step1：导入 jar 包

- [下载 druid jar包](https://repo1.maven.org/maven2/com/alibaba/druid/)，并导入到项目中；导入 mysql 驱动 jar 包。

Step2：定义 druid.properties 配置文件

- 注意：properties配置文件不能有空格，值不能有双引号，行不能写分号 。

- ```properties
  driverClassName=com.mysql.cj.jdbc.Driver
  url=jdbc:mysql://localhost:3306/test?useSSL=false&characterEncoding=utf-8&serverTimezone=Asia/Shanghai
  username=root
  password=password
  #初始化连接数量
  initialSize=5
  #最大连接数
  maxActive=20
  #最大等待时间
  maxWait=60000
  #1.Destroy 线程会检测连接的时间间隔，单位毫秒 2.testWhileIdle的判断依据
  timeBetweenEvictionRunsMillis:60000
  #Destory线程中如果检测到当前连接的最后活跃时间和当前时间的差值大于minEvictableIdleTimeMillis，则关闭当前连接
  minEvictableIdleTimeMillis:300000
  #检测连接是否成功的sql,要求是查询语句
  validationQuery=SELECT 'x'
  #申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery连接是否有效
  testWhileIdle=true
  #申请连接时执行validationQuery检测连接是否有效 这个配置会降低性能
  testOnBorrow=false
  #归还连接时执行validationQuery检测连接是否有效 这个配置会降低性能
  testOnReturn=false
  #对于建立连接超过removeAbandonedTimeout的连接强制关闭
  removeAbandoned:true
  #指定连接建立多长就被强制关闭
  removeAbandonedTimeout:1800
  #指定发生removeabandoned时，是否记录当前线程的堆栈信息到日志中
  logAbandoned:true
  ```

Step3：加载配置文件

- ```java
  //1、利用Properties对象来读取文件
  Properties prop = new Properties();
  //2、获取src路径下的文件的方式 -> classLoader类加载器
  ClassLoader classLoader = DruidTest.class.getClassLoader();
  InputStream is = classLoader.getResourceAsStream("druid.properties");
  //3、将properties文件加载进内存
  prop.load(is);
  ```

Step4：获取连接池对象，通过工厂类创建

- ` DataSource ds = DruidDataSourceFactory.createDataSource(prop);`

Step5：获取数据库连接对象

- `Connection conn = ds.getConnection();`

Step6：执行 SQL 语句对象，处理结果

- `PreparedStatement pspmt = conn.prepareStatement(sql);`
- `ResultSet rs = pspmt.executeQuery();`

Step7：释放资源

- `conn.close();`

- 如果连接对象 Connection 是从连接池中获取的，那么调用 Connection.close() 方法，则不会再关闭连接了，而是归还连接。

### Druid 工具类

```java
package druid;

import com.alibaba.druid.pool.DruidDataSourceFactory;
import javax.sql.DataSource;
import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class DruidUtils {
    private static DataSource ds;
    static {
        try {
            //加载properties配置文件
            Properties properties = new Properties();
            ClassLoader classLoader = DruidUtils.class.getClassLoader();
            InputStream is = classLoader.getResourceAsStream("druid.properties");
            properties.load(is);
            //获取连接池对象
            ds = DruidDataSourceFactory.createDataSource(properties);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    //获取连接池对象
    public static DataSource getDatasource()  {
        return  ds;
    }
    //获取连接对象
    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }
    //释放资源
    public static void close(Statement stmt,Connection conn){
        close(null,stmt,conn);
    }
    public static void close(ResultSet rs, Statement stmt, Connection conn){
        if (rs != null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (stmt != null){
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn != null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

