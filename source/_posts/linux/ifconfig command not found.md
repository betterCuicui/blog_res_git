---
title: ifconfig command not found
date: 2016-09-04 10:58:48
tags: linux
category: linux
---
新机器，想查一下ip，然后敲击ifconfig，结果惊讶的发现，屏幕上竟然提示：ifconfig命令找不到了？？？
<!--more-->
- 首先`whereis ifconfig`找一个这个命令的位置。发现在 `/sbin/ifconfig`
- 然后再`echo $PATH`,发现环境变量里面并没有 `/sbin/ifconfig`。所以才不能执行ifconfig这个命令，但是可以直接  `/sbin/ifconfig` 这样执行
- 也可以通过`export PATH=$PATH:/sbin`。然后再直接ifconfig就可以直接使用这个命令了。
-最后，当然当别的命令也找不到的时候，也可以使用这个方法来试试解决。