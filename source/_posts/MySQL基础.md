---
title: MySQL基础
date: 2021-08-06 18:53:42
tags: SQL
categories: 学习笔记
index_img: /img/mysql_logo.jpg
---

MySQL基础

```sql
mysql -uroot -p; -- 连接数据库

flush privileges; -- 刷新权限

show databases; -- 查看所有的数据库

use 数据库名; -- 切换数据库

show tables; -- 查看所有的表

describe/desc 数据表; -- 显示表信息

create database 数据库名; -- 创建数据库

exit; -- 退出连接
```

数据库XXX语言

DDL	定义

DML	操作

DQL	查询

DCL	控制

## 1.操作数据库

操作数据库>操作数据库中的表>操作数据库中表的数据

<font color="FF0000">mysql关键字不区分大小写</font>

### 操作数据库

1.创建数据库

```sql
CREATE DATABASE [IF NOT EXISTS] 数据库名;
```

2.删除数据库

```sql
DROP DATABASE [IF EXISTS] 数据库名;
```

3.使用数据库

```sql
-- tab键上面，如果表名或字段名是一个特殊字符，就需要带``
USE `school`
```

4.查看数据库

```sql
SHOW DATABASES -- 查看所有的数据库
```

### 数据库的列类型

> 数值

+ tinyint	       十分小的数据	     1个字节
+ smallint        较小的数据            2个字节
+ mediumint   中等大小的数据    3个字节
+ int                 标准的整数            4个字节（常用）
+ bigint             较大的数据           8个字节
+ float               浮点数                   4个字节
+ double           浮点数                   8个字节
+ decimal          字符串形式的浮点数     金融计算的时候一般使用decimal

> 字符串

+ char              字符串固定大小     0-255**字符**
+ varchar         可变字符串             0-65535字节（常用）
+ tinytext          微型文本                2^8 - 1（255字节）
+ text                 文本串                   2^16 - 1（65535字节）

>时间日期

java.util.Date

+ date          YYYY-MM-DD，日期格式
+ time          HH:mm:ss，时间格式
+ datetime   YYYY-MM-DD  HH:mm:ss，最常用的时间格式
+ timestamp   时间戳，1970.1.1到现在的毫秒数，也较为常用
+ year            年份

> null

+ 没有值，未知
+ <font color="FF0000">不要使用NULL进行运算，结果为NULL</font>

### 数据库的字段属性（重点）

**Unsigned：**

+ 无符号的整数
+ 声明了该列不能声明为负数

**zerofill：**

+ 0填充的
+ 不足的位数，使用0来填充，int(3)，5 -> 005

**自增：**

+ 通常理解为自增，自动在上一条记录的基础上+1
+ 通常用来设计唯一的主键 index，必须是整数类型
+ 可以自定义设计主键自增的起始值和步长

**非空 not null：**

+ 设置为not null，如果不给它赋值，就会报错
+ NULL，如果不填写值，默认就是null

**默认：**

+ 设置默认的值

### 创建数据表

```sql
CREATE TABLE [IF NOT EXISTS] `表名`(
	`字段名` 列类型 [属性] [索引] [注释],
    `字段名` 列类型 [属性] [索引] [注释],
    ...
    `字段名` 列类型 [属性] [索引] [注释],
)[表类型][字符集设置][注释]
```

### 数据表类型

数据库引擎

INNODB

MYISAM 早些年使用

|              | MYISAM | INNODB        |
| ------------ | ------ | ------------- |
| 事务支持     | 不支持 | 支持          |
| 数据行锁定   | 不支持 | 支持          |
| 外键约束     | 不支持 | 支持          |
| 全文索引     | 支持   | 不支持        |
| 表空间的大小 | 较小   | 较大，约为2倍 |

常规使用操作：

+ MYISAM	节约空间，速度较快
+ INNODB   安全性高，事务的处理，多表用户操作

> 在物理空间存在的位置（了解）

所有的数据库文件都存在data目录下

本质还是文件的存储

MySQL引擎在物理文件上的区别

+ InnoDB在数据表中只有一个*.frm文件，以及上级目录下的ibdata1文件
+ MYISAM对应的文件
  + *.frm - 表结构的定义文件
  + *.MYD 数据文件(data)
  + *.MYI   索引文件(index)

> 修改表

