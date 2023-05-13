---
title: Redis
date: 2021-08-09 10:20:43
tags: Nosql
categories: 学习笔记
---

**Redis**（Remote Dictionary Server）

## Linux(centos 7)使用

`yum install -y gcc tcl`

解压redis安装包，并进入解压目录

执行`make && make install`

默认的安装路径是在`usr/local/bin`目录下

该目录以及默认配置到环境变量，因此可以在任意目录下运行这些命令。

其中：

- redis-cli：是redis提供的命令行客户端
- redis-server：是redis的服务器启动脚本
- redis-sentinel：是redis的哨兵启动脚本

直接使用`redis-server`可进行前台启动



如果要让Redis以后台方式启动，则必须修改redis的配置文件，即修改解压目录下的redis.conf。

备份：`cp redis.conf redis.conf.bak`

修改配置：

```conf
#监听的地址，默认是127.0.0.1，会导致只能在本地访问，修改为0.0.0.0则可以在任意ip访问，生产环境不要设置为0.0.0.0
bind 0.0.0.0
#守护进程，修改为yes后即可后台运行
daemonize yes
#密码，设置后访问redis必须输入密码
requirepass 17221273
```

其他配置项：

```conf
#监听的端口
port 6379
#工作目录，默认当前目录（启动redis时所在目录）（建议在解压目录内启动）
dir .
#数据库数量，设置为1，代表只能使用1个库，默认16个库，编号0-15
database 1
#设置redis能够使用的最大内存
maxmemory 512mb
#日志文件，默认为空，不记录日志，可以指定日志文件名
logfile "redis.log"
```

启动redis：

进入redis安装目录，启动：`redis-server redis.conf`

开放6379端口号：`firewall-cmd --zone=public --add-port=6379/tcp --permanent`

重新加载：`firewall-cmd --reload`

查看开放的端口号：`firewall-cmd --zone=public --list-ports`





开机自启需要单独编写redis.service（网上查）

## Windows下使用

+ redis-server.exe启动服务，默认端口号6379
+ redis-cli.exe打开客户端
+ ping命令测试是否连接，连接正常返回PONG

## Redis的基本知识

redis默认有16个数据库

![数据库数量配置](image-20210709120427992.png)

默认使用第0个数据库

可以使用select进行切换

```bash
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> select 0
OK
127.0.0.1:6379> DBSIZE
(integer) 0
127.0.0.1:6379>
```

查询当前数据库所有的key `keys *`

清除当前数据库`flushdb `

清除全部数据库的内容`FLUSHALL`

> Redis是单线程的

Redis是基于内存操作，CPU不是Redis性能瓶颈，Redis的瓶颈是内存和网络带宽。



使用`help @generic`查看常用命令用法



## Redis通用命令

通用命令是部分数据类型的，都可以使用的指令，常见的有：

- KEYS：查看符合模板的所有key，<font style="color:red">不建议在生产环境设备上使用。</font>
- DEL：删除一个指定的key
- EXISTS：判断key是否存在
- EXPIRE：给一个key设置有效期，有效期到期时该key会被自动删除
- TTL：查看一个KEY的剩余有效期

通过help [command]可以查看一个命令的具体用法，例如：`help keys`



## 五大数据类型

### String

String类型，也就是字符串类型，是redis中最简单的存储类型。

其value是字符串，不过根据字符串格式不同，又分为3类：

- string：普通字符串
- int：整数类型，可以做自增、自减操作
- float：浮点类型，可以做自增、自减操作

不管是哪种格式，底层都是字节数组形式存储，只不过是编码方式不同。字符串类型的最大空间不能超过512m。

| KEY   | VALUE       |
| ----- | ----------- |
| msg   | hello world |
| num   | 10          |
| score | 92.5        |

#### String类型的常见命令

- SET：添加或者修改已经存在的一个String类型的键值对
- GET：根据key获取String类型的键值对
- MSET：批量添加多个String类型的value
- INCR：让一个整型的key自增1
- INCRBY：让一个整形的key自增并指定步长，例如：incrby num 2 让num值自增2
- INCRBYALOAT：让一个浮点类型的数字自增并指定步长
- SETNX：添加一个String类型的键值对，前提是这个key不存在，否则不执行：
- SETEX：添加一个String类型的键值对，并且指定有效期



### Hash

Hash类型，也叫散列，其value是一个无序字典，类似于Java中的HashMap结构。

String结构是将对象序列化为JSON字符串后存储，当需要修改对象某个字段时很不方便：

| KEY            | VALUE                 |
| -------------- | --------------------- |
| project:user:1 | {name:"Jack", age:21} |
| project:user:2 | {name:"Rose", age:18} |

Hash结构可以将对象中的每个字段独立存储，可以针对单个字段做CRUD：

| key            | value | -     |
| -------------- | ----- | ----- |
|                | field | value |
| project:user:1 | name  | Jack  |
|                | age   | 21    |
| project:user:2 | name  | Rose  |
|                | age   | 18    |

#### Hash类型的常见命令

- HSET key field value：添加或者修改hash类型key的field的值
- HGET key field：获取一个hash类型key的field的值
- HMSET：批量添加多个hash类型key的field的值（针对一个key批量添加多个field）（redis4.0以后被废弃）
- HMGET：批量获取多个hash类型key的field的值
- HGETALL：获取一个hash类型的key中的所有的field和value
- HKEYS：获取一个hash类型的key中的所有的field
- HVALS：获取一个hash类型的key中所有的value
- HINCRBY：让一个hash类型key的字段值自增并指定步长
- HSETNX：添加一个hash类型的key的field值，前提是这个field不存在，否则不执行



