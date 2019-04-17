title: database-note-1
author: Vaskka
tags:
  - database
  - note
categories:
  - notes
date: 2019-03-04 08:16:00
---
# Relation Model

## Attribute Types

+ domain: 域。可能的取值范围。
+ NULL: 空值。包含在全部的域中。（可能造成数据的不一致问题）
+ atomic： 原子性。不可分割。

## Relation Schema and Instance

> Schema: 模式（关系表），表示一张表的表结构。	
> Instance： 某个特定时刻的表的值。
   
### Define
	
$R=(A_1,A_2,...,A_n)$，其中$D_1,D_2,...,D_3$表示每种属性的域。$R \subset (D_1 \times D_2 \times D_3 \times ... \times D_n)$。

## Database

+ 一个数据库包含多张表。
+ 应该将一个系统抽象化为多张表，而不是将多种关系糅合成一张表，反之造成的问题：
	+ 多个重复的信息。
    + 需要**NULL**来进行信息的补充。

## Keys

> Keys：码。

+ $K \subset R$，码是属性的子集。

### 种类
	
+ superkey-超码：能唯一的确定一个record。
	+ 超码的**超集**也是超码。
+ candidatekey-候选码：唯一标志record，且为超码的**最小子集**。
	+ 可以为多个（只要符合定义）
+ primarykey-主码：候选码中的某一个（选为主码）。

## Relational Query Language

### 种类

+ Relational algebra
+ Tuple Relational Calculus
+ Domain Relational Calculus

### 运算

#### 查询（选择）

##### 行查询

e.g. $\sigma_{A=B and D > 5}(r)$  means Select tuples with $A=B$ and $D > 5$

##### 列查询 （投影）

e.g. $\Pi_{A, C}(r) $，随后去除重复的record。

#### 连接

##### 笛卡尔连接

e.g. $r \times s$（将r表中每个record分别与s表中全部的record进行连接）。

##### 自然连接

> 形成的结果为两张表中共有属性只出现一次且共有属性上取值相同的record进行连接。

e.g. $r \Join s$ （假设r有ABCD这些key，s有BCD这些key，则$r \Join s $得到的表keys包含ABCDE， 其中r中BCD的值域s中BCD中值相等的record会成为结果表的record）。

#### 并

e.g. $r \cup s$（将r，s表中所有的元素进行合并，再去重）。

#### 差

e.g. $r-s$（生成一个新表，其中record存在r表中但不存在s表中）。

#### 交

e.g. $r \cap s$（同时在r和s表中的record）

# SQL

> 生于IBM实验室（IBM San Jose Research Laboratory）
> 基于版本：SQL-92

## DDL (data-definition language)

### 主要功能（应用）

+ **定义表结构**
+ **定义数据约束**
+ **参照定义**
+ **安全授权定义**
+ **物理存储的定义**
+ ...

## domain types in SQL (SQL的域类型)

+ char 定长字符串
+ varchar(n) 可变定长n的字符串
+ int 机器相关的integer
+ smallint 短整型
+ numeric(d, p) 数字类型，总位数d位，小数点后p位
+ real，double，precision 双精度
+ float 单精度

## 具体查询

### 建表

e.g. 
``` sql
create table r (
    A_1      D_1, # A_1   D_1   primary key, 标注格式也可以
    A_2      D_2 not null,
    ...,
    A_n      D_n,
    primary key (A_n),
    foreign key (A_fk) references A_pk 
    );
# 其中，A_fk 称作 参照表 的外码，A_pk 是 被参照表 的主码
```
+ 主码默认**not null**

### 改动表

e.g.
``` sql
# 删除表结构及其数据
drop table r;

# 删除表中数据保留表结构
delete from r;

# 增加列（属性）（默认分配 null ）
alter table r add A D(n);

# 删除列 （属性）（大多数据库的实现不支持此操作）
alter table r drop A;
```