```sql
-- 修改表名 ALTER TABLE 旧表名RENAME AS 新表名
ALTER TABLE teacher RENAME AS teacher1
-- 增加表的字段 ALTER TABLE 表名 ADD 字段名 列属性
ALTER TABLE teacher1 ADD age INT(11)
-- 修改表的字段（重命名，修改约束）
ALTER TABLE teacher1 MODIFY age VARCHAR(11) -- 修改约束
ALTER TABLE teacher1 CHANGE age age1 INT(1) -- 字段重命名

-- 删除表的字段
ALTER TABLE teacher1 DROP age1


```

> 删除

```sql
-- 删除表
DROP TABLE [IF EXISTS] teacher1
```

所有的创建和删除操作尽量加上判断，以免报错。

## 2.MySQL数据管理

### 外键



### DML语言

数据库意义：数据存储，数据管理

DML语言：数据操作语言

+ insert
+ update
+ delete

### 添加

```sql
-- 插入语句（添加）
insert into 表名([字段名1,字段2,字段3]) values('值1','值2','值3')
```

注意事项：

1. 字段和字段之间使用英文逗号隔开
2. 字段可以省略，但是后面的值必须要一一对应，不能少
3. 可以同时插入多条数据，values后面的值，需要使用,隔开

### 修改

```sql
UPDATE 表名 set colnum_name = value,[colnum_name = value,...] where 条件
```

+ colnum_name是数据库的列，尽量带上``
+ 筛选的条件，如果没有指定，则会修改所有的列
+ value，是一个具体的值，也可以是一个变量
+ 多个设置的属性之间，使用英文逗号隔开

### 删除

> delete命令

语法：`delete from 表名 [where 条件]`

```sql
-- 删除数据（避免这样写，会全部删除）
DELETE FROM `student`;

-- 删除指定数据
DELETE FROM `student` WHERE id = 1;
```



> TRUNCATE命令

作用：完全清空一个数据表，表的结构和索引约束不会变

```sql
-- 清空 student 表
TRUNCATE `student`
```



> delete与TRUNCATE区别

+ 相同点：都能删除数据，都不会删除表结构
+ 不同点：
  + TRUNCATE 重新设置自增列，计数器会归零
  + TRUNCATE 不会影响事务

## 3.DQL查询数据（重点）

（Data Query Language）数据查询语言

SELECT完整语法

```sql
SELECT [ALL | DISTINCT]
{* | table.* | [table.field[as alias1][,table.field2[as alias2]][,...]]}
FROM table_name [as table_allias]
	[left | right | inner join table_name2] -- 联合查询
	[WHERE ...]	-- 指定结果需要满足的条件
	[GROUP BY ...] -- 指定结果按照哪个字段来分组
	[HAVING] -- 过滤分组的记录必须满足的次要条件
	[ORDER BY] -- 指定查询记录按一个或多个条件排序
	[limit {[offset,]row_count} | row_countOFFSET offset()};
     -- 查询记录的记录从哪至哪条
```



### 指定查询字段

```sql
-- 查询全部的学生
SELECT * FROM student

-- 查询指定字段
SELECT `StudentNo`,`StudentName` FROM student

-- 别名，给结果起一个名字 AS 可以给字段起别名，也可以给表起别名
-- 字段名 AS 别名
SELECT `StudentNo` AS 学号,`StudentName` AS 学生姓名 FROM student AS s

-- 函数 Concat(a,b)
SELECT CONCAT('姓名：',StudentNmae) AS 新名字 FROM Student

```

> 去重 distinct

作用：去除SELECT查询出来的结果中重复的数据，重复的数据只显示一条。

```sql
-- 查询全部的考试成绩
SELECT * FROM result
-- 查询哪些同学参加了考试
SELECT `StudentNo` FROM result
-- 发现重复数据，去重
SELECT DISTINCT `StudentNo` FROM result
```

数据库中的表达式：文本值，列，Null，函数，计算表达式，系统变量...

### where条件子句

作用：检索数据中符合条件的值

> 逻辑运算符

| 运算符    | 语法                       | 描述                             |
| --------- | -------------------------- | -------------------------------- |
| and    && | a and b          a&&b      | 逻辑与，两个都为真，结果为真     |
| or  \|\|  | a or b            a \|\| b | 逻辑或，其中一个为真，则结果为真 |
| Not    !  | not a               !a     | 逻辑非，真为假，假为真           |