### List

Redis中的LIst类型与Java中的LinkedList类似，可以看做是一个双向链表结构。既可以支持正向检索也可以支持反向检索。

特征也与LinkedList类似：

- 有序
- 元素可以重复
- 插入和删除块
- 查询速度一般

#### List常见命令

- LPUSH key element ...：向列表左侧插入一个或多个元素
- LPOP key：移除并返回列表左侧的第一个元素，没有则返回nil
- RPUSH key element ...：向列表右侧插入一个或多个元素
- RPOP key：移除并返回列表右侧的第一个元素
- LRANGE key star end：返回一段角标范围内的所有元素
- BLPOP和BRPOP：与LPOP和RPOP类似，只不过在没有元素时等待指定时间，而不是直接返回nil



### Set（集合）

Redis的Set结构与Java中的HashSet类似，可以看做是一个value为null的HashMap。因此也是一个hash表，因此具备与HashSet类似的特征：

- 无序
- 元素不可重复
- 查找快
- 支持交集、并集、差集等功能

#### Set类型的常见命令

- SADD key member .. ：向set中添加一个或多个元素
- SREM key member ...：移除set中的指定元素
- SCARD key：返回set中元素的个数
- SISMEMBER key member：判断一个元素是否存在于set中
- SMEMBERS：获取set中的所有元素
- SINTER key1 key2 ...：求key1与key2的交集
- SDIFF key1 key2 ...：求key1与key2的差集（在key1中但不在key2中）
- SUNION key1 key2 ...：求key1和key2的并集



### SortedSet（有序集合）

redis的SortedSet是一个可排序的set集合，与java中的TreeSet有些类似，但底层数据结构却差别很大。SortedSet中的每一个元素都带有一个score属性，可以基于score属性对元素排序，底层的实现是一个跳表（SkipList）加hash表。

SortedSet具备下列特征：

- 可排序
- 元素不重复
- 查询速度块

因为SortedSet的可排序性，经常被用来实现排行榜这样的功能。

#### SortedSet的常见命令

- ZADD key score member：添加一个或多个元素到sorted set，如果已经存在则更新其score值
- ZREM key member：删除sorted set中的一个指定元素
- ZSCORE key member：获取sorted set中指定元素的score值
- ZRANK key member：获取sorted set中的指定元素的排名
- ZCARD key：获取sorted set中元素的个数
- ZCOUNT key min max：统计score值在给定范围内的所有元素的个数
- ZINCRBY key increment member：让sorted set中的指定元素自增，步长为指定的increment值
- ZRANGE key min max：按照score排序后，获取指定排名范围内的元素
- ZRANGEBYSCORE key min max：按照score排序后，获取指定score范围内的元素
- ZDIFF、ZINTER、ZUNION：求差集、交集、并集



## key的层级格式

redis的key允许有多个单词形成层级结构，多个单词之间用':'隔开，格式如下：`项目名:业务名:类型:id`

这个格式并非固定，也可以根据自己的需求来删除或添加词条。



## 三种特殊数据类型

### Geospatial 地理位置

#### GEO的常见命令

- GEOADD：添加一个地理空间信息，包含：经度（longitude）、纬度（latitude）、值（member）
- GEODIST：计算指定的两个点之间的距离并返回
- GEOHASH：将指定member的坐标转为hash字符串形式并返回
- GEOPOS：返回指定member的坐标
- GEORADIUS：指定圆心、半径，找到该圆内包含的所有member，并按照与圆心之间的距离排序后返回。6.2以后已废弃
- GEOSEARCH：在指定范围内搜索member，并按照与指定点之间的距离排序后返回。范围可以是圆形或矩形。6.2新功能
- GEOSEARCHSTORE：与GEOSEARCH功能一致，不过可以把结果存储到一个指定的key。6.2新功能



6个命令

> geoadd

```bash
# 添加地理位置
# 参数 key 值（经度、纬度、名称）
127.0.0.1:6379> GEOADD china:city 116.40 39.90 beijing
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.23 shanghai
(integer) 1
127.0.0.1:6379> geoadd china:city 106.50 29.53 chongqing
(integer) 1
127.0.0.1:6379> geoadd china:city 120.16 30.24 hangzhou 108.96 34.26 xian
(integer) 2
```

> geopos

```bash
127.0.0.1:6379> geopos china:city beijing	#获取指定城市的经度和纬度
1) 1) "116.39999896287918"
   2) "39.900000091670925"
127.0.0.1:6379> geopos china:city beijing chongqing
1) 1) "116.39999896287918"
   2) "39.900000091670925"
2) 1) "106.49999767541885"
   2) "29.529999579006592"
```

>geodist（返回两个给定位置之间的距离）

单位：

+ m表示米
+ km表示单位为千米
+ mi表示单位为英里
+ ft表示单位为英尺

```bash
127.0.0.1:6379> geodist china:city beijing shanghai	#查看北京到上海的距离
"1067378.7564"
127.0.0.1:6379> geodist china:city beijing shanghai km
"1067.3788"
```

> georadius 以给定的经纬度为中心，找出某一半径内的元素

