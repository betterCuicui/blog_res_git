---
title: php爬虫技术（1）---curl_*()函数
date: 2016-05-13
tags: [php]
category: [php]
---
php爬虫技术的基础也是核心，可能就是curl函数簇了吧！
<!--more-->
## 基本介绍
curl函数簇，能够模仿一个浏览器，好像浏览器是所有有的功能它都有，能够支持很多的协议，http、https、ftp……。所以使用它不仅能够get、post下载页面，也能上传数据，下载文件，真的是太强大了。。。
## 基本用法
- 初始化
`curl_init();`
- 设置变量
`curl_setopt();`[变量的选项](http://php.net/manual/zh/function.curl-setopt.php)
这一步为最重要，决定了这次执行curl函数簇的作用，是get还是post，是接收数据还是上传数据，需不需要打印出来等。。。
- 开始执行并且获取结果
`curl_exec();`
- 释放curl句柄
`curl_close();`

## curl的例子
这里举两个例子，一个是get、一个是post。都是以抓取天气预报的网页
#### 1、get例子
```
<?php
$aaa = curl_init();//初始化获得句柄
curl_setopt($aaa,CURLOPT_URL,'http://www.webxml.com.cn/WebServices
/WeatherWS.asmx/getWeather?theCityCode=31&theUserID=');
//设置url，因为是get，所以直接把参数带进去
curl_setopt($aaa,CURLOPT_RETURNTRANSFER,true);
//设置返回的数据不要直接打印出来
curl_setopt($aaa, CURLOPT_USERAGENT, 
"user-agent:Mozilla/5.0 (Windows NT 5.1; rv:24.0) Gecko/20100101 Firefox/24.0");
//设置浏览器选项
$output = curl_exec($aaa);
curl_close($aaa);
echo $output;
?>
```
#### 2、post例子
```
<?php
$data = 'theCityCode=792&theUserID=';
//待会需要传的参数，格式是固定的，就是这样'key1=value1&key2=value2&key3=value3.....'
//好像有时候需要传数组格式的数据，类似json
$curlobj = curl_init();
curl_setopt($curlobj,CURLOPT_URL,'http://www.webxml.com.cn/WebServices/WeatherWS.asmx/getWeather');
//设置url
curl_setopt($curlobj,CURLOPT_HEADER,0);
curl_setopt($curlobj,CURLOPT_RETURNTRANSFER,1);
curl_setopt($curlobj,CURLOPT_POST,1);//设置方式为post
curl_setopt($curlobj,CURLOPT_POSTFIELDS,$data);//设置post的数据
curl_setopt($curlobj,CURLOPT_HTTPHEADER,
array("application/x-www-form-urlencoded;charset=utf-8",
"Content-Length: ".strlen($data)));//设置头信息
curl_setopt($curlobj, CURLOPT_USERAGENT, 
"user-agent:Mozilla/5.0 (Windows NT 5.1; rv:24.0) Gecko/20100101 Firefox/24.0");
$rtn = curl_exec($curlobj);
if(!curl_errno($curlobj)){
  echo $rtn;
} else {
  echo 'cURL error: '.curl_error($curlobj);
}
curl_close($curlobj);
?>
```
- 有时候，返回的数据会是json格式的，那么就需要用
`$output_array = json_decode($output,true);`
如果使用json_decode($output)解析的话，将会得到object类型的数据。
- 可以设置cookie来爬网页，这样可以代替登录啊，就不用用户名，密码啥的啦
cookie怎么获得呢？浏览器F12->Resources->Cookies;
一个个地复制，以"name=value;name=value;..."这样的形式组成一个cookie字符串。
接下来就可以使用该cookie字符串来发送请求。
`curl_setopt($ch, CURLOPT_COOKIE, $this->config_arr['user_cookie']);`
- [php爬知乎网页](http://www.chinaz.com/web/2015/0930/452838.shtml)