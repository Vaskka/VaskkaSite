title: database-note-3
author: Vaskka
tags:
  - note
  - database
categories:
  - notes
date: 2019-03-18 08:20:00
---
# SQL-续

## union/intersect/except

> 并/交/差

### union

union all: 不去重(效率比union快很多)

### intersect

### except

> 在第一个集合不在第二个集合中

## null

is null : 用于检查null

## unknow

### 举例

+ OR: 
   + unknow **OR** true == **true**
   + unknow **OR** false == **unknow**
   + unknow **OR** unknow == **unknow**
+ AND:
   + true **AND** unknow == **unknow**
   + false **AND** unknow == **false**
   + unknow **AND** unknow == **unknow**
+ NOT:
   + **NOT** unknow == unknow

P **is unknow** == **true**: 当且仅当P是unknow

## 算数运算符

+ avg
+ min
+ max
+ sum: 自动忽略null
+ count

对于全部为null的集合，以上函数处了count，其他都返回null。

## group by

> 把某个列进行归类（类似HashMap）

在聚集函数出现时，除了聚集函数外全部col都需要出现在group by中
```sql
select id, name, avg(num) from user group by id, name; 
```
**Reason**： group by 指定的col在结果集中类似HashMap中的key，不可重复

## 子查询

e.g. :
```sql
select distinct course_id from section
	where semester = 'Fail' and year = 2009 and 
	course_id in (select course_id from section 
    				where semester = 'Spring' and year = 2010);
```

## some/all

+ some: 表示集合中某一个record（类似闭包）
   + 可配合 &lt; 、 &gt; 等
+ all: 表示集合中全部的record（表示全部）

## exists


$exists \; r \Leftrightarrow r \neq \varnothing$

类似 
```python
len(list) not 0
```

## not exists

$X-Y=\varnothing \; \Leftrightarrow \; X \subseteq Y$

## unique

筛选集合中只出现一次的record
