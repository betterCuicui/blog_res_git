---
title: redis的安装
date: 2017-06-14 11:58:48
tags: [php,linux,redis]
category: [database]
---
redis的安装步骤
<!--more-->
- 1、redis官网下载redis安装包。
- 2、将其解压并进入目录
- 3、编译源码

```
make
cd src
make install PREFIX=/usr/local/redis
```

- 5、将配置文件移动到redis目录
```
cd ..
mv redis.conf /usr/local/redis/etc/
```
hmset man nam "Tom" age 25 career "Programmer"

- 6、编辑redis配置，配置后台守护进程启动
```
vim /usr/local/redis/etc/redis.conf
将daemonize的值改为yes
```

- 7、启动redis守护进程
```
//启动的时候带上配置文件
/usr/local/redis/bin/redis-server /usr/local/redis/etc/redis.conf
```

- 8、设置开机启动
```
vim /etc/rc.local
//添加一行
/usr/local/redis/bin/redis-server /usr/local/redis/etc/redis.conf
```

- 9、redis客户端连接,输入命令
```
/usr/local/redis/bin/redis-cli
```

- 10、可以设置别名
```
vim ~/.bashrc
//添加别名
alias redis='/usr/local/redis/bin/redis-cli'
```
使用命令重新加载`source ~/.bashrc`
```
[root@192 etc]# source ~/.bashrc
[root@192 etc]# redis
127.0.0.1:6379>
```

- 11、接下来我们看看/usr/local/redis/bin目录下的几个文件时什么
(1)、redis-benchmark：redis性能测试工具
(2)、redis-check-aof：检查aof日志的工具
(3)、redis-check-dump：检查rdb日志的工具
(4)、redis-cli：连接用的客户端
(5)、redis-server：redis服务进程

- 12、redis配置

```
daemonize：如需要在后台运行，把该项的值改为yes
pdifile：把pid文件放在/var/run/redis.pid，可以配置到其他地址
bind：指定redis只接收来自该IP的请求，如果不设置，那么将处理所有请求，在生产环节中最好设置该项
port：监听端口，默认为6379
timeout：设置客户端连接时的超时时间，单位为秒
loglevel：等级分为4级，debug，revbose，notice和warning。生产环境下一般开启notice
logfile：配置log文件地址，默认使用标准输出，即打印在命令行终端的端口上
database：设置数据库的个数，默认使用的数据库是0
save：设置redis进行数据库镜像的频率
rdbcompression：在进行镜像备份时，是否进行压缩
dbfilename：镜像备份文件的文件名
dir：数据库镜像备份的文件放置的路径
slaveof：设置该数据库为其他数据库的从数据库
masterauth：当主数据库连接需要密码验证时，在这里设定
requirepass：设置客户端连接后进行任何其他指定前需要使用的密码
maxclients：限制同时连接的客户端数量
maxmemory：设置redis能够使用的最大内存
appendonly：开启appendonly模式后，redis会把每一次所接收到的写操作都追加到appendonly.aof文件中，当redis重新启动时，会从该文件恢复出之前的状态
appendfsync：设置appendonly.aof文件进行同步的频率
vm_enabled：是否开启虚拟内存支持
vm_swap_file：设置虚拟内存的交换文件的路径
vm_max_momery：设置开启虚拟内存后，redis将使用的最大物理内存的大小，默认为0
vm_page_size：设置虚拟内存页的大小
vm_pages：设置交换文件的总的page数量
vm_max_thrrads：设置vm IO同时使用的线程数量
```