> 模糊查询

| 运算符      | 语法              | 描述                                           |
| ----------- | ----------------- | ---------------------------------------------- |
| IS NULL     | a is null         | 如果操作符为NULL，结果为真                     |
| IS NOT NULL | a is not null     | 如果操作符不为null，结果为真                   |
| BETWEEN     | a between b and c | 若a在b和c之间，则结果为真                      |
| **Like**    | a like b          | SQL匹配，如果a匹配b，则结果为真                |
| **In**      | a in (a1,a2,a3)   | 假设a在a1，或者a2...其中的某一个值中，结果为真 |

```sql
-- 查询姓刘的同学
-- like结合	%(代表0到任意个字符)	_(代表一个字符)
SELECT `StudentNo`,`StudentName` FROM `student`
WHERE StudentName LIKE `刘%`;

-- 查询姓刘的同学，名字后面只有一个字的
SELECT `StudentNo`,`StudentName` FROM `student`
WHERE StudentName LIKE `刘_`;

-- 查询名字中间有嘉字的同学 %嘉%
SELECT `StudentNo`,`StudentName` FROM `student`
WHERE StudentName LIKE '%嘉%';

-- in
-- 查询1001，1002，1003号学员
SELECT `StudentNo`, `StudentName` FROM `student`
WHERE StudentNo IN (1001,1002,1003);

-- 查询在北京的学生
SELECT `StudentNo`,`StudentName` FROM `student`
WHERE `Address` IN ('安徽','河南洛阳');

-- 查询地址为空的学生
SELECT `StudentNo`,`StudentName` FROM `student`
WHERE address='' OR address IS NULL

-- 查询有出生日期的同学
SELECT `StudentNo`,`StudentName` FROM `student`
WHERE `BornDate` IS NOT NULL
```

### 联表查询

> Join

![联表查询](20181103160140252.png)

MySQL不支持full outer join

```sql
SELECT s.studentNO,studentName,SubjectNo,StudentResult
FROM student AS s -- AS 可以省略
INNER JOIN result AS r
WHERE s.studentNO = r.studentNO;

-- Right Join
SELECT s.studentNO,studentName,SubjectNo,StudentResult
FROM student s
RIGHT JOIN result r
ON s.studentNO = r.studentNO; -- 可以用on
```

> 自连接

自己的表和自己的表连接

### 分页和排序

> 排序

```sql
-- 排序：升序asc, 降序desc
-- ORDER BY 通过哪个字段排序，怎么排
-- 查询的结果根据 成绩降序 排序
SELECT s.`StudentNo`, `StudentName`, `SubjectName`, `SubjectResult`
FROM student s
INNER JOIN `result` r
ON s.StudentNo = r.StudentNo
INNER JOIN `subject` sub
ON r.`SubjectNo` = sub.`SubjectNo`
WHERE subjectName = `数据结构-1`
ORDER BY StudentResult ASC
```

> 分页

```sql
-- 分页
-- 语法：limit 起始值/偏移量，页面的大小
SELECT s.`StudentNo`, `StudentName`, `SubjectName`, `SubjectResult`
FROM student s
INNER JOIN `result` r
ON s.StudentNo = r.StudentNo
INNER JOIN `subject` sub
ON r.`SubjectNo` = sub.`SubjectNo`
WHERE subjectName = `数据结构-1`
ORDER BY StudentResult ASC
LIMIT 0,5

-- 第一页 limit 0,5    (1-1)*5
-- 第二页 limit 5,5    (2-1)*5
-- 第三页 limit 10,5	(3-1)*5
-- 第N页  limit        (n-1)*pageSize,pageSize
-- 【pageSize：页面大小】
-- 【(n-1)*pageSize：起始值】
-- 【n：当前页】
-- 【数据总数/页面大小 = 总页数】
```

语法：`limit(查询起始下标，pageSize)`

### 子查询

本质：在where语句中嵌套一个子查询语句

where (select * from ...)

