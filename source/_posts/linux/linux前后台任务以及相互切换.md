---
title: linux前后台进程以及相互切换
date: 2016-09-04 10:58:48
tags: linux
toc: true
category: linux
---
如果有一个大大的脚本需要在你的服务器上跑，所以这个时候你打开了secureCRT，然后连接你的服务器，打开一个会话。然后你执行了你的脚本。但是这个时候，你傻眼了。接下来你该怎么办，这个脚本需要一直在跑，而你打开的会话到时候是需要关闭的。当你关闭会话的时候，你的脚本就会发现突然断了，所以你知道，你脚本的生命周期，竟然只是这次会话。你打开另外一个会话竟然发现不了这个运行的脚本，所以你的脚本竟然并不是在后台跑的。那么我们该怎么办呢？
<!--more-->

### 一、&命令
&命令特别简单，仅仅需要在你执行的脚本后添加 &,那么你的程序就变成后台程序了
eg：`php temp.php &`

### 二、接下来我们了解一下更为强大的方法，首先我们来了解一些命令
- ctrl + z
这个命令是把前台正在执行的命令放在后台的作业队列中，并且暂停
- jobs
查看当天后台有多少运行的命令
- bg
把后台的一个暂停的命令，使其继续执行。如果后台中有多个命令，可以用bg jobnumber 将选中的命令调出，jobnumber是通过jobs命令查到的后台正在执行的命令的序号(不是pid)
- fg
将后台中的命令调至前台继续运行
如果后台中有多个命令，可以用 fg jobnumber将选中的命令调出，jobnumber是通过jobs命令查到的后台正在执行的命令的序号(不是pid)

### 三、好了，既然命令学会，那么我们就可以开始使用
- 首先我们执行一个shell脚本 `/root/bin/rsync.sh`
- 使用Ctrl-Z挂起这个程序，这个时候我们可以看到系统提示：
`[1]+ Stopped /root/bin/rsync.sh` 这个代表这个这个命令的编号是  1  。
- 我们把命令调度到后台执行：
```
bg 1
[1]+ /root/bin/rsync.sh &
```
- 使用jobs查看正在运行的任务
声明以下，jobs只会在同一个会话中能查询到，也就是说查询不到别的会话的正在运行的任务的。如果你要强行查询只能通过`ps -ef | grep 命令`的方式来查询.
`[1]+ Running /root/bin/rsync.sh &`
- 如果这个时候想把这个任务调会前台，可以使用
```
#fg 1
/root/bin/rsync.sh
```
这样，你的任务又出现在了你的secureCRT屏幕上了。

### 四、脱离session的进程->nohup
发现一个问题，当使用前面的方法，进程掉到后台了，但是生命周期竟然还是当前的会话。所以当当前的session关闭了，即如果发现进程还是没有了。
- `nohup Command [ Arg ... ] [　& ]`

然后发现了nohup这个命令，它可以使进程变成守护进程，并且同样可以通过jobs来查看。
一般的形式是`nohup command &`
如果使用nohup命令提交作业，那么在缺省情况下该作业的所有输出都被重定向到一个名为nohup.out的文件中，除非另外指定了输出文件：
nohup command > myout.file 2>&1 &
在上面的例子中，0 – stdin (standard input)，1 – stdout (standard output)，2 – stderr (standard error) ；
2>&1是将标准错误（2）重定向到标准输出（&1），标准输出（&1）再被重定向输入到myout.file文件中。
使用 jobs 查看任务。
使用 fg %n　关闭。

#### 资料：
[百度百科->nohup](http://baike.baidu.com/link?url=JgcGIGgeJsx4uZxT6vqVUpUJAMysLVXUwzp8irmSBiz0MZ5dfFDK1JUpZ6TCX9LWuxjYq5GIZt8S7exR2kr2Cq)