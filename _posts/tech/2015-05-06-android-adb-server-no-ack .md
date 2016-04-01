---
layout: post
title: Android port ADB server didn't ACK
category: 技术
tags: [Android, ADB serverdidn't ACK 5037 ]
keywords:  ADB server didn't ACK

description:  ADB server didn't ACK Android 端口被占用
---

>  在AndroidStudio 中的run 时碰到的问题 ，  说让检查一下 ADB 没有响应 ， 让手动启动adb server   问题已解决，此刻记录一下，备忘  


Android ADB server  didn't ACK  端口被占用快速解决
====================================


* 问题描述：在AndroidStudio 中的run 时碰到的问题 ，  说让检查一下 ADB 没有响应 ， 让手动启动adb server   问题已解决，此刻记录一下，备忘 

找到 出现错误

```
[2014-01-08 14:00:07 - adb] ADB server didn't ACK
[2014-01-08 14:00:07 - adb] * failed to start daemon *
[2014-01-08 14:07:24 - adb] ADB server didn't ACK
[2014-01-08 14:07:24 - adb] * failed to start daemon *
```

以上问题，总体来说， ADB Server没有响应 无法启动 daemon  应该是守护进程 。

网上搜索之后找到的办法 ， http://www.cnblogs.com/fengzhblog/p/3511434.html

* 解决方法：先找到adb服务是否绑定端口出现问题，如果真的是绑定端口出现问题，找出该端口被哪个进程占据，最后杀掉这个进程。

1. 首先通过CMD启动adb服务。这个时候会提示启动失败。

```
C://user/superuser>adbstart-server
adbserver is out of date. killing...ADBserver didn't ACK* failed to start daemon *

```

2. 服务启动失败的原因有很多，但一般是端口绑定失败。我们来查看一下端口绑定信息。如图所示，真的是端口绑定出了问题。

``` 
 C://user/superuser>adb nodaemon servercannot bind 'tcp:5037'

```

3. 我们来看看哪个服务占用了这个端口。这里面有2个进程占用了这个端口。
C://user/superuser>netstat -ano | findstr "5037"
  TCP    127.0.0.1:5037         0.0.0.0:0              LISTENING    6196
在控制台下杀死在5037端口监听的进程 ，进程ID 为 6196 的进程

```
C://user/superuser>taskkill /pid 6196
```

错误: 无法终止 PID 为 6196 的进程。原因: 只能强行终止这个进程(带 /F 选项)。

```
C://user/superuser>taskkill /pid 6196/f
```

成功: 已终止 PID 为 6196的进程。
杀死造成问题的进程，再执行前面的命令 非暴力不合作啊 ，加个f 弄死它， 再次查询占用该端口的进程信息， 发现没有，  重新启动。

```
C://user/superuser>netstat -ano | findstr "5037"
C://user/superuser>adb nodaemon server
```

结果显示此问题已解决， run  OK ！

* 总结： 时刻注意细节， 用360 通过pid 查到，该进程就是个搜狐影音的一个adb 进程， 可能就是通过这个进程与手机进行连接的，暂时被这家伙占用了。 事后果断把搜狐影音卸载，哈哈哈~~  没事学习学习 cmd 挺好，这玩意儿不用不知道，用时才发现重要，还有taskill 命令 ， 哇咔咔

 
