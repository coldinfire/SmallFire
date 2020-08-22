---
title: "SAP JCO连接"
date: 2019-11-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - JCO

---



### SAP JCO简介

​	为了在R/3系统和JAVA平台之间进行实时的交换数据。SAP提供了一套高效的基于RFC的ABAP和JAVA进程间通讯组件：SAP JAV Connector.

​	Jco库提供了可以直接在JAVA程序中使用的API.该API通过JNI调用部署在客户端的SAP的RFC库。

### 安装与配置

​	下载Jco库的jar包。然后解压，将文件librfc32.dll的文件复制到目录system32下面。这个文件就是SAP的RFC协议实现。然后确保CLASSPATH环境下包含文件sapjco.jar所在的目录。该jar包中包含有在JAVA程序中需要直接调用的类和接口。

### 建立连接

​	类JCO是Jco库中最主要的一个入口，提供了很多静态方法。其中有一系列重载的createClient方法可以用来创建于SAP系统的连接信息。

- 直接输入参数

  ```JS
  import com.sap.mw.jco.*;
  JCO.Client myCont = JCO.createClient("000",        //SAP Client
                                       "UserName",   //userid
                                       "PassWord",   //password
                                       "EN",         //Language
                                       "ClientIP",   //application server host name
                                       "ClientID")   //system number
  ```

- 使用JAVA 配置文件

  ```JS
  Properties properties = new Properties();
  properties.put("jco.client.ashost","HostIP");
  properties.put("jco.client.client","client");
  properties.put("jco.client.user","user");
  properties.put("jco.client.passwd","passwd");
  properties.put("jco.client.sysnr","00");
  properties.put("jco.client.lange","EN");
  
  JCO.Client myCont = JCO.createClient(properties);
  ```

​	建立从当前JAVA进程到SAP服务器的连接:	`this.myCont.connect();`

​	获取连接状态：`if( myCont != null && myCont.isAlive())`

#### 连接池

​	Jco库支持以连接池的形式重用已经创建的连接。需要调用JCO类的静态方法addClientPool即可创建一个连接池，并可以在参数中指定连接池的名字和允许同时激活的最大连接数。

```JS
public static final String POOL_NAME = "JCO_Pool";
public static int max_cont = 3;

JCO.Pool pool = JCO.getClientPoolManager().getPool(POOL_NAME);
if(poo == null){
   Properties properties = new Properties();
    ... read properties file ...
   JCO.addClientPool(POOL_NAME,max_cont,properties);
};
```

获取连接：`myCont = JCO.getClient(POOL_NAME);`

释放连接：`JCO.releaseClient(myCont);`

移除连接池：`JCO.removeClientPool(POOL_NAME);`

 移除连接池会导致其中所有的活动连接被迫强行关闭，必须在确保连接池中所有的连接都不在被使用时才能执行该操作。

### 调用Function Models

​	Jco库使用RFC的方式来调用ABAP中的函数，所以被调用的函数必须已经勾选"Remote-enabled"属性。

- 第一步：创建JCO.Repository类的对象，获取所有ABAP函数的元数据。

  - `JCO.Repository myRepository = new JCO.Repository("Repository",myCont/POOL_NAME);`
  - 构造函数有两个参数，第一个是可以任意指定的名字；第二个是当前使用的连接。可以指定连接池名字，Jco库会自动从该连接池获取连接	

- 第二步：通过该实例获取函数的信息。

  ```JS
  String strFunc = "BAPI_NAME";
  IFunctionTemplate ft =  myRepository.getFunctionTemplate(strFunc.toUpperCase());
  JCO.Function function = ft.getFunction();
  //Get a client form the pool
  JCO.Clietn client = JCO.getClient(pool);
  //Set up scalar parameter
  JCO.ParameterList input = function.getImportParameterList();
  input.setValue(10,"MAX_ROWS");
  //Set up structure parameter
  JCO.Structure sFrom = input.getStructure("STRUC_NAME");
  sFrom.seValue("Value","FIELD_NAME");
  input.setValue(sFrom,"STRUC_NAME");
  //Set up table parameter
  JCO.Table table = function.getTableParameterList().getTable("TABLE_NAME");
  table.appendRow();
  table.setRow(0);
  talbe.setValue("Value","FIELD_NAME");
  ....
  table.appendRow();
  table.setRow(n);
  table.setValue("Value","FIELD_NAME");
  ```
  
- 第三步：执行

  - `JCO.Client client = JCO.getClient(POOL_NAME); client.execute(function);`
  - `myCont.execute(funtion);`

- 第四步：关闭连接`myCont.disconnect();`或则`JCO.releaseClient(myCont)`

- 第五步：获取输出参数

  ```JS
  // GET export structure
  JCO.Structure struct = function.getExportParameterList.getStructure("RETURN");
  //GET table parameter
  JCO.Table table = function.getTableParameterList().getTable("TABLE_NAME");
  ```


### 异常处理

- JCO.AbapException：ABAP函数执行过程中出现异常，在JAVA进程中触发该异常。
- JCO.ConversionException：当执行参数的get,set方法时，如果在Java类型和ABAP类型间转换失败。

### 调试

- 激活Jco的ABAP调试功能：

  ```js
  JCO.Pool pool = JCO.getClientPoolManage().getPool(POOL_NAME);
  pool.setAbapDebug(true);
  ```

- 在ABAP程序内设置外部断点。

  - 并设置Debugging里设置External Debugging的Users为设置的外部名。



### DATA TYPE

| ABAP Type | Description             | JAVA Data Type | JCo Type Code    | JCo Access Method          |
| --------- | ----------------------- | -------------- | ---------------- | -------------------------- |
| b         | 1-byte integer          | int            | JCO.TYPE_INT1    | int getInt()               |
| s         | 2-byte integer          | int            | JCO.TYPE_INT2    | int getInt()               |
| I         | 4-byte integer          | int            | JCO.TYPE_INT     | int getInt()               |
| C         | Character               | String         | JCO.TYPE_CHAR    | String getString()         |
| N         | Numerical Character     | String         | JCO.TYPE_NUM     | String getString()         |
| P         | Binary Coded Decimal    | BigDecimal     | JCO.TYPE_BCD     | BigDecimal getBigDecimal() |
| D         | Date                    | Date           | JCO.TYPE_DATE    | Date getDate()             |
| T         | Time                    | Date           | JCO.TYPE_TIME    | Date getTime()             |
| F         | Float                   | double         | JCO.TYPE_FLOAT   | double getDouble()         |
| X         | Raw data                | bytes[]        | JCO.TYPE_BYTE    | byte[] getByteArray()      |
| g         | String(variable-length) | String         | JCO.TYPE_STRING  | String getString()         |
| y         | Raw data                | byte[]         | JCO.TYPE_XSTRING | byte[] getByteArray()      |

### JCO Table method

| JCO.Table Method             | Description                             |
| ---------------------------- | --------------------------------------- |
| int getNumRows()             | Returns teh number of rows              |
| void setRow(int pos)         | Sets the current row pointer            |
| int getRow()                 | Returns the current row pointer         |
| void firstRow()              | Moves to the first row                  |
| void firstRow()              | Moves to the last row                   |
| boolean nextRow()            | Moves to the next row                   |
| boolean previousRow(0)       | Moves to the previous row               |
| void appendRow()             | Adds one row at the end of the table    |
| void appendRow(int num_rows) | Adds multiple rows at the end of table  |
| void deleteAllRows()         | Deletes all table rows                  |
| void deleteRow()             | Delete the current row                  |
| void deleteRow(int pos)      | Deletes the specified row               |
| void insertRow(int pos)      | Inserts a row at the specified position |

