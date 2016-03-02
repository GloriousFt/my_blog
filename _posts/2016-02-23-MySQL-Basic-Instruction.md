---
layout: post
title: MySQL用户权限常用操作
date: 2016-02-23 13:51:17.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- MySQL
meta:
  _edit_last: '1'
---
### 登录MySQL
```
mysql -u 'username' -p'password' //用户名和密码直接换掉,-p和密码中间没有space.
```

进入到mysql之后

### 新增用户
```
insert into mysql.user(Host,User,Password) values("localhost","test",password("1234"));
//修改test 和 密码
```

### 删除用户
```
delete from mysql.user Where User='test' and Host='localhost';
//以用户名和host为目标选择
```

<!--more-->

### 查看用户信息
```
select user,host from mysql.user; //查看用户user和host信息
```

### 修改用户信息
```
update mysql.user set password=password('新密码') where User="test" and Host="localhost";
//修改密码
update mysql.user set host='%' where User="test";
//修改host,'%'表示外部主机可以连接,'localhost'表示只能本机连接.
```

### 用户权限设置
```
grant select,delete,update,create,drop on basename.* to "username"@"%" identified by "1234"; // 或者all privileges
//在"basename"数据库中,赋予username以上权利,密码要付在后面.
```

### 数据库操作
```
show databases; //显示所有数据库
drop database dataname; //删除指定数据库
drop table tablename; //删除指定表

```

**对用户信息进行修改后,要记得加flush privileges;**






