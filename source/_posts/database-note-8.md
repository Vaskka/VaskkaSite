title: database-note-8
author: Vaskka
tags:
  - database
  - note
categories:
  - notes
date: 2019-04-29 09:12:00
---
# 元组关系演算

## 表示形式

$\{t|P(t)\}$ $t$表示元组，$P(t)$表示元组满足的谓词。

$t[A]$ $t$代表元组，$t[A]$元组在id上特定的值。

### example

$\{t|t \in instructor \land t[salary] > 80000\}$

## 条件

找到教员的名字所在系的大楼是华盛顿大楼

$\{t|\exists s \in instructor(t[name]=s[name]
\land \exists u \in department(u[deptname]=s[deptname] 
\land u[building]="Watson") )\}$

# 域关系演算

找到ID，name，dept_name, salary 且薪水在80000以上的教员

$\{<i,n,d> | <i,n,d> \in instructor \land s > 80000 \}$