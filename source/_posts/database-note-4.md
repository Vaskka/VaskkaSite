title: database-note-4
author: Vaskka
tags:
  - note
  - database
categories:
  - notes
date: 2019-04-01 08:26:00
---
# SQL-续

## not null 与 unique

### not null

略

### unique

```sql
unique (A_1, A_2, ... , A_n)
```

后接属性集，可作为候选码。

### check

类似where，后面接条件。

```sql
create table s_table (
	...,
    ...,
    check (a_1 in ('ABC', 'WIN', 'DES')) # a_1 属性只能为'ABC', 'WIN', 'DES', 类似枚举
)
```

## 外码约束

```sql
foreign key S references table_name
# S: 主表中做外吗，table_name: 外表
```

### 级联

```sql
# 主表ddl
create table main_table (
	A char(5) primary key,
    other_id varchar(60) references f_table
    on delete cascade # 删除设定为级联操作
    on update cascade, # 更新设定为级联操作
    ...
);

# 外表ddl
create table other_name (
	other_id int(11) primary key,
    other_other char(1) not null
);
```

## check

```sql
# 嵌套子查询表达某个属性取值的范围
check (time_slot_id in (select time_slot_id from time_slot));
```
### 作用

当某个属性只是**参照完整性约束**下的码而**不是外码**，可以用**check** clause对这种情况进行描述。

在创建表的时候描述为对某种参照情况的描述。

## index

```sql
create table main_table (
	A char(5) primary key,
    other_id varchar(60) references f_table
    on delete cascade # 删除设定为级联操作
    on update cascade, # 更新设定为级联操作
    ...
);

# index

create index A_index on main_table(A);
```

### 作用

可以快速检索，详见11章

## create type

```sql
# 包装类型
create type Dollars as numeric(12, 2) final;
```

## 大数存储

+ blob 数字型大数
+ clob 字符型大数


## 授权 

> Authorization

+ 数据授权
  + Read: allow reading.
  + Insert: allow insert, not modification.
  + Update: allow modification.
  + Delete: allow deletion.
+ 结构授权
  + Index: allow create and deletion of Indices.
  + Resourse: allow creation of relation.
  + Alteration: allow addition or deletion of attribute in a relation.
  + Drop: allow deletion of relations.

### grant

```sql
grant <priviege list> on <relation name or view name> to <user list>;
```
#### &lt;priviege list&gt;:

+ insert
+ update
+ delete
+ select
+ all privieges

#### &lt;user list&gt;:

+ a user-id
+ public, allow all user valid users the priviledge granted
+ a role

### revoke

> 回收权限

```sql
revoke <priviledge list>;
```

all 代表全体用户。

public 代表非指明的用户。

#### 特殊情况

当某个用户在同一个权限上被授予两次，每次调用revoke会回收其中一条权限。

### role

```sql
craete role instructor;
grand instructor to Amit;
```

#### 作用

当某个用户从表中删除，其关联的角色所授予的权限仍然有效。