```bash
127.0.0.1:6379> GEORADIUS china:city 110 30 1000 km	# 以110，30这个经纬度为中心，寻找方圆1000km内的城市
1) "chongqing"
2) "xian"
3) "hangzhou"
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km
1) "chongqing"
2) "xian"
127.0.0.1:6379> georadius china:city 110 30 500 km withdist	# 显示到中间距离的位置
1) 1) "chongqing"
   2) "341.9374"
2) 1) "xian"
   2) "483.8340"
127.0.0.1:6379> georadius china:city 110 30 500 km withcoord	# 显示他人的定位信息
1) 1) "chongqing"
   2) 1) "106.49999767541885"
      2) "29.529999579006592"
2) 1) "xian"
   2) 1) "108.96000176668167"
      2) "34.2599996441893"
127.0.0.1:6379> georadius china:city 110 30 500 km withcoord count 1	# count查询的个数
1) 1) "chongqing"
   2) 1) "106.49999767541885"
      2) "29.529999579006592"
127.0.0.1:6379> georadius china:city 110 30 500 km withcoord count 2
1) 1) "chongqing"
   2) 1) "106.49999767541885"
      2) "29.529999579006592"
2) 1) "xian"
   2) 1) "108.96000176668167"
      2) "34.2599996441893"
```

> georadiusbymember 找出位于指定范围内的元素，中心点是由给定的位置元素决定

```bash
127.0.0.1:6379> GEORADIUSBYMEMBER china:city beijing 1000 km
1) "beijing"
2) "xian"
127.0.0.1:6379> GEORADIUSBYMEMBER china:city shanghai 400 km
1) "hangzhou"
2) "shanghai"
```

> geohash 返回一个或多个位置元素的Geohash表示

该命令将返回11个字符的Geohash字符串

```bash
# 将二维的经纬度转换为一维的字符串，如果两个字符串越接近，那么则距离越近
127.0.0.1:6379> geohash china:city beijing chongqing
1) "wx4fbxxfke0"
2) "wm5xzrybty0"
```

> geo底层的实现原理其实就是Zset！可以使用Zset命令来操作geo

```bash
127.0.0.1:6379> ZRANGE china:city 0 -1	# 查看地图中全部的元素
1) "chongqing"
2) "xian"
3) "hangzhou"
4) "shanghai"
5) "beijing"
127.0.0.1:6379> zrem china:city beijing	# 移除指定元素
(integer) 1
127.0.0.1:6379> zrange china:city 0 -1
1) "chongqing"
2) "xian"
3) "hangzhou"
4) "shanghai"
```

### Hyperloglog

- UV：全称Unique Visitor，也叫独立访客量，是指通过互联网访问、浏览这个网页的自然人。1天内同一个用户多次访问该网站，只记录1次。
- PV：全称Page View，也叫页面访问量或点击量，用户每访问网站的一个页面，记录1次PV，用户多次打开页面，则记录多次PV。往往用来衡量网站的流量。

Hyperloglog（HLL）是从Loglog算法派生的概率算法，用于确定非常大的集合的基数，而不需要存储其所有值。相关算法原理可以参考：https://juejin.cn/post/6844903785744056333#heading-0

Redis中的HLL是基于string结构实现的，对单个HLL的内存永远小于16kb，内存占用低的令人发指！作为代价，其测量结果是概率性的，有小于0.81%的误差。不过对于UV统计来说，这完全可以忽略。

#### HLL的命令

- PFADD
- PFCOUNT
- PFMERGE



> 什么是基数？

A{1,3,5,7,8,7}

B{1,3,5,7,8}

基数（去重后元素的个数） = 5

Redis Hyperloglog基数统计的算法。

优点：占用的内存是固定的，2^64不同的元素的技术，只需要废12KB内存。如果要从内存角度来比较的话Hyperloglog首选。

**网页的UV（一个人访问一个网站多次，但是还是算作一个人）**

传统的方式，set保存用户的id，然后就可以统计set中的元素数量作为标准判断。

这个方式如果保存大量的用户id，就会比较麻烦，我们的目的是为了计数，而不是保存用户的id。存在0.81%错误率，但是统计UV任务，可以忽略不计。

> 测试使用

```bash
127.0.0.1:6379> PFADD mykey a b c d e f g h i j	# 创建第一组元素
(integer) 1
127.0.0.1:6379> PFCOUNT mykey	# 统计 mykey 元素的基数数量
(integer) 10
127.0.0.1:6379> PFADD mykey2 i j z x c v b n m	# 创建第二组元素
(integer) 1
127.0.0.1:6379> PFCOUNT mykey2
(integer) 9
127.0.0.1:6379> PFMERGE mykey3 mykey mykey2	# 合并两组 mykey mykey2 => mykey3 并集
OK
127.0.0.1:6379> PFCOUNT mykey3
(integer) 15
```

如果允许容错，那么一定可以使用Hyperloglog

如果不允许容错，就使用set或者自己的数据类型即可

### Bitmaps

Redis中是利用string类型数据结构实现BitMap，因此最大上限是512M，转换为bit则是2^32个bit。

> 位存储

Bitmaps位图，操作二进制位来进行记录，只有0和1两个状态。

#### BitMaps的操作命令

- SETBIT：向指定位置（offset）存入一个0或1

- GETBIT：获取指定位置（offset）的bit值
- BITCOUNT：统计BItMaps中值为1的bit位的数量
- BITFIELD：操作（查询、修改、自增）BitMap中bit数组中的指定位置（offset）的值
- BITFIELD_RO：获取BItMap中bit数组，并以十进制形式返回
- BITOP：将多个BitMap的结果做位运算（与、或、异或）
- BITPOS：查找bit数组中指定范围内第一个0或1出现的位置

