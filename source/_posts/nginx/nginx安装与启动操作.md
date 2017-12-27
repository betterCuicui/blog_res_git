---
title: nginx安装与启动操作
date: 2017-02-09 10:58:48
tags: [linux,nginx]
category: nginx
---
[http://nginx.org/en/download.html](http://nginx.org/en/download.html) 这个网站查看最新最稳定的nginx版本。我的目前最稳定版本是1.10.3.
<!--more-->
### 一、wget http://nginx.org/download/nginx-1.10.3.tar.gz  下载最稳定的nginx下载包

### 二、ngxin`tar -zxvf nginx-1.10.3.tar.gz`

### 三、配置信息 ` ./configure --prefix=/usr/local/nginx`
可能会出现下面的错误
##### 3.1、 ./configure: error: the HTTP rewrite module requires the PCRE library.
**安装pcre-devel解决问题**
yum -y install pcre-devel
##### 3.2、 ./configure: error: the HTTP cache module requires md5 functions from OpenSSL library.
**解决办法：**
yum -y install openssl openssl-devel

### 四、make 编译

### 五、make install 安装

然后你的nginx就成功的安装了,可以查看nginx配置是不是安装成功
```
# /usr/local/nginx/sbin/nginx -t
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```

### 六、nginx启动
```
./usr/local/nginx/sbin/nginx
```

##### 启动的时候报错
```
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] still could not bind()
```
这是因为启动了两次，这个端口已经被上一个nginx占领了。所以启动后，可以
```
ps aux | grep nginx
```
查看一下nginx是不是已经启动成功了。

##### 访问的时候，linux本机可以curl http://127.0.0.1:80 可以访问，但是外网访问不到。这个是因为防火墙的原因
```
service iptables stop
```
关闭防火墙即可。

### 七、nginx步骤

- 1、nginx 启动

```
./nginx
```

- 2、nginx重启

```
./nginx -s reload
```

- 3、nginx关闭

```
./nginx -s stop
```

或者
```
ps -e | grep nginx
kill -9 nginx进程号
```