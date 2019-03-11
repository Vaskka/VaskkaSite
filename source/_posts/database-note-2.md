title: database-note-2
author: Vaskka
tags: []
categories: []
date: 2019-03-11 08:16:00
---
# SQL

## DDL

### 删除表、数据

```sql
# 删除表结构和数据
drop table 表名;
```

```sql
# 保留表结构，可筛选条件
delete from 表名;
```

## DML 

> DML: data-manipulation language

### 定义

提供操作数据的能力，包括**增删查改**。

### 查询

#### 定义

$select \quad A_1, A_2, ..., A_3 \quad from \quad r_1, r_2, ..., r_m \quad where \quad P$

> $A_i$ 表示**属性集**
> $r_i$ 表示**关系表**
> $P$ 表示**查询条件**，谓词(predicate)

``` sql
select 列名_1,列名_2, ... from 表明 where 筛选条件;  
```
#### 关键字

+ **distinct**:去除重复record，**all**:选取全部可重复record
+ **\***:通配符，选取全部属性

#### from

操作多个表时，对这几个表的属性集做笛卡尔连接。

#### Natural Join

> 在两张表中存在共有取值的record连接起来。

```sql
select * from A natural join B; 
```

#### 重命名

```sql
select ID, name, salary/12 as monthly_salary from teacher;
```

#### 模糊匹配

> 模糊匹配使用 **like**

+ %: *
+ \_\_\_: {3}
+ \_\_\_%: {3,}

#### 排序

+ asc: 升序，desc: 降序
+ 多个条件表示，在前一个条件无法区分时，下一个条件作为评判标准。