```bash
127.0.0.1:6379> SETBIT sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 0
(integer) 0
127.0.0.1:6379> setbit sign 2 0
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 1
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
127.0.0.1:6379> setbit sign 6 0
(integer) 0
127.0.0.1:6379> getbit sign 3
(integer) 1
127.0.0.1:6379> getbit sign 6
(integer) 0
127.0.0.1:6379> BITCOUNT sign
(integer) 3
```

## 事务

Redis单条命令是保存原子性的，但是事务不保证原子性

所有的命令在事务中并没有直接被执行，只有发起执行命令的时候才会执行。

redis的事务：

+ 开启事务（multi）
+ 命令入队（...）
+ 执行事务（exec）

> 正常执行事务

```bash
127.0.0.1:6379> multi	# 开启事务
OK
# 命令入队
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec	# 执行事务
1) OK
2) OK
3) "v2"
4) OK
```

> 放弃事务

```bash
127.0.0.1:6379> multi	#开启事务
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> discard	#取消事务
OK
127.0.0.1:6379> get k4
(nil)
```

> 编译型异常（代码有问题，命令有错），事务中所有的命令都不会被执行



> 运行时异常（1/0），如果事务队列中存在语法性错误，那么执行命令的时候，其他命令是可以正常执行的



**悲观锁：**

+ 很悲观，认为什么时候都会出问题，无论做什么都会加锁

**乐观锁：**

+ 很乐观，认为什么时候都不会出问题，所以不会上锁，更新数据的时候去判断一下，在此期间是否有人修改过这个数据。
+ 获取version
+ 更新的时候比较version

> Redis监视测试

正常执行成功

```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money	# 监视money对象
OK
127.0.0.1:6379> multi	# 事务正常结束，数据期间没有发生变动
OK
127.0.0.1:6379> DECRBY money 20
QUEUED
127.0.0.1:6379> INCRBY out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
```

执行事务时若watch到money发生改变，则此事务会执行失败。

使用watch可以当作redis的乐观锁操作。

## Jedis

使用Java操作Redis

1.导入对应的依赖

```xml
	<dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.3.0</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.76</version>
    </dependency>
```

2.编码测试：

+ 连接数据库

+ 操作命令
+ 断开连接

```java
public class TestPing {
    public static void main(String[] args) {
        //1.new jedis对象
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        //jedis所有的命令就是redis的所有命令
        System.out.println(jedis.ping());//输出PONG
    }
}
```

## SpringDataRedis

SpringData是Spring中数据操作的模块，包含对各种数据库的集成，其中对Redis的集成模块就叫做SpringDataRedis。

- 提供了对不同Redis客户端的整合（Lettuce和Jedis）
- 提供了RedisTemplate统一API来操作Redis
- 支持Redis的发布订阅模型
- 支持Redis哨兵和Redis集群
- 支持基于Lettuce的响应式编程
- 支持基于JDK、JSON、字符串、Spring对象的数据序列化及反序列化。
- 支持基于Redis的JDKCollection实现



SpringDataRedis中提供了RedisTemplate工具类，其中封装了各种对Redis的操作。并且将不同数据类型的操作API封装到了不同的类型中：

| API                         | 返回值类型      | 说明                  |
| --------------------------- | --------------- | --------------------- |
| redisTemplate.opsForValue() | ValueOperations | 操作String类型数据    |
| redisTemplate.opsForHash()  | HashOperations  | 操作Hash类型数据      |
| redisTemplate.opsForList()  | ListOperations  | 操作List类型数据      |
| redisTemplate.opsForSet()   | SetOperations   | 操作Set类型数据       |
| redisTemplate.opsForZSet()  | ZSetOperations  | 操作SortedSet类型数据 |
| redisTemplate               |                 | 通用命令              |



### StringRedisTemplate

为了节省内存空间，我们并不会使用JSON序列化器来处理value，而是统一使用String序列化器，要求只能存储String类型的key和value。当需要存储Java对象时，手动完成对象的序列化和反序列化。



### RedisTemplate的两种序列化方案：

方案一：

1. 自定义RedisTemplate
2. 修改RedisTemplate的序列化器为GenericJackson2JsonRedisSerializer

方案二：

1. 使用StringRedisTemplate
2. 写入Redis时，手动把对象序列化为JSON
3. 读取Redis时，手动把读取到的JSON反序列化为对象

## SpringBoot整合

SpringBoot2.x之后，原来使用的jedis被替换为了lettuce

jedis：采用的直连，多个线程操作的话是不安全的，如果想要避免不安全的，使用JedisPool连接池。

lettuce：采用netty，实例可以在多个线程中进行共享，不存在线程不安全的情况。

> 整合

1.导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

2.配置连接

```yaml
spring:
  redis:
    host: 127.0.0.1
    port: 6379
```

3.测试



## Redis.conf详解

> 包含

![Redis.conf](image-20210720180203714.png)

> 网络

```bash
bind 127.0.0.1	#绑定的ip
protected-mode yes	#保护模式
port 6379	#端口设置
```

> 通用 GENERAL

```bash
daemonize yes	# 以守护进程的方式运行，默认是no，我们需要自己开启为yes

pidfile /var/run/redis.pid	# 如果以后台的方式运行，就需要指定一个pid文件

#日志
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel notice
logfile ""	#日志的文件位置名
databases 16 #数据库的数量，默认是 16 个数据库
always-show-logo yes # 是否总是显示LOGO
```

> 快照 SNAPSHOTTING

持久化，在规定的时间内，执行了多少次操作，则会持久化到文件`.rdb` `.aof`

