---
title: pdo给mysql插入数据乱码问题
date: 2017-06-28 11:56:23
tags: [mysql,php]
category: [database]
---

在弄毕设的时候，php用pdo给mysql插入数据，结果发现一直乱码，弄了好久弄不出来。mysql服务端的编码全都是utf-8的。。。数据库、表、相关字段也全是utf-8。。。为什么就不好使呢？神烦。。。
<!--more-->
### 最后发现问题所在，首先连接数据库的时候，应该设置字符集。
``` 
$dns = 'mysql:dbname=sausagedb;host=127.0.0.1;charset=utf8';
$user = 'root';
$password = '111111';
$dbh = new PDO($dns, $user, $password);
//需要设置utf-8
$dbh->exec("set names utf8");
```
### 如果是用php直接连接而不是pdo的话，应该加上一条语句
`mysql_query("SET NAMES 'utf8'");`