```sql
-- 查询 数据库结构-1 的所有考试结果（学号，科目编号，成绩），降序排列
-- 方式一：使用连接查询
SELECT `StudentNo`,r.`SubjectNo`,`StudentResult`
FROM `result` r
INNER JOIN `subject` sub
ON r.SubjectNo = sub.SubjectNo
WHERE SubjectName = `数据库结构-1`
ORDER BY StudentResult DESC

-- 方式二：使用子查询
SELECT `StudentNo`,`SubjectNo`,`StudentResult`
FROM `result`
WHERE SubjectNo = (
	SELECT SubjectNo FROM `subject`
    WHERE SubjectName = `数据库结构-1`
)
ORDER BY StudentResult DESC
```

## 4.MySQL函数

### 常用函数

```sql
-- 数学运算
SELECT ABS(-8) -- 绝对值
SELECT CEILING(9.4) -- 向上取整
SELECT FLOOR(9.4) -- 向下取整
SELECT RAND() -- 返回一个0~1之间的随机数
SELECT SIGN(-10) -- 判断一个数的符号 0-0 负数返回-1，正数返回1

-- 字符串函数
SELECT CHAR_LENGTH('即使再小的帆也能远航') -- 字符串长度
SELECT CONCAT('我','爱','coding') -- 拼接字符串
SELECT INSERT('我爱coding',1,2,"热爱") -- 查询，替换
SELECT LOWER('CZh') -- 小写
SELECT UPPER('CZh') -- 大写
SELECT INSTR('CZh', 'h') -- 返回第一次出现的子串的索引
SELECT REPLACE('坚持就能成功','坚持','努力') -- 替换出现的指定字符串
SELECT SUBSTR('坚持就能成功',1,3) -- 返回指定的子字符串(源字符串，截取的位置，截取的长度)
SELECT REVERSE('坚持就能成功') -- 反转

-- 时间和日期函数（记住）
SELECT CURRENT_DATE() -- 获取当前日期
SELECT CURDATE() -- 获取当前日期
SELECT NOW() -- 获取当前时间
SELECT LOCALTIME() -- 本地时间
SELECT SYSDATE() -- 系统时间

SELECT YEAR(NOW())
SELECT MONTH(NOW())
SELECT DAY(NOW())
SELECT HOUR(NOW())
SELECT MINUTE(NOW())
SELECT SECOND(NOW())

-- 系统
SELECT SYSTEM_USER()
SELECT USER()
SELECT VERSION()
```

### 聚合函数

| 函数名称 | 描述   |
| -------- | ------ |
| COUNT()  | 计数   |
| SUM()    | 求和   |
| AVG()    | 平均值 |
| MAX()    | 最大值 |
| MIN()    | 最小值 |
| ...      | ...    |

```sql
SELECT COUNT(studentname) FROM student; -- Count(指定列)，会忽略所有的null值
SELECT COUNT(*) FROM student; -- Count(*)，不会忽略null值
SELECT COUNT(1) FROM result;  -- Count(1)，不会忽略所有的null值，本质：计算行数

SELECT SUM(`StudentResult`) AS 总和 FROM result
SELECT AVG(`StudentResult`) AS 平均分 FROM result
SELECT MAX(`StudentResult`) AS 最高分 FROM result
SELECT MIN(`StudentResult`) AS 最低分 FROM result

-- 查询不同课程的平均分，最高分，最低分，平均分大于80
-- 核心：根据不同的课程分组
SELECT SubjectName,AVG(StudentResult) AS 平均分,MAX(StudentResult) AS 最高分,MIN(StudentResult) AS 最低分
FROM result r
INNER JOIN `suject` sub
ON r.`SubjectNo` = sub.`SubjectNo`
GROUP BY r.SubjectNo -- 通过什么字段来分组
HAVING 平均分>80
```

### 数据库级别的MD5加密

```sql
-- 加密
UPDATE testmd5 SET pwd=MD5(pwd) WHERE id = 1;
UPDATE testmd5 SET pwd=MD5(pwd) -- 加密全部的密码

-- 插入的时候加密
INSERT INTO testmd5 VALUES(4, 'xiaoming', MD5('123456'))

-- 如何校验：将用户传递进来的密码，进行md5加密，然后对比加密后的值
SELECT * FROM testmd5 WHERE `name` = 'xiaoming' AND pwd=MD5('123456')
```

## 5.事务

> 事务原则：ACID原则 原子性、一致性、隔离性、持久性

**原子性（Atomicity）**

要么都成功，要么都失败

**一致性（Consistency）**

事务前后的数据完整性要保持一致