redis是内存数据库，如果没有持久化，那么数据断电即失！

```bash
#如果900秒内，如果至少有一个key进行了修改，就进行持久化操作
save 900 1
#如果300s内，如果至少10 key进行了修改，就进行持久化操作
save 300 10
#如果60s内，如果至少10000 key进行了修改，就进行持久化操作
save 60 10000

stop-writes-on-bgsave-error yes #持久化如果出错，是否还需要继续工作

rdbcompression yes #默认值是yes。对于存储到磁盘中的快照，可以设置是否进行压缩存储。建议不开启，压缩也会消耗cpu，磁盘不值钱

rdbchecksum yes #默认值是yes。在存储快照后，我们还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。

dir ./ # rdb文件保存的目录
```

> 主从复制 REPLICATION



> SECURITY

可以为redis设置密码，默认没有密码

`config get requirepass` 获取redis密码

`config set requirepass "123456"` 设置redis密码

`auth 123456` 使用密码进行登录

> 限制 LIMITS

```bash
maxClients 10000 #设置能连接上redis的最大客户端的数量
maxmemory <bytes> #redis 配置最大的内存容量

maxmemory-policy noeviction #内存达到上限之后的处理策略
    1.volatile-lru -> remove the key with an expire set using an LRU algorithm
    只对设置了过期时间的key进行LRU
    2.allkeys-lru -> remove any key according to the LRU algorithm
    删除LRU算法的key
    3.volatile-random -> remove a random key with an expire set
    在设置了过期时间的key中进行随机删除
    4.allkeys-random -> remove a random key, any key
    随机删除key
    5.volatile-ttl -> remove the key with the nearest expire time (minor TTL)
    删除即将过期的key
    6.noeviction -> don't expire at all, just return an error on write operations
    永不过期，返回错误

```

> AOF配置 APPEND ONLY MODE

```bash
appendonly no #默认不开启aof模式，默认是使用rdb方式持久化的，在大部分的情况下，rdb完全够用

appendfilename "appendonly.aof" # 持久化的文件的名字

# appendfsync always #每次修改都会 sync，消耗性能
appendfsync everysec #每秒执行一次 sync
# appendfsync no	 #不执行 sync(同步)，操作系统自己同步数据，速度最快
```



## Redis持久化

Redis是内存数据库，如果不将内存中的数据库保存到磁盘，那么一旦服务器进程退出，服务器中的数据库状态也会消失。所以Redis提供了持久化功能。

### RDB(Redis Database)

在指定的时间间隔内将内存中的数据集快照(SnapShot)写入磁盘，它恢复时将快照文件直接读到内存中。

Redis会单独创建(fork)一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程不进行任何IO操作的，这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那么RDB方式比AOF方式更加高效。RDB的缺点是最后一次持久化后的数据可能丢失。

rdb保存的文件是dump.rdb，在配置文件中`SNAPSHOTTING`部分进行了配置。`dbfilename dump.rdb`

#### 1.RDB生成快照保存的三种触发方式

1. **save触发方式**

   该命令会阻塞当前的Redis服务器，执行save命令期间，Redis不能处理其他命令，直到RDB过程完成为止。执行完成的时候如果存在老的RDB文件，就把新的替代掉旧的。如果客户端多达几万或几十万，那么这种方式显然不可取。

2. **bgsave触发方式**（bg即backgroud，后台）

   执行该命令时，Redis会在后台异步进行快照操作，快照同时还可以响应客户端请求。

   具体操作为Redis进程执行fork操作创建子进程，RDB持久化过程由子进程负责，完成后自动结束。阻塞只发生在fork阶段，一般时间很短。基本上Redis内部所有的RDB操作都是采用bgsave命令。

3. **自动触发**

   自动触发是由我们的配置文件来完成的。

   ```bash
   # 900秒内，如果至少有1个key被修改，则执行bgsave，如果save "" 则表示禁用rdb
   save 900 1
   save 300 10
   save 60 10000
   ```

#### 2.save与bgsave对比

| 命令   | save             | bgsave             |
| ------ | ---------------- | ------------------ |
| IO类型 | 同步             | 异步               |
| 阻塞   | 是               | 是（发生在fork）   |
| 复杂度 | O(n)             | O(n)               |
| 优点   | 不会消耗额外内存 | 不阻塞客户端命令   |
| 缺点   | 阻塞客户端命令   | 需要fork，消耗内存 |

#### 3.RDB的优点和缺点

优点：

1. RDB文件紧凑，全量备份，非常适合用于进行备份和灾难恢复。
2. 生成RDB文件的时候，redis主进程会fork()一个子进程来处理所有保存工作，主进程不需要进行任何磁盘IO操作。
3. RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快。

缺点：

​	RDB快照是一次全量备份，存储的是内存数据的二进制序列化形式，存储上非常紧凑。当进行快照持久化时，会开启一个子进程专门负责快照持久化，子进程会拥有父进程的内存数据，父进程修改内存子进程不会反应出来，所以在快照持久化期间修改的数据不会被保存，可能丢失数据。

### AOF(Append Only File)

全量备份总是耗时的，有时候我们提供一种更加高效的方式AOF，工作机制很简单，redis会将每一个收到的写命令都通过write函数追加到文件中。通俗的理解就是日志记录。

以日志的形式来记录每个写操作，将Redis执行过的所有指令记录下来（读操作不记录），只许追加记录不可以修改记录，redis启动之初会读取该文件重新构建数据。

aof保存的是appendonly.aof文件。

如果aof文件有错误，此时redis是无法启动的，需要修复aof文件

