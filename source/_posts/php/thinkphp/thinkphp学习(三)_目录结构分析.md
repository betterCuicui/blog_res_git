---
title: thinkphp学习(三)_目录结构分析
date: 2016-06-14 11:45:23
tags: [php,thinkphp]
category: [php]

---
简单分析thinkphp的目录结构
<!--more-->
![](/public/image/php/thinkphp3/QQ_u622A_u56FE20160523125709.png)
看到图中生成的这些文件，来分析一下文件
### common :用来存放当前项目的公共函数
### conf ：存放配置文件
### Lang ：存放语言包
### Lib： 存放控制器与模型
### RunTime ：存放当前项目的运行时的文件
![](/public/image/php/thinkphp3/QQ_u622A_u56FE20160523130448.png)
- Cache:放模版的缓存
- Data：数据的目录
- Logs：日志
- Temp：数据缓存
- runtime.php：编译后记载的文件

### Tpl:存放模版文件
## 怎么体现MVC呢？？？
#### MC在Lib中
![](/public/image/php/thinkphp3/QQ_u622A_u56FE20160523130912.png)
- Action:控制器
- Behavior:行为管理的目录
- Model:模型文件
- Widget:组件

#### V在Tpl中