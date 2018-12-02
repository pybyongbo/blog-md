---
title: Mysql学习笔记
tags:
  - Mysql
categories: Mysql
date: 2018-12-01 22:03:53
---
当我们一个人做一个项目的时候,如果涉及到后台相关的知识,mysql是必不可少的知识,如何建表,如何进行CURD操作等等,都离开数据库相关的知识,目前自己接触的后台程序写一下简单的接口,一般用的比较多点就是php和nodejs啦,如果想一个人玩转一个项目,做web开发的,这项东西也很有必要学习一下的.

### database

查看所有

> show databases;

进入

> use db_name;

删除

> drop database db_name;

### table

查看所有

> show tables;

查看结构

> desc tb_name

修改表名

> alter table tb_name rename to bbb;

添加字段

```
alter table tb_name add column col_name varchar(30);
# 添加主键
alter table tb_name add col_name int(5) unsigned default 0 not null auto_increment ,add primary key (tb_name);
```


删除字段

> alter table tb_name drop column col_name;

修改字段名

> alter table tb_name change col_name new_col_name int;

修改字段属性

> alter table tb_name modify col_name varchar(22);


### ROW

查找

> SELECT [col1,col2]|* FROM table_name

修改

> UPDATE tb_name SET col1 = val1, col2 = val2 WHERE col3 = val3

删除

> DELETE FROM tb_name WHERE col1 = val1

### 用户管理

修改root密码

> mysqladmin -u root password 'somepassword'

登录
> mysql [-h hostname] -u username|root -p

### 数据备份

导入

> mysql -u root -p dbname < /path/to/file.sql

导出


```
# export database
mysqldump [-h localhost] -u root -p dbname > /path/to/dbname.sql

# export table
mysqldump [-h localhost] -u root -p dbname tablename > /path/to/tablename.sql

# export database structure
mysqldump [-h localhost] -u root -p dbname --add-drop-table > /path/to/dbname_struct.sql
```