redis提供了一个工具`redis-check-aof`

#### 1.文件重写原理

AOF的方式也同时带来了另一个问题。持久化文件会变的越来越大。为了压缩aof的持久化文件。redis提供了bgrewriteaof命令。将内存中的数据以命令的方式保存到临时文件中，同时会fork出一条新进程来将文件重写。

![文件重写原理](文件重写原理.jpeg)

重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似。

#### 2.AOF三种触发机制

| 配置项   | 刷盘时机     | 优点                     | 缺点                         |
| -------- | ------------ | ------------------------ | ---------------------------- |
| always   | 同步刷盘     | 可靠性高，几乎不丢失数据 | 性能影响大                   |
| everysec | 每秒刷盘     | 性能适中                 | 最多丢失1秒数据              |
| no       | 操作系统控制 | 性能最好                 | 可靠性较差，可能丢失大量数据 |

因为是记录命令，AOF文件会比RDB文件大得多。而且AOF会记录对同一个key的多次写操作，但只有最后一次写操作才有意义。通过执行bgrewriteaof命令，可以让AOF文件执行重写功能，用最少的命令达到相同的效果。

Redis也会在触发阈值时自动去重写AOF文件。阈值也可以在redis.conf中配置：

```bash
# AOF文件比上次文件 增长超过多少百分比则触发重写
auto-aof-rewrite-percentage 100
# AOF文件体积最小多大以上才触发重写
auto-aof-rewrite-min-size 64mb
```

两个需同时配置

#### 3.AOF的优点和缺点

优点：

1. AOF可以更好的保护数据不丢失，一般AOF会每隔1秒，通过一个后台线程执行一次fsync操作，最多丢失1秒钟的数据。
2. AOF日志文件没有任何磁盘寻址的开销，写入性能非常高，文件不容易破损。
3. AOF日志文件即使过大的时候，出现后台重写操作，也不会影响客户端的读写。
4. AOF日志文件的命令通过非常可读的方式进行记录，这个特性非常适合做灾难性的误删除的紧急恢复。比如某人不小心用flushall命令清空了所有数据，只要这个时候后台rewrite还没有发生，那么就可以立即拷贝AOF文件，将最后一条flushall命令给删了，然后再将该AOF文件放回去，就可以通过恢复机制，自动恢复所有数据

缺点：

1. 对于同一份数据来说，AOF日志文件通常比RDB数据快照文件更大。
2. AOF运行效率比RDB慢。



#### RDB与AOF对比

RDB和AOF各有自己的优缺点，如果对数据安全性要求较高，在实际开发中往往会***<u>结合</u>***两者来使用

|                | RDB                                          | AOF                                                      |
| -------------- | -------------------------------------------- | -------------------------------------------------------- |
| 持久化方式     | 定时对整个内存做快照                         | 记录每一次执行的命令                                     |
| 数据完整性     | 不完整，两次备份之间会丢失                   | 相对完整，取决于刷盘策略                                 |
| 文件大小       | 会有压缩，文件体积小                         | 记录命令，文件体积很大                                   |
| 宕机恢复速度   | 很快                                         | 慢                                                       |
| 数据恢复优先级 | 低，因为数据完整性不如AOF                    | 高，因为数据完整性更高                                   |
| 系统资源占用   | 高，大量CPU和内存消耗                        | 低，主要是磁盘IO资源，但AOF重写时会占用大量CPU和内存资源 |
| 使用场景       | 可以容忍数分钟的数据丢失，追求更快的启动速度 | 对数据安全性要求较高常见                                 |



## Redis发布订阅

Redis发布订阅(pub/sub)是一种<font color="#FF0000">消息通信模式</font>：发送者(pub)发送消息，订阅者(sub)接收消息。

Redis 客户端可以订阅任意数量的频道。

下图展示了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的关系：

![pubsub1](pubsub1.png)

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户端：

![pubsub2](pubsub2.png)

![pubsub相关命令](image-20210721134621524.png)

**使用场景：**

1. 实时消息系统
2. 实时聊天
3. 订阅

稍微复杂的场景会使用消息中间件MQ

## Redis主从复制

概念

主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点（master/leader），后者称为从节点（slave/follower）；数据的复制是单向的，只能从主节点到从节点。Master以写为主，Slave以读为主。

默认情况下，每台Redis服务器都是主节点；且一个主节点可以有多个从节点（或没有从节点），但一个从节点只能有一个主节点。

主从复制的作用主要包括：

1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余。
3. 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务（即写Redis数据时应用连接主节点，读Redis数据时应用连接从节点），分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高Redis服务器的并发量。
4. 高可用基础：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制是Redis高可用的基础。

一般来说，要将Redis运用于工程项目中，只使用一台Redis是万万不能的，原因如下：

1. 从结构上，单个Redis服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力较大。
2. 从容量上，单个Redis服务器内存容量有限，就算一台Redis服务器内存容量为256G，也不能将所有内存用作Redis存储内存，一般来说，单台Redis最大使用内存应该不超过20G。

