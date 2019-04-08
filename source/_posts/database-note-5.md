title: database-note-6
author: Vaskka
tags:
  - database
  - note
categories:
  - notes
date: 2019-04-08 08:16:00
---
# 高级sql

> + sql链接库
> + 数据类型、事务
> + 应用构建
> + 触发器
> + 高级聚合
> + OLAP

## JDBC and ODBC

> API: application programming interface

### 作用

+ 连接数据库与应用程序
+ 向数据库服务发送sql命令
+ 连接sql元组与相应的语言的数据类型（one by one）

### JDBC、ODBC introduce

> + ODBC: Open Database Connectivity 
> + JDBC: Java Database Connectivity

+ ODBC: work with c\cpp\c#
    + other API's such as ADO.NET sit on the ODBC.
+ JDBC: Java

### JDCB

> 连接java应用与sql服务

+ JDBC支持对**表数据**和**表结构**进行处理。

#### 步骤

+ 打开链接
+ 创建statement对象
+ 执行sql获取数据
+ 处理异常

### ODBC

> 为.net成员提供的数据库连接支持

## Embedded SQL

> 嵌入式sql





