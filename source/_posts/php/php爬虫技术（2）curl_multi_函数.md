---
title: php爬虫技术（2）---curl_multi_*()函数
date: 2016-05-16
tags: [php]
category: [php]
---
爬网页是一个庞大的工程，如果用curl来实现，需要加上多线程才能加快进度，但是我们知道php是没有多线程的，如果强行要用多线程，需要加上拓展，需要重新编译php。但是万能的php还是为curl中提供了一组函数，完美的解决了这个效率的问题
<!--more-->
## 函数简介
  因为php是单线程的，为了解决爬虫效率的问题，所以就出了`curl_multi_()`函数簇，底层网上很多的博客说是多线程，但是也没有见到他们说有依据。而我更加偏向是I/O复用+多线程，因为这个函数簇有点类似与epoll网络模型。多线程去爬网页，I/O复用来监听线程是否爬完。
## 基本用法
  网络上有很多的博客，说curl_multi并不好用，因为它需要等所有加进去的网页都爬完了，才能够去读取网页的信息。
所以，万一期中某个网页爬下去需要10s，其他的都需要1s，那么其他的网页爬完后就需要等这个10s的网页爬完才能去读取所有的网页的信息。
是实际情况真的是这样吗？我似乎找到了捷径。能够每次去判断有没有爬完的网页，有的话就读取该网页的内容，然后把已经读取完的网页句柄删除，添加新的句柄。
```
$ch1 = curl_init();
$ch2 = curl_init();
$ch3 = curl_init();

// 设置URL和相应的选项
curl_setopt($ch3, CURLOPT_URL, "http://192.168.72.128/net_spider/");
curl_setopt($ch3,CURLOPT_RETURNTRANSFER,true);
curl_setopt($ch3, CURLOPT_USERAGENT, "user-agent:Mozilla/5.0
 (Windows NT 5.1; rv:24.0) Gecko/20100101 Firefox/24.0");
curl_setopt($ch1, CURLOPT_URL, "http://192.168.72.128/net_spider/");
curl_setopt($ch1,CURLOPT_RETURNTRANSFER,true);
curl_setopt($ch1, CURLOPT_USERAGENT, "user-agent:Mozilla/5.0
 (Windows NT 5.1; rv:24.0) Gecko/20100101 Firefox/24.0");
curl_setopt($ch2, CURLOPT_URL, "http://192.168.72.128/net_spider/demo/demo_post.php");
curl_setopt($ch2,CURLOPT_RETURNTRANSFER,true);
curl_setopt($ch2, CURLOPT_USERAGENT, "user-agent:Mozilla/5.0
 (Windows NT 5.1; rv:24.0) Gecko/20100101 Firefox/24.0");
$curl_arr = array($ch1,$ch2,$ch3);

// 创建批处理cURL句柄
$mh = curl_multi_init();
// 增加2个句柄
$active = null;
do{
  $cme = curl_multi_exec($mh, $active);

  //通过curl_multi_info_read判断是否有消息能够读取
  while ($done = curl_multi_info_read($mh)){
  $tmp_result = curl_multi_getcontent($done['handle']);
  curl_multi_remove_handle($mh, $done['handle']);
  }
 //curl_multi_select防止CPU 100%   ??????  
 if (($cme == 0 && $active == 0)&&(curl_multi_select($mh)== -1)) {
     usleep(1);
  }
  //这里用来表示移动4个线程去爬网页，如果有线程空闲，则添加网页句柄
  if($active < 4){
      if(!empty($curl_arr)){
         curl_multi_add_handle($mh,$curl_arr[0]);
         array_splice($curl_arr,0,1);
         $active++;
  }
 }
}while($active);
curl_multi_close($mh);
//$active这个变量，代表还有多少个网页没有爬完，如果都爬完了，那么就为0；
```
### 感受
- 网上的东西不一定是真的，别人总结的博客可能是错的，需要自己思考思考
- 多敲一敲代码，试试功能，也许会有惊喜，就像这个函数，网上都说需要等网页都爬完才能开始读取内容，但是我通过自己还是能够实现自己想要的功能。