```bash
127.0.0.1:6379> info replication
# Replication
role:master # 角色 master
connected_slaves:0 # 没有从机
master_repl_offset:0
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

设置从机

```bash
#设置从机
SLAVEOF ip port #设置哪个redis作为主机
```

使用命令设置主从机只是暂时的，在配置文件中配置是永久的。

![Redis.conf](image-20210721144531883.png)

主机可以写，从机不能写只能读。主机中的所有信息和数据，都会自动被从机保存。

> 复制原理

Slave启动成功连接到master后会发送一个sync同步命令

Master接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集的命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，并完成一次完全同步。

全量复制：slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。

增量复制：master继续将新的所有收集到的修改命令依次传给slave，完成同步。

只要是重新连接master，一次完全同步（全量复制）将被自动执行。

**如果主机断开了连接，我们可以使用`SLAVEOF no one`**让自己变成主机，其他节点就可以手动连接到最新的这个主节点。

## 哨兵模式

自动选择主节点

> 概述

主从切换技术的方法是：当主服务器宕机后，需要手动把一台从服务器切换为主服务器，这就需要人工干预，费时费力，还会造成一段时间内服务不可用。这不是一种推荐的方式，更多时候，需要优先考虑哨兵模式。Redis从2.8开始正式提供了Sentinel（哨兵）架构来解决这个问题。

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，它会独立运行。其原理是**哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例。**

![哨兵模式](11320039-57a77ca2757d0924.png)

![哨兵模式](11320039-3f40b17c0412116c.png)

**故障切换（failover）**的过程：假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行failover过程，仅仅是哨兵1主观的认为主服务器不可用，这个现象成为**主观下线**。当后面的哨兵也检测到主服务器不可用，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行failover操作。切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为**客观下线**。这样对于客户端而言，一切都是透明的。

> 哨兵模式配置项

| 配置项                           | 参数类型                     | 作用                                                         |
| -------------------------------- | ---------------------------- | ------------------------------------------------------------ |
| port                             | 整数                         | 启动哨兵进程端口                                             |
| dir                              | 文件夹目录                   | 哨兵进程服务临时文件夹，默认为/tmp，要保证有可写入的权限     |
| sentinel down-after-milliseconds | <服务名称><毫秒数（整数）>   | 指定哨兵在监控Redis服务时，当Redis服务在一个默认毫秒数内都无法回答时，单个哨兵认为的主观下线时间，默认为30000（30秒） |
| sentinel parallel-syncs          | <服务名称><服务器数（整数）> | 指定可以有多少个Redis服务同步新的主机，一般而言，这个数字越小同步时间越长，而越大，则对网络资源要求越高 |
| sentinel failover-timeout        | <服务名称><毫秒数（整数）>   | 指定故障切换允许的毫秒数，超过这个时间，就认为故障切换失败，默认为3分钟 |
| sentinel notification-script     | <服务名称><脚本路径>         | 指定sentinel检测到该监控的redis实例指向的实例异常时，调用的报警脚本。该配置项可选，比较常用 |

## Redis缓存穿透和雪崩（常用）

Redis缓存的使用，极大地提升了应用程序的性能和效率，特别是数据查询方面。但同时，它也带来了一些问题。其中，最要害的问题，就是数据的一致性问题，从严格意义上讲，这个问题无解。如果对数据的一致性要求很高，那么就不能使用缓存。

另外的一些典型问题就是，缓存穿透和缓存击穿。

### 缓存穿透

> 概念

缓存穿透是指查询一个根本不存在的数据，缓存层和持久层都不会命中。在日常工作中出于容错的考虑，如果从持久层查不到数据则不写入缓存层，缓存穿透将导致不存在的数据每次请求都要到持久层去查询，失去了缓存保护后端持久的意义。

![缓存穿透](缓存穿透示意图.png)

> 解决方案

1.**布隆过滤器**

布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行校验，不符合则丢弃，从而避免了对底层存储系统的查询压力。

2.**缓存空对象**

缓存空对象：是指在持久层没有命中的情况下，对key进行set （key,null）

这样做会存在两个问题：

1. value为null 不代表不占用内存空间，空值做了缓存，意味着缓存层中存了更多的键，需要更多的内存空间，比较有效的方法是针对这类数据设置一个较短的过期时间，让其自动剔除。

2. 缓存层和存储层的数据会有一段时间窗口的不一致，可能会对业务有一定影响。例如过期时间设置为5分钟，如果此时存储层添加了这个数据，那此段时间就会出现缓存层和存储层数据的不一致，此时可以利用消息系统或者其他方式清除掉缓存层中的空对象。

### 缓存击穿

> 概念

- 当前key是一个热点key（例如一个秒杀活动），并发量非常大。
- 重建缓存不能在短时间完成，可能是一个复杂计算，例如复杂的SQL、多次IO、多个依赖等。

在缓存失效的瞬间，有大量线程来重建缓存，造成后端负载加大，甚至可能会让应用崩溃。

> 解决方案

1.**永不过期**

- 从缓存层面来看，确实没有设置过期时间，所以不会出现热点key过期后产生的问题，也就是“物理”不过期。
- 从功能层面来看，为每个value设置一个逻辑过期时间，当发现超过逻辑过期时间后，会使用单独的线程去更新缓存

2.**加互斥锁**

只允许一个线程重建缓存，其他线程等待重建缓存的线程执行完，重新从缓存获取数据即可。set(key,value,timeout)



### 缓存雪崩

> 概念

由于缓存层承载着大量请求，有效地保护了存储层，但是如果缓存层由于某些原因不可用（宕机）或者大量缓存由于超时时间相同在同一时间段失效（大批key失效/热点数据失效），大量请求直接到达存储层，存储层压力过大导致系统雪崩。

> 解决方案

+ 可以把缓存层设计成高可用的，即使个别节点、个别机器、甚至是机房宕掉，依然可以提供服务。利用sentinel或cluster实现。
+ 采用多级缓存，本地进程作为一级缓存，redis作为二级缓存，不同级别的缓存设置的超时时间不同，即使某级缓存过期了，也有其他级别缓存兜底
+ 缓存的过期时间用随机值，尽量让不同的key的过期时间不同（例如：定时任务新建大批量key，设置的过期时间相同）



## Redis内存淘汰策略（常用）

noeviction：当内存使用达到阈值的时候，所有引起申请内存的命令会报错。
allkeys-lru：在主键空间中，优先移除最近未使用的key。(推荐)
volatile-lru：在设置了过期时间的键空间中，优先移除最近未使用的key。
allkeys-random：在主键空间中，随机移除某个key。
volatile-random：在设置了过期时间的键空间中，随机移除某个key。
volatile-ttl：在设置了过期时间的键空间中，具有更早过期时间的key优先移除。



## 基于List结构模拟消息队列

Redis的list数据结构是一个双向链表，很容易模拟出队列效果。

队列是入口和出口不在一边，因此可以利用：LPUSH结合RPOP、或者RPUSH结合LPOP来实现。

不过需要注意的是，当队列中没有消息时RPOP或LPOP操作返回null，并不像JVM的阻塞队列那样会阻塞并等待消息。

因此应该使用`BRPOP`或者`BLPOP`来实现阻塞效果。

优点：

- 利用Redis存储，不受限于JVM内存上限
- 基于Redis的持久化机制，数据安全性有保证
- 可以满足消息有序性

缺点：

- 无法避免消息丢失
- 只支持单消费者



## 基于PubSub的消息队列

PubSub（发布订阅）是Redis2.0版本引入的消息传递模型。顾名思义，消费者可以订阅一个或多个channel，生产者向对应channel发送消息后，所有订阅者都能收到相关消息。

- SUBSCRIBE channel [channel]：订阅一个或多个频道
- PUBLIC channel msg：向一个频道发送消息
- PSUBSCRIBE pattern [pattern]：订阅与pattern格式匹配的所有频道



基于PubSub的消息队列有哪些优缺点？

优点：

- 采用发布订阅模型，支持多生产、多消费

缺点：

- 不支持数据持久化
- 无法避免消息丢失
- 消息堆积有上限，超出时数据丢失



## 基于Stream的消息队列

Stream是Redis5.0引入的一种***<u>新数据类型</u>***（意味着可以被持久化），可以实现一个功能非常完善的消息队列。

发送消息命令：XADD

读取消息的方式一：XREAD

XREAD阻塞方式，读取最新的消息：

```bash
XREAD COUNT 1 BLOCK STREAMS users $
```

注意：当我们指定启示ID为$时，代表读取最新的消息，如果我们处理一条消息的过程中，又有超过1条以上的消息到达队列，则下次获取时也只能获取到最新的一条，会出现`漏读消息`的问题。



STREAM类型消息队列的XREAD命令特点：

- 消息可回溯
- 一个消息可以被多个消费者读取
- 可以阻塞读取
- 有消息漏读的风险



基于Stream的消息队列-消费者组

消费者组（Consumer Group）：将多个消费者划分到一个组中，监听同一个队列。具备下列特点：

- 消息分流：队列中的消息会分流给组内的不同消费者，而不是重复消费，从而加快消息处理的速度。
- 消息标示：消费者组会维护一个标示，记录最后一个被处理的消息，哪怕消费者宕机重启，还会从标示之后读取消息。确保每一个消息都会被消费。

- 消息确认：消费者获取消息后，消息处于pending状态，并存入一个pending-list。当处理完成后需要通过XACK来确认消息，标记消息为已处理，才会从pending-list移除。

创建消费者组：

XGROUP CREATE key groupName ID [MKSTREAM]

- key：队列名称
- groupName：消费者组名称
- ID：起始ID标示，$代表队列中最后一个消息，0则代表队列中第一个消息。
- MKSTREAM：队列不存在时自动创建队列。

其他常见命令：

```bash
#删除指定的消费者组
XGROUP DESTORY key groupName

