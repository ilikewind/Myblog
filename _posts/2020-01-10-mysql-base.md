---
layout: post
title: "MySql基本使用命令"
subtitle: ""
catalog: true
author: "WangW"
head-style: text
tags:
    - Mysql
    - Database
---

## 数据库操作
```sql
mysql -u<root> -p<password> -- 链接数据库

exit/quit/ctrl+d  -- 退出数据库

mysqldump -u<root> -p <database_name> > <ssssss>.sql;  -- 备份
mysql -u<root> -p <database_name> < <ssssss>.sql;  -- 恢复

select version(); -- 显示数据库版本

select now(); -- 显示时间

show databases;  -- 查看所有的数据库

create database <database_name>;  -- 创建数据库
create database <database_name> charset=utf8;  -- 创建数据库，使用utf8编码

show create database <database_name>;  -- 查看创建数据库的语句

select database();  -- 查看当前使用的数据库

use <database_name>;  -- 使用数据库

drop database <database_name>;  -- 删除数据库

```

<!--break-->

## 数据表操作
```sql
show tables;  -- 查看数据库中所有表

desc <table_name>;  -- 查看表结构

create table <table_name>(  -- 创建表
    column1 datatype contrai,
    column2 datatype,
    column3 datatype
); 
create table classes(  -- 创建班级表
    id int unsigned auto_increment primary key not null,
    name varchar(10)
);
create table students(  -- 创建学生表
    id int unsigned primary key auto_increment not null,
    name varchar(20) default '',
    age tinyint unsigned default 0,
    height decimal(5,2),
    gender enum('男','女','人妖','保密'),
    cls_id int unsigned default 0
)

alter table <table_name> add 列名 类型;  -- 修改表，添加字段
alter table <table_name> change 原名 新名 类型及约束;  -- 修改表，修改字段(重命名版)
alter table <table_name> modify 列名 类型及约束;  -- 修改表，修改字段(不重命名版)
alter table <table_name> drop 列名;  -- 修改表，删除字段

drop table <table_name>;  -- 删除表

show create table <table_name>;  -- 查看表的创建语句

```

## 数据的增删改查(curt) create update retrieve Delete

### 查询基本使用
```sql
select * from <table_name>;  -- 查询所有列

select 列1, 列2 from <table_name>;  -- 查询列1, 列2

```

### 增加基本使用
> 格式:INSERT [INTO] tb_name [(col_name,...)] {VALUES | VALUE} ({expr | DEFAULT},...),(...),...

```sql
-- 说明：主键列是自动增长，但是在全列插入时需要占位，通常使用0或者 default 或者 null 来占位，插入成功后以实际数据为准
-- 全列插入：值的顺序与表中字段的顺序对应
insert into 表名 values(...)  -- 完全插入

-- 部分列插入：值的顺序与给出的列顺序对应
insert into 表名(列1,...) values(值1,...)  -- 部分列插入


-- 上面的语句一次可以向表中插入一行数据，还可以一次性插入多行数据，这样可以减少与数据库的通信
-- 全列多行插入：值的顺序与给出的列顺序对应
insert into 表名 values(...),(...)...;  -- 全列多行插入

insert into 表名(列1,...) values(值1,...),(值1,...)...;  -- 部分列多行插入
```

### 修改基本使用
> 格式: UPDATE tbname SET col1={expr1|DEFAULT} [,col2={expr2|default}]...[where 条件判断]

```sql
update 表名 set 列1=值1,列2=值2... where 条件  -- 修改
```

### 删除基本使用
> DELETE FROM tbname [where 条件判断]
```sql
delete from 表名 where 条件

-- 逻辑删除，本质就是修改操作
update students set isdelete=1 where id=1;

```

