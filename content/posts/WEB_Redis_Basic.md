---
title: " Redis 基本使用 "
date: 2018-03-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - Redis

---

### Redis 简介

redis 时一款高性能的非关系型数据库(NOSQL)，包含多种数据结构、支持网络、可选持久性的键值对存储数据库。

- 数据的存储格式是key,value形式，且数据之间没有关联关系
- 数据存储在内存中，性能高效
- 支持分布式，理论上可以无限扩展

关系型数据库和 NoSQL 数据库是互补的关系。一般将数据存储在关系型数据库中，在 nosql 数据库中备份存储关系型数据库的数据。

### Redis 命令操作

#### Redis 数据结构

redis 存储的是 key,value 形式的数据，key 都是字符串，value 有5中不同的数据结构。

字符串类型：string；可以存储字符串、图片等多种类型，最大长度支持 512 M。

- GET key/MGET key1 [key2 ...]：获取单个/多个字符元素
- SET/MSET/SETEX/MSETNX：存储单个/多个值/设置指定过期时间的键值对
- DEL key1 [key2 ...]：删除指定 key
- INCR/DECR
- GETSET

哈希类型：hash；map 格式，key 和 value 都是字符串类型

- HGET/HMGET/HGETALL：获取一个/多个/所有字段的值
- HSET/HMSET/HSETNX：赋值一个/多个字段/键不存在时设置值
- HDEL：删除字段
- HKEYS/HVALS：获取所有字段名/字段值
- HEXISTS/HLEN

列表类型：list；基于 linkedList 实现，是一个插入顺序排序的字符串元素集合。

- LPUSH/LPUSHX/RPUSH/RPUSHX/LINSERT/LSET
- LPOP/RPOP：删除列表中最左/右边的数据，并将元素返回
- LINDEX/LRANGE：获取列表在：给定位置的单个元素/给定范围内的所有值
- LLEN/LTRIM

集合类型：Set；集合中的元素没有顺序, 且元素是唯一的。Set 类型的底层是通过哈希表实现的。

- SADD
- SMEMBERS：获取 set 集合中所有元素
- SREM/SPOP/SMOVE：删除 set 集合中的元素
- SCARD：获取 set 集合长度
- SINTER/SDIFF/SDIFFSTORE/SUNION

有序集合类型：sorted set；每个元素都会关联一个 double 类型的分数权值，通过这个权值来为集合中的成员进行从小到大的排序。与 Set 类型一样，其底层也是通过哈希表实现的。

- ZADD
- ZRANGE：获取指定范围内的所有值
- ZREM/ZPOP/ZMOVE
- ZCARD/ZCOUNT
- ZINTER/ZDIFF/ZDIFFSTORE/ZUNION

#### 通用命令

`keys *` ：查询所有的键

`type key`：获取键对应的 value 的类型

`del key`： 删除指定的 key:value

`exists key [key ...]`：判断是否存在指定的 key

`expire kery second`：设置 key 的过期时间 

### Redis 持久化

将 redis 内存中的数据持久化保存到磁盘中。

#### RDB(默认方式)

默认使用的机制，在一定的时间间隔中，检查 key 的变化情况，然后持久化数据。

Step1：在文件 redis.windows.conf 中配置保存时间

![RDB 持久化设置](/images/WEB/Redis1.png)

Step2：重启 redis 服务器，并指定配置文件名称

- redis-server.exe redis.windows.conf

#### AOF (Append only file)

日志记录的方式，可以记录每一条命令的操作。在每一次操作后都进行数据持久化。

Step1：编辑 redis.windows.conf 文件：`appendonly no` 关闭 aof；`appendonly yes` 开启 aof。

![AOF 持久化设置](/images/WEB/Redis2.png)

Step2：设置持久化的方式

- `appendfsync no` ：不进行持久化
- `appendfsync always` ：每一次操作都进行持久化
- `appendfsync everysec`：每隔一秒进行一次持久化

### JAVA 操作 Redis

#### 下载 jar 包

将准备好的 jar 包添加到项目中。

- commons-pool2-x.x.jar
- jedis-x.x.x.jar

#### 程序使用

```java
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;
public class RedisTest {
  // 直接获取并使用
  @Test
  public void test1() {
    // 获取核心对象：jedis(host,port)
    Jedis jedis = new Jedis("127.0.0.1", 6379);
    jedis.set("name", "test");
    String value = jedis.get("name");
    System.out.println(value);
    jedis.close();
  }
  // 通过连接池获取并使用
  @Test
  public void test2() {
    // 获取连接池配置对象
    JedisPoolConfig config = new JedisPoolConfig();
    // 设置最大连接数
    config.setMaxTotal(30);
    // 设置最大的空闲连接
    config.setMaxIdle(10);
    // 获取连接池
    JedisPool jedisPool = new JedisPool(config,"127.0.0.1",6379);
    // 获取核心对象：jedis
    Jedis jedis = jedisPool.getResource();
    jedis.set("jedis", "testJedis");
    String value = jedis.get("jedis");
    System.out.println(value);
    jedis.close();
    jedisPool.close();
  }
}
```
