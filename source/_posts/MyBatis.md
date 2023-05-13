---
title: MyBatis
date: 2021-08-06 19:19:16
tags: ORM框架
categories: 学习笔记
index_img: /img/mybatis-logo.png
---

# MyBatis

[mybatis – MyBatis 3 | 简介](https://mybatis.org/mybatis-3/zh/)

> XML映射文件

## SQL映射文件的顶级元素

| 元素      | 作用                             |
| --------- | -------------------------------- |
| cache     | 该命名空间的缓存配置             |
| cache-ref | 引用其他命名空间的缓存配置       |
| resultMap | 描述如何从数据库结果集中加载对象 |
| sql       | 可被其他语句引用的可重用语句块   |
| insert    | 映射插入语句                     |
| update    | 映射更新语句                     |
| delete    | 映射删除语句                     |
| select    | 映射查询语句                     |

## CRUD

### 1.namespace

namespace中的包名要和Dao/Mapper接口的包名一致。

### 2.select

选择，查询语句



```xml
<select id="selectPerson" parameterType="int" resultType="hashmap">
  SELECT * FROM PERSON WHERE ID = #{id}
</select>
```

+ id：语句名(selectPerson)，对应namespace中的方法名

+ parameterType：接受参数的类型（int）

+ resultType：返回对象的类型

### 3.insert

```xml
<insert id="insertAuthor">
  insert into Author (id,username,password,email,bio)
  values (#{id},#{username},#{password},#{email},#{bio})
</insert>

```

### 4.update

```xml
<update id="updateAuthor">
  update Author set
    username = #{username},
    password = #{password},
    email = #{email},
    bio = #{bio}
  where id = #{id}
</update>
```

### 5.delete

```xml
<delete id="deleteAuthor">
  delete from Author where id = #{id}
</delete>
```

## 配置解析

## ResultMap

> 解决属性名和字段名不一致的问题

结果集映射[mybatis – MyBatis 3 | XML 映射器](https://mybatis.org/mybatis-3/zh/sqlmap-xml.html#Result_Maps)

## 日志

## 分页

limit

## 动态SQL

[mybatis – MyBatis 3 | 动态 SQL](https://mybatis.org/mybatis-3/zh/dynamic-sql.html)

```sql
CREATE TABLE `blog`(
`id` VARCHAR(50) NOT NULL COMMENT 博客id,
`title` VARCHAR(100) NOT NULL COMMENT 博客标题,
`author` VARCHAR(30) NOT NULL COMMENT 博客作者,
`create_time` DATETIME NOT NULL COMMENT 创建时间,
`views` INT(30) NOT NULL COMMENT 浏览量
)ENGINE=INNODB DEFAULT CHARSET=utf8
```

### \<if>

### \<choose>(when, otherwise)

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

类似 Java 中的 switch 语句。

### \<where>

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

***where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除。**

### \<set>

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

***set* 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）。**

### \<trim>(where, set)

通过自定义 trim 元素来定制 *where* 元素和*set*元素的功能。

和 *where* 元素等价的自定义 trim 元素为：

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```

与 *set* 元素等价的自定义 *trim* 元素为：

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

### \<foreach>

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

## Mybatis缓存

[mybatis – MyBatis 3 | XML 映射器](https://mybatis.org/mybatis-3/zh/sqlmap-xml.html#cache)

+ MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大地提升查询效率。
+ MyBatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**
  + 默认情况下，只有一级缓存开启。（sqlSession级别的缓存，也称为本地缓存）
  + 二级缓存需要手动开启和配置，是基于namespace级别的缓存。
  + 为了提高扩展性，MyBatis定义了缓存接口Cache。可以通过实现Cache接口来自定义二级缓存。

### 一级缓存

+ 一级缓存也叫本地缓存：
  + 与数据库同一次会话期间查询到的数据会放在本地缓存中。
  + 以后如果需要获取相同的数据，直接从缓存中拿，不必再去查数据库。



小结：一级缓存默认是开启的，只在一次SqlSession中有效，也就是拿到连接到关闭连接这个区间段。一级缓存就是一个Map。

### 二级缓存

+ 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存。
+ 基于namespace级别的缓存，一个命名空间对应一个二级缓存。
+ 工作机制
  + 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
  + 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；
  + 新的会话查询信息，就可以从二级缓存中获取内容；
  + 不同的mapper查出的数据会放在自己对应的缓存中。

```
- 映射语句文件中的所有 select 语句的结果将会被缓存。
- 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
- 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
- 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
- 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
- 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。
```

小结：

+ 只要开启了二级缓存，在同一个Mapper下就有效
+ 所有的数据都会先放在一级缓存中
+ 只有当会话提交，或者关闭的时候，才会提交到二级缓存中。

