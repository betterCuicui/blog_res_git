---
title: APUS校招题--查询10亿IP地址
date: 2016-09-29 18:41:23
tags: [mysql,php,redis]
category: [database]
---
![这个是最近APUS的一个校招题目：](/public/image/apus_find_ip.jpg)
假设APUS网站每天有超过10亿次的网页访问量，出于安全考虑，网站会记录访问客户端访问的IP地址和对应时间。如果现在已经记录了1000亿条数据，想统计一个指定时间段内的各个国家地区IP地址访问量，那么这些数据应该按照什么方式来组织，才能尽快满足上面的统计需求呢？设计完方案后，并指出该方案的优缺点，比如在什么情况下，会非常慢？
<!--more-->
### 1、首先我们需要把这些数据存储到数据库中，这个毋庸置疑。
字段： id、city_id、ip、visit_time   这几个字段是必须的。。。
因为要存储在数据库中，为了节省内存，需要先对数据进行处理一下。
- **ip以int的形式存储：**所以先对得到的ip进行转化

php代码：
```
$ip_int = ip2long($ip_str); //ip转化为int
$ip_str = long2ip($ip_int); //int转化为ip
```
mysql代码
```
select INET_ATON('192.168.1.1');
select INET_NTOA(3232235777);
```
上面两种代码是一样的！！！
- **访问时间以unix时间戳的形式存储**,这样时间也可以以int的形式存储，而不用字符串，大大节省空间爱你
```
$visit_time = time();
```
- **最后地区应该用一个int来存，而不是string**：地区用int首先是可以节省空间，其次建立索引方便。
而如果数据量不大的话，我觉得应该存在redis里面，key为city_name、value为city_id。这样查询的速度可想而知，毕竟hash的时间复杂度是1。

### 2、分表存储
##### 2.1 为什么要分表
- 每天有超过10亿次的网页访问量，也就是说QPS大概是11574。并发量这么高，我们知道insert到一个同一个数据表会加锁处理的，所以不能让这么多的insert请求一直在等待吧。所以要分表。
- 有1000亿条数据，这么海量的数据，如果塞到一个表中，查询不得慢死啊，所以一开始就想到要分表存储。

##### 2.2 按照什么来分表呢？
根据题目的意思是需要统计一段时间内各个国家地区ip地址访问量。所以现在就有三种分表方式：
- 按照时间分表。
- 按照地区分表。
- 按照地区加时间来分表
我更加倾向与第三种。

##### 2.3 任何按照地区时间来分表
- 根据ip获得地址：这个网上有很多的教程，这里写一个简单的。
```
//用来获取淘宝的对应IP的省市信息，返回的是json格式的数据。
$ip = get_client_ip(); //获取当前用户的ip
$url = "http://ip.taobao.com/service/getIpInfo.php?ip=".$ip;
$data = file_get_contents($url);//调用淘宝接口获取信息
echo $data;
```
- 首先根据地区来分表：因为地区太多，而并不是每个地区访问量都相同，可能有些地区访问量特别少，如果单独分一个表就太浪费了。假设我们按照地区分了100个表，table_00、table_01、'''''''table_98、table_99。然后用hash的方式来获取表名。
```
function get_hash_table($table,$city_name) {
    $str = crc32($city_name);
    $hash = substr(abs($str),?0,?2);
    return $table."_".$hash;
}
```
至于为什么要只用abs($str)，因为返回值可能是负数。
- 然后按照地区加时间分表：比如说我们按照地区加月来分表：比如现在是16年9月，那么就应该是table\_00\_16\_09、table\_01\_16\_09''''''
形式就是**table\_地区\_年\_月**

### 3、索引问题
一般谈到查询的时候，就马上会想到索引index。
我们可以对字段：city_id、visit_time建立索引，这两个字段都是int类型，然后又经过分表的过程，所以数据量不大，那么查询的时候，可想而知，会很快的。。。

### 4、mysql集群
mysql集群对应的需求是每秒查询的次数有很多，如果不多就直接忽略吧。
当查询次数很多的时候，比如说一次来了10个sql需要查询一个库，那么这10个sql会一次等待查询。会比较慢。
可以做mysql集群，比如说1个主库，2个从库。主库用于插入数据，从库用于查询数据。这样，一次来10个sql，平摊下来一个数据库分担5个sql。明显等待的时间会降低。

### 5、缺点：
- 当QPS特别大的时候，因为每次访问都插入数据，而因为插入数据的时候，需要维护索引，所以会大大降低插入时候的速度。
- 集群问题，刚刚插入的数据查询是会有延迟的。当主库刚刚插入一条新数据的时候，从库一直在同步主库的数据，可能会有延迟，所以在同步的那个时间，可能查询不到。

### 以上纯属个人想法，有更好的解决方案，欢迎提醒。


#### 参考资料
- [mysql 分表的几种方式](http://wenku.baidu.com/link?url=JNthoIkSEhzFhr3e4kPTxLZS7RV3nO4aAAj8ilm5muOdD1kTneSgXlgziKH9GmfWu-0BJ2oWMaatZVEBEX5ZtWphnEWuaYxouAYLTFbc20m)
