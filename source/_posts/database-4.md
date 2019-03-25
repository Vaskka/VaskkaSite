title: database-4
author: Vaskka
tags:
  - note
  - database
categories:
  - notes
date: 2019-03-25 09:13:00
---
# 深入sql

## join


### natural join

> 利用同样的属性连接成一个元组

#### 要求

共有属性只出现一次，并把共有属性去值相同的record连接起来

```sql
# 笛卡尔连接
select name, course_id from instructor, taeches where instructor.ID = teaches.ID;

# 等价于

# 自然连接
select name, course_id from instructor natural join teaches;
```

### join (inner)

> 两个关系表通过连接操作形成了一个关系表
> 经常出现在from clause中

####  outer join

```sql
A outer join B
```

+ left outer join

表示在第一个表 join 第二个表中using字段未出现的字段。第一个表中未匹配到的record标记为null，保证左边的表的数据都不会丢失。

+ right outer join

同理与left outer join

### 判断条件

+ on

后面可以接判断条件，更为灵活

+ natural

全部的公共属性全部相同（有时不止一个公共属性）

+ using

使用什么属性进行连接

## 视图 Views

> 定制查询的字段，可屏蔽相应的字段。
> 类似用变量存储sql语句，在使用时才执行。

### 视图创建

```sql
create view v(a_1, a_2) as <query expression>;
```

在表更改或删除后，应该重建视图

## 事务 Transaction

> 不可分割的子操作



