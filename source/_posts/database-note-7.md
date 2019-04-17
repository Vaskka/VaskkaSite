title: database-note-7
author: Vaskka
tags:
  - database
  - note
categories:
  - notes
date: 2019-04-15 08:51:00
---
# Triggers

> 触发器，处理数据约束条件。

## 条件

某个操作的**执行前**或者**执行后**进行更新。

## 定义

+ 插入后触发

```sql
create trigger time_slot_check1 after insert on `section`
referencing new row as nrow
for each row
when (nrow.time_slot_id not in (
	select time_slot_id time_slot)) # 条件
begin # 动作
	rollback
end
```

*新行*代表刚插入数据库的元组。

+ 删除后触发

```sql
create trigger timeslot_check2 after delete on timeslot 
	referencing old row as orow
    for each row 
    when (orow.time_slot_id not in (select time_slot_id from time_slot))
    and orow.time_slot_id in (select time_slot_id from section)
begin
rollback
end
```

类似RESTRCUCT级联限制。

+ 更新前出发

```sql
create trigger setnull_trigger before update of takes
referencing new row as nrow
for each row 
when (nrow='')
begin
	set nrow.grade=null;
end
```

对于此sql，可以在表takes之后使用on lt;field name&gt;来指定在哪个字段进行更新时进行触发。

# Recursion SQL

...

# 高级聚合函数

## Ranking

```sql
select ID, rank() over (order by GPA desc) as s_rank from student_grades;
```

可用*count()*模拟实现。