**隔离性（Isolation）**

事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰

**持久性（Durability）**

事务一旦提交则不可逆，被持久化到数据库中

> 隔离所导致的一些问题

**脏读：**

指一个事务读取了另一个事务未提交的数据。

**不可重复读：**

在一个事务内读取表中的某一行数据，多次读取结果不同。

**虚读（幻读）：**

是指在一个事务内读取到了别的事务插入的数据，导致前后读取不一致。

```sql
-- MySQL默认开启事务自动提交
SET autocommit = 0 /* 关闭 */
SET autocommit = 1 /* 开启（默认的）*/

-- 手动处理事务

-- 事务开启
START TRANSACTION -- 标记一个事务的开始，从这个之后的sql都在同一个事务内

-- 提交：持久化，数据已提交就不能回滚了
COMMIT
-- 回滚：回到原来的样子
ROLLBACK

-- 事务结束


SAVEPOINT 保存点名  -- 设置一个事务的保存点
ROLLBACK TO SAVEPOINT 保存点名 -- 回滚到保存点
RELEASE SAVEPOINT 保存点名 -- 释放保存点
```

## 6.索引

> MySQL官方对索引的定义为：**索引（index）是帮助MySQL高效获取数据的数据结构。**
>
> 提取句子主干，就可以得到索引的本质：**索引是数据结构**

### 索引的分类

+ 主键索引（PRIMARY KEY）
  + 唯一的标识，主键不可重复，只能有一个列作为主键
+ 唯一索引（UNIQUE KEY）
  + 索引列的值必须唯一，唯一索引可以重复，多个列都可以标识为唯一索引
+ 普通索引（KEY/INDEX）
  + 默认的，index，key关键字来设置
+ 全文索引（FullText）
  + 在特定的数据库引擎下才有，如MyISAM
  + 快速定位数据

```sql
-- 索引的使用
-- 1.在创建表的时候给字段增加索引
-- 2.创建完毕后，增加索引

-- 显示所有的索引信息
SHOW INDEX FROM student

-- 增加一个全文索引（索引名）列名
ALTER TABLE school.student ADD FULLTEXT INDEX `studentName`(`studentName`);

-- EXPLAIN分析sql执行的状况
EXPLAIN SELECT * FROM student; -- 非全文索引

```

> 测试索引

```sql
-- 插入100万条数据
DELIMITER $$ -- 写函数前必须要写
CREATE FUNCTION mock_data()
RETURNS INT
BEGIN
	DECLARE num INT DEFAULT 1000000;
	DECLARE i INT DEFAULT 0;
	
	WHERE i<num DO
		-- 插入语句
		INSERT INTO...
		SET i = i + 1;
	END WHILE
END;
SELECT mock_data()
```

### 索引原则

+ 索引不是越多越好
+ 不要对经常变动的数据加索引
+ 小数据量的表不需要加索引
+ 索引一般加在常用来查询的字段上

> 索引的数据结构

Btree：InnoDB的默认数据结构

[CodingLabs - MySQL索引背后的数据结构及算法原理](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)

[深入理解 Mysql 索引底层原理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/113917726)

索引最终选择B+树的原因:

1. hash很快，但每次IO只能取一个数

2. AVL和红黑树，在大量数据的情况下，IO操作还是太多

3. B树每个节点内存储的是数据，因此每个节点存储的分支太少
4. B+节点存储的是索引+指针(引用指向下一个节点)，可以存储大量索引，同时最终数据存储在叶子节点，并且有引用横向链接，可以在2-3次的IO操作内完成千万级别的表操作。
5. 建议索引是是自增长数字，这样适合范围查找

