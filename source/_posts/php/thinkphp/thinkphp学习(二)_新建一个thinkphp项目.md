---
title: thinkphp学习（二）_新建一个thinkphp项目
date: 2016-06-14 10:45:23
tags: [php,thinkphp]
category: [php]

---

简单介绍新建一个thinkphp项目的步骤
<!--more-->
- 1、下载thinkphp框架
- 2、在框架新建一个index.php的入口文件
```
<?php
//定义项目名称
define('APP_NAME','TEST');
//定义项目的目录
define('APP_PATH','./TEST/');
//包含项目的框架
require('./ThinkPHP/ThinkPHP.php');
```
- 3、localhost/项目名称
- 4、生成了你的项目文件，eg
![](/public/image/php/thinkphp2/QQ_u622A_u56FE20160523125654.png)
![](/public/image/php/thinkphp2/QQ_u622A_u56FE20160523125709.png)
- 5、over