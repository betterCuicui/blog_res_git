---
title: linux下redis的安装与使用
date: 2016-05-04 10:58:48
tags: [php,linux,redis]
category: [database]
---
因为要写的项目需要用到队列，所以搞上了redis，然后花了点时间安装，现在总结一下：
<!--more-->
## 一、redis安装
- 首先当然是`yum install redis`
- 然后启动服务 `service redis start`
- 老规矩开机启动`chkconfig --level 2345 redis on`

#### 1、切换到你想安装redis的目录下
`cd /usr/sbin/php_extensions`
#### 2、把拓展down下来
`wget https://github.com/phpredis/phpredis/archive/2.2.4.tar.gz`
#### 3、解压
`tar -zxvf 2.2.4.tar.gz`
#### 4、进入安装目录
`cd phpredis-2.2.4`
#### 5、用phpize生成configure配置文件
`/usr/bin/phpize`
- 如果你不知道你的phpize在哪里，可以用whereis phpize
- phpize时报“Can't find PHP headers in /usr/include/php”，原因是没有安装php-devel，“yum install php-devel”

#### 6、配置
`./configure --with-php-config=/usr/bin/php-config`
#### 7、编译和安装
`make && make install`

## 二、配置php支持
```
vi /etc/php.ini
extension=redis.so; //在最后一行添加这个
```

## 三、重启服务
```
service nginx restart
service php-fpm restart
```
- phpinfo();看看有么有支持redis拓展啦。。。哈哈

## 四、redis使用
#### 1、php连接redis
```
<?php
  //连接本地的 Redis 服务
  $redis = new Redis();
  $a = $redis->connect('127.0.0.1', 6379);
  var_dump($a);//连接失败时为false；
  //设置 redis 字符串数据
  $redis->set("tutorial-name", "Redis tutorial");
  echo "213weq";
  // 获取存储的数据并输出
  echo "Stored string in redis:: " . $redis->get("tutorial-name");
?>
```
#### 2、redis list功能
```
<?php
//连接本地的 Redis 服务
$redis =  new  Redis();
$redis->connect('127.0.0.1',  6379);
echo "Connection to server sucessfully";
//存储数据到列表中
$redis->lpush("tutorial-list",  "Redis");
$redis->lpush("tutorial-list",  "Mongodb");
$redis->lpush("tutorial-list",  "Mysql");
// 获取存储的数据并输出
$arList = $redis->lrange("tutorial-list",  0  ,5); echo "Stored string in redis";
print_r($arList);
?>
```
#### 3、redis keys功能
```
<?php
//连接本地的 Redis 服务
$redis = new Redis();
$redis->connect('127.0.0.1', 6379);
echo "Connection to server sucessfully";
// 获取数据并输出
$arList = $redis->keys("*");
echo "Stored keys in redis:: ";
print_r($arList);
?>
```