---
title: php安装
date: 2017-06-14 11:41:23
tags: [php]
category: [php]
---

php安装方法
<!--more-->
## 一、下载安装php
```
wget  http://cn2.php.NET/distributions/php-7.1.2.tar.gz
tar zxvf php-7.1.2.tar.gz
cd  php-7.1.2
./configure --enable-fpm --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc
```

## 二、处理问题
![](http://images2015.cnblogs.com/blog/594530/201611/594530-20161108172205639-2090518596.png)
```
yum install libxml2-devel
```

## 三、报错yum正在执行进程2321 pid
```
kill -s 9 2321
```
然后继续完成上述步骤

## 四、编译&安装php
```
make && make install
```

## 五、配置
```
#配置  （一下配置都是我个人的路径配置 大家请按照各自路径配置）
cp  php.ini-development /usr/local/lib/php.ini
cp sapi/fpm/init.d.php-fpm /etc/init.d/php7-fpm
chmod +x /etc/init.d/php7-fpm
cd /usr/local/php/etc
cp php-fpm.conf.default php-fpm.conf
cp php-fpm.d/www.conf.default  php-fpm.d/www.conf
```

## 六、启动php
```
/etc/init.d/php7-fpm  start
```