[聚簇索引与非聚簇索引（也叫二级索引） - 简书 (jianshu.com)](https://www.jianshu.com/p/fa8192853184)

[索引知识系列二：联合索引、索引覆盖和索引下推详解_程序编织梦想的博客-CSDN博客_索引覆盖和索引下推](https://blog.csdn.net/mffandxx/article/details/111916252)

## 权限管理和备份

### 用户管理

```sql
-- 创建用户
CREATE USER 用户名 IDENTIFIED BY '密码';

-- 修改密码（修改当前用户密码）
SET PASSWORD = PASSWORD('123456');

-- 修改密码（修改指定用户密码）
SET PASSWORD FOR 用户名 = PASSWORD('123456')

-- 重命名
RENAME USER 原名 TO 新名字

-- 用户授权
-- ALL PRIVILEGES除了给别人授权，其他都能干
GRANT ALL PRIVILEGES ON *.* TO 用户名

-- 查询权限
SHOW GRANTS FOR 用户名 -- 查看指定用户的权限

-- 撤销权限
REVOKE ALL PRIVILEGES ON *.* FROM 用户名

```

### MySQL备份

命令行导出

```sql
# mysqldump -h 主机 -u 用户名 -p 密码 数据库 表名 > 物理磁盘位置
mysqldump -hlocalhost -uroot -p123456 school student >D:/a.sql

# 导入
# 登录的情况下，切换到指定的数据库
source d:/a.sql

```



## 数据库的归约，三大范式



## SQL注入



## EXPLAIN

[MySQL ：： MySQL 5.7 参考手册 ：： 8.8.2 EXPLAIN 输出格式](https://dev.mysql.com/doc/refman/5.7/en/explain-output.html)

> 使用EXPLAIN关键字可以模拟优化器执行SQL查询语句，从而知道MySQL是如何处理你的SQL语句的。分析你的查询语句或表结构的性能瓶颈。

用法：explain + sql语句

能够知道：

+ 表的读取顺序
+ 数据读取操作的操作类型
+ 哪些索引可以被使用
+ 哪些索引被实际使用
+ 表之间的引用
+ 每张表有多少行被优化器查询

表包含的字段如下：

![explain输出字段](image-20210715114955960.png)

### id

> select查询的序列号，包含一组数字，表示查询中执行select子句或操作表的顺序

三种情况：

+ id相同，执行顺序由上至下
+ id不同，如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行。
+ id相同与不同同时存在，id相同的从上往下循序执行，id越大，优先级越高，越先执行。

### select_type

+ **SIMPLE**：简单的select查询，查询中不包含子查询或者UNION。
+ **PRIMARY**：查询中若包含任何复杂的子查询，最外层查询则标记为PRIMARY。
+ **SUBQUERY**：在select或where列表中包含的子查询。
+ **DERIVED**：在FROM列表中包含的子查询被标记为DERIVED(衍生)，MySQL会递归执行这些子查询，把结果放在临时表里。
+ **UNION**：若第二个SELECT出现在UNION之后，则被标记为UNION；若UNION包含在FROM子句的子查询中，外层SELECT将被标记为：DERIVED。
+ **UNION RESULT**：从UNION表获取结果的SELECT。

### table 

显示这一行的数据是关于哪张表的

### type

访问类型排列

显示查询使用了何种类型

<font color='FF0000'>从最好到最差依次是：</font>

system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL

简化如下：

system->const->eq_ref>ref>range>index>ALL

+ **system**：表只有一行记录（等于系统表），这是const类型的特例，平时不会出现，可忽略不计
+ **const**：表示通过索引一次就找到了，const用于比较primary key或者unique索引。因为只匹配一行数据，所以很快。如将主键置于where列表中，MySQL就能将该查询转换为一个常量。
+ **eq_ref**：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描。

+ 

### possible_keys

提示使用哪个索引会在该表中找到行，不太重要

### key

MYSQL使用的索引，简单且重要

### key_len

MYSQL使用的索引长度

### ref

ref列显示使用哪个列或常数与key一起从表中选择行。

### rows

显示MYSQL执行查询的行数，简单且重要，数值越大越不好，说明没有用好索引

### filtered

按表条件筛选的行的百分比

### Extra

该列包含MySQL解决查询的详细信息。



## MVCC(多版本并发控制)

[MySQL ：： MySQL 8.0 参考手册 ：： 15.3 InnoDB 多版本控制](https://dev.mysql.com/doc/refman/8.0/en/innodb-multi-versioning.html)（官方文档，推荐）

[Mysql中的MVCC - 简书 (jianshu.com)](https://www.jianshu.com/p/654773e14c38)

[MySQL，MVCC详解，快照读在RC、RR下的区别_小道仙97的博客-CSDN博客_mysql rc 快照读](https://blog.csdn.net/Tomwildboar/article/details/121064240)





[招银面试官，听说你精通 MySQL，我们来大战 66 回合 (qq.com)](https://mp.weixin.qq.com/s/JFjFs_7xduCmHOegbJ-Gbg)（有某些错误）

## char与varchar的区别

[闲谈MySQL的char和varchar](https://blog.csdn.net/bigwood99/article/details/114981705)

[MySQL :: MySQL 8.0 Reference Manual :: 11.3.2 The CHAR and VARCHAR Types](https://dev.mysql.com/doc/refman/8.0/en/char.html)

### char

- char 表示定长字符串，长度是固定的；
- 如果插入数据的长度小于 char 的固定长度时，则用空格填充；
- 因为长度固定，所以存取速度要比 varchar 快很多，甚至能快 50%，但正因为其长度固定，所以会占据多余的空间，是空间换时间的做法；
- 对于 char 来说，最多能存放的字符个数为 255，和编码无关

### varchar

- varchar 表示可变长字符串，长度是可变的；
- 插入的数据是多长，就按照多长来存储；
- varchar 在存取方面与 char 相反，它存取慢，因为长度不固定，但正因如此，不占据多余的空间，是时间换空间的做法；
- 对于 varchar 来说，最多能存放的字符个数为 65535 ，但这个值是理论值，实际比这个少，因为字符开头会使用1-2个字节记录字符串长度



### **重要**

一般认为，char是定长字符，varchar是可变长度字符。

在MySQL版本4.12以后，这种说法就不完全正确了。在4.12版之后，char(n)中的n代表的不再是字节长度，而是字符长度。在这个改变下，如果字符集选择的是多字节字符集，比如utf-8，那么char(n)则存储的数据就不是固定长度了。

例如，char(3)可以存储“abc”、“一二三”。显然，前面两个字符串的字节长度是不一样的。

所以只有当字符集选择了单字节字符集，比如latin1，char是固定长度。在使用多字节字符集时，char则被当作vachar类型。

这一问题是很多程序员容易忽略的。

另一个容易忽略的问题是varchar最大能存储多长字节。官方资料显示是65536，但是如果你定义一个varchar长度65535是会报错的。

实际测试，最大值是65532。这里又隐藏了一个问题，如果字符集是utf-8，定义varchar(65532)还是会报错的。因为varchar(n)中的n指的是字符长度。



日常的设计，对于长度相对固定的字符串，可以使用 char，对于长度不确定的，使用 varchar 更合适一些。



## blob 和 text 有什么区别

- blob 用于存储二进制数据，而 text 用于存储大字符串。
- blob 没有字符集，text 有一个字符集，并且根据字符集的校对规则对值进行排序和比较



## DATETIME 和 TIMESTAMP 的异同

**相同点**：

1. 两个数据类型存储时间的表现格式一致。均为 `YYYY-MM-DD HH:MM:SS`
2. 两个数据类型都包含「日期」和「时间」部分。
3. 两个数据类型都可以存储微秒的小数秒（秒后 6 位小数秒）



**区别**：

1. **日期范围**：DATETIME 的日期范围是 `1000-01-01 00:00:00.000000` 到 `9999-12-31 23:59:59.999999`；TIMESTAMP 的时间范围是`1970-01-01 00:00:01.000000` UTC `到 ``2038-01-09 03:14:07.999999` UTC
2. **存储空间**：在5.6.4之前，datetime存储占用8个字节，而timestamp是占用4字节；但是在5.6.4之后，由于这两个类型允许有小数部分，所以占用的存储空间和以前不同；MySQL规范规定，datetime的非小数部分需要5个字节，而不是8个字节，而timestamp的非小数部分是需要4个字节，并且这两个部分的小数部分都需要0到3个字节，具体取决于存储值的小数秒精度。

1. **时区相关**：DATETIME 存储时间与时区无关；TIMESTAMP 存储时间与时区有关，显示的值也依赖于时区
2. **默认值**：DATETIME 的默认值为 null；TIMESTAMP 的字段默认不为空(not null)，默认值为当前时间(CURRENT_TIMESTAMP)





## MySQL 中 in 和 exists 的区别

MySQL 中的 in 语句是把外表和内表作 hash 连接，而 exists 语句是对外表作 loop 循环，每次 loop 循环再对内表进行查询。我们可能认为 exists 比 in 语句的效率要高，这种说法其实是不准确的，要区分情景：

1. 如果查询的两个表大小相当，那么用 in 和 exists 差别不大。
2. 如果两个表中一个较小，一个是大表，则子查询表大的用 exists，子查询表小的用 in。
3. not in 和 not exists：如果查询语句使用了 not in，那么内外表都进行全表扫描，没有用到索引；而 not extsts 的子查询依然能用到表上的索引。所以无论那个表大，用 not exists 都比 not in 要快。



## drop、truncate和delete的区别

| 区别点            | drop                                                 | truncate                                           | delete                                                       |
| ----------------- | ---------------------------------------------------- | -------------------------------------------------- | ------------------------------------------------------------ |
| 执行速度          | 快                                                   | 较快                                               | 慢                                                           |
| 命令分类          | DDL（数据定义语言）                                  | DDL（数据定义语言）                                | DML（数据操作语言）                                          |
| 删除对象          | **删除整张表和表结构**，以及表的索引、约束和触发器。 | **只删除表数据**，表的结构、索引、约束等会被保留。 | **只删除表的全部或部分数据**，表结构、索引、约束等会被保留。 |
| 删除条件（where） | 不能用                                               | 不能用                                             | 可使用                                                       |
| 回滚              | 不可回滚                                             | 不可回滚                                           | 可回滚                                                       |
| 自增初始值        | -                                                    | 重置                                               | 不重置                                                       |



## count(1)、count(*) 与 count(列名) 的区别

**执行效果**：

- count(*)包括了所有的列，相当于行数，在统计结果的时候，不会忽略列值为 NULL
- count(1)包括了忽略所有列，用 1 代表代码行，在统计结果的时候，不会忽略列值为 NULL
- count(列名)只包括列名那一列，在统计结果的时候，会忽略列值为空（这里的空不是只空字符串或者 0，而是表示 null）的计数，即某个字段值为 NULL 时，不统计。

**执行速度**：

- 列名为主键，count(列名)会比 count(1)快
- 列名不为主键，count(1)会比 count(列名)快
- 如果表多个列并且没有主键，则 count（1） 的执行效率优于 count（*）
- 如果有主键，则 select count（主键）的执行效率是最优的
- 如果表只有一个字段，则 select count（*）最优。



## B+树

![B+树](image-20220902113957460.png)

[InnoDb页结构 - MaXianZhe - 博客园 (cnblogs.com)](https://www.cnblogs.com/juniorMa/p/13589501.html)

[Mysql数据读取方式以及InnoDb页存储](https://blog.csdn.net/MAYHENG/article/details/106575604)

## 事务隔离级别

[一文详解脏读、不可重复读、幻读](https://blog.csdn.net/weixin_41814871/article/details/124996364)

![事务隔离级别](事务隔离级别.png)

在RR级别下面不会产生 不可重复读，之所以不会产生 不可重复读，是快照读在RC和RR下的生成的策略不一样。

RC隔离级别下，是每个快照读都会生成并获取最新的Read View；而在RR隔离级别下，则是同一个事务中的第一个快照读才会创建Read View, 之后的快照读获取的都是同一个Read View。



## [MySQL模糊查询再也用不着 like+% 了！](https://mp.weixin.qq.com/s/ScJAgUkLHGpmTDS9KlZDBA)

## [聚簇索引与非聚簇索引（也叫二级索引）](https://www.jianshu.com/p/fa8192853184)

## [索引知识系列二：联合索引、覆盖索引（covering index）和索引条件下推（index condition pushdown, icp）详解](https://blog.csdn.net/mffandxx/article/details/111916252)



## Innodb是如何实现事务的

Innodb通过Buffer Pool，LogBuffer，Redo Log，Undo Log来实现事务，以一个update语句为例

1. Innodb在收到一个update语句后，会先根据条件查找到数据所在的页，并将该页缓存在Buffer Pool中
2. 执行update语句，修改Buffer Pool中的数据，也就是内存中的数据
3. 针对update语句生成一个RedoLog对象并存入LogBuffer中
4. 针对update语句生成UndoLog日志，用于事务回滚
5. 如果事务提交，那么则把RedoLog对象进行持久化，后续还有其他机制将Buffer Pool中所修改的数据页持久化到磁盘中
6. 如果事务回滚，则利用undolog日志进行回滚



## [mysql 插入加锁_MySQL锁_山东石头的博客-CSDN博客](https://blog.csdn.net/weixin_34553861/article/details/112380759)
