title: mongodb with jpa 的一些问题处理
author: Vaskka
tags:
  - mongodb
  - jpa
categories:
  - 项目总结
date: 2019-04-29 11:26:00
---
> 问题源于小程序比赛的项目，看过需求文档后我觉得使用传统的关系型数据库过于麻烦，nosql型的数据库更加适合。综合考虑选择mongodb作为数据库。

## 基本配置

maven依赖：pom.xml

```xml
		<!-- jpa -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

        <!-- mongodb -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-mongodb</artifactId>
		</dependency>
```

和mysql的依赖比起来简洁不少。

应用级配置：application.properties

```properties
spring.data.mongodb.host=127.0.0.1
spring.data.mongodb.port=27017
spring.data.mongodb.database=happyfamily
spring.data.mongodb.username=op
spring.data.mongodb.password=xxxxxx
```

## 遇到的问题

### Q1:Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource could be configured.

#### 解决

在运行类中配置注解的exclude

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
public class HappyfamilyApplication {

	public static void main(String[] args) {
		SpringApplication.run(HappyfamilyApplication.class, args);
	}

}
```

### Q2: 执行插入时出现mongo的auth问题

#### 解决

> mongodb中每个数据库都有独立的权限系统，在进行操作之前需要为每个数据库添加role

##### mongodb 创建用户

+ 首先在非验证身份模式下运行mongod

```bash
sudo mongod
```

+ 为数据库添加grant权限用户

```bash
mongo
```

```js
use admin
db.createUser({
     user: "root",
     pwd: "root",
     roles: [{role: "userAdminAnyDatabase", db: "admin"}]})
```

+ 为指定数据库创建角色

```js
db.createUser(
	{
	 user: "op",
	 pwd: "xxxxxx",
	 roles: [ { role: "dbOwner", db: "happyfamily" } ]
	}
)
```

+ 打开auth模式，登陆mongo

```bash
sudo mongo --auth
mongo
```

登陆，使用刚刚创建的角色进行操作

```js
use admin
db.auth("op", "xxxxxx")
use happyfamily
show collections
```

##### 所有权限对照表

+ Read：允许用户读取指定数据库
+ readWrite：允许用户读写指定数据库
+ dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
+ userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户
+ clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
+ readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限
+ readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限
+ userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
+ dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。
+ root：只在admin数据库中可用。超级账号，超级权限