#给指定的消费者组添加消费者
XGROUP CREATECONSUMER key groupname consumername

#删除消费者组中的指定消费者
XGROUP DELCONSUMER key groupname consumername
```

从消费者组读取消息：
```bash
XREADGROUP GROUP group consumer [COUNT count] [BLOCK milliseconds] [NOACK] STREAMS key [key ...] ID [ID ...]
```

- group：消费组名称
- consumer：消费者名称，如果消费者不存在，会自动创建一个消费者
- count：本次查询的最大数量

- BLOCK milliseconds：当没有消息时最长等待时间
- NOACK：无需手动ACK，获取到消息后自动确认
- STREAMS key：指定队列名称
- ID：获取消息的起始ID：
  - ">"：从下一个未消费的消息开始
  - 其他：根据指定id从pending-list中获取已消费但未确认的消息，例如0，是从pending-list中的第一个消息开始。



Redis消息队列

|              | List                                     | PubSub             | Stream                                                 |
| ------------ | ---------------------------------------- | ------------------ | ------------------------------------------------------ |
| 消息持久化   | 支持                                     | 不支持             | 支持                                                   |
| 阻塞读取     | 支持                                     | 支持               | 支持                                                   |
| 消息堆积处理 | 受限于内存空间，可以利用多消费者加快处理 | 受限于消费者缓冲区 | 受限于队列长度，可以利用消费者组提高消费速度，减少堆积 |
| 消息确认机制 | 不支持                                   | 不支持             | 支持                                                   |
| 消息回溯     | 不支持                                   | 不支持             | 支持                                                   |

