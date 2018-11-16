---
title: mysql copy表或表数据常用的语句整理汇总
tags:
  - 工作整理
  - 学习笔记
categories: web技术(数据库Mysql)
abbrlink: 26f0825f
date: 2015-05-17 10:40:19
---

## mysql copy表或表数据常用的语句整理汇总.

假如我们有以下这样一个表：

|id|username|password|
| :--|--: |:--:|
| 1 | admin|******  |
| 2 | sameer|****** |
| 3 | stewart|******|

### 代码块

```sql
CREATE TABLE IF NOT EXISTS `admin` (   
`id` int(6) unsigned NOT NULL auto_increment,   
`username` varchar(50) NOT NULL default '',   
`password` varchar(100) default NULL,   
PRIMARY KEY (`id`)   
) ENGINE=MyISAM DEFAULT CHARSET=latin1 AUTO_INCREMENT=4 ;

```
<!-- more -->
**下面这个语句会拷贝表结构到新表newadmin中。 （不会拷贝表中的数据）**

(1).CREATE TABLE newadmin LIKE admin

**下面这个语句会拷贝数据到新表中。 注意：这个语句其实只是把select语句的结果建一个表。所以newadmin这个表不会有主键，索引**

```sql
CREATE TABLE newadmin AS   
(   
SELECT *  FROM admin   
)

```

**如果你要真正的复制一个表。可以用下面的语句**

```sql
CREATE TABLE newadmin LIKE admin;
INSERT INTO newadmin SELECT * FROM admin;
```

**我们可以操作不同的数据库**

```sql
CREATE TABLE newadmin LIKE shop.admin;
CREATE TABLE newshop.newadmin LIKE shop.admin;
```

**我们也可以拷贝一个表中其中的一些字段**

```sql
CREATE TABLE newadmin AS   
(   
SELECT username, password FROM admin   
)

```

**我们也可以将新建的表的字段改名**

```sql

CREATE TABLE newadmin AS   
(   
SELECT id, username AS uname, password AS pass FROM admin   
)

```

**我们也可以拷贝一部分数据**
```sql

CREATE TABLE newadmin AS   
(   
SELECT * FROM admin WHERE LEFT(username,1) = 's'   
)

```
**我们也可以在创建表的同时定义表中的字段信息**

```sql

CREATE TABLE newadmin   
(   
id INTEGER NOT NULL AUTO_INCREMENT PRIMARY KEY   
)   
AS   
(   
SELECT * FROM admin   
)

```

**MySQL复制表结构及数据到新表**

```sql

CREATE TABLE 新表  SELECT * FROM 旧表

```

**Mysql只复制表结构不复制数据**

```sql
CREATE TABLE 新表
SELECT * FROM 旧表 WHERE 1=2
(即:让WHERE条件不成立.)
```

**复制不同结构的表**

```sql

create table 新表(字段1,字段2,,,)    SELECT 字段1,字段2... FROM 旧表

```

### 参考:
- [mysql拷贝表的几种方式](http://database.51cto.com/art/201011/234776.htm)