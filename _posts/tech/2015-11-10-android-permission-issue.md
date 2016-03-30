---
layout: post
title: Android 5.0 权限冲突导致安装失败的问题
category: 技术
tags: [Android,  permission install fail  ]
keywords: Android5.0 ,permission install failure
description: Android5.0 permission install failure
---

> 最近把运行的项目放在云测上测试, 发现Android 5.0 系统上频频出现安装失败的问题, 最先考虑的问题是JNI cpu兼容问题,更换so文件之后,便排除了该问题. 具体错误是Android5.0 应用权限导致的安装问题.

# 问题描述

  ``` Failure [INSTALL_FAILED_DUPLICATE_PERMISSION perm=android.permission.BAIDU_LOCATION_SERVICE pkg=com.bmcc.ms.ui] ```
    由于项目中引用的有百度地图SDK jar 包, 所以直接定位百度地图的引用,以及各种配置. 结果并没有发现什么异常,  而且大部分手机测试结果是没有问题的.
于是在网络上找到了一篇帖子, 是在百度贴吧中找到的, 晒出来看一下:[http://tieba.baidu.com/p/3237226241](http://tieba.baidu.com/p/3237226241)
仔细阅读后发现,这是Android 5.0加强权限控制导致的, 让我继续拿百度Google 得到原因. 

# 解决办法

## 经过查找了Google,发现了一些提示, 并且在国内的网站上也找到了类似的提示:
	[http://stackoverflow.com/questions/26491251/android-5-0-lollipop-app-install-shows-unknown-error-code-during-application-ins](http://stackoverflow.com/questions/26491251/android-5-0-lollipop-app-install-shows-unknown-error-code-during-application-ins) 	[http://stackoverflow.com/questions/27043933/install-failed-duplicate-permission-c2d-message](http://stackoverflow.com/questions/27043933/install-failed-duplicate-permission-c2d-message)
	[http://www.weste.net/2015/07-27/104873.html](http://www.weste.net/2015/07-27/104873.html) 
	[http://lmbj.net/blog/solve-android-5.0-installed-app-failed-problem/](http://lmbj.net/blog/solve-android-5.0-installed-app-failed-problem/) 
		
	**正要安装的App的自定义权限与手机上已有App的自定义权限名字相同，但两个App具有不同的签名信息导致安装失败。**
	很明显, 这样就意味着,我们安装了同一应用权限的签名的应用的话, 只能N选一了, 例如说安装了引用了百度地图sdk的应用,也就只能安装一个, 怪不得安装不成功, 问题找到了. 
	
## 继续查找:
    这么大的问题,百度肯定有解决的办法,继续查找百度地图SDK文档, 找到了答案:
+个链接: [http://developer.baidu.com/map/index.php?title=android-locsdk/guide/v5-0](http://developer.baidu.com/map/index.php?title=android-locsdk/guide/v5-0)
<br>
** 【重要提醒】**
** 定位SDKv3.1版本之后，以下权限已不需要，请取消声明，否则将由于Android 5.0多帐户系统加强权限管理而导致应用安装失败。 <uses-permission android:name="android.permission.BAIDU_LOCATION_SERVICE"></uses-permission> **

# 总结

 这也没什么好总结的, 总之我觉得还有有点要记住的:
 1. 自己不要瞎定义权限, 搞不好,又被Google的另一个东西给限制了. 不过总的来说, 对我Android 开发的也未尝不是一件好事.
 2. 接下来就是不要瞎引用第三方,特别是小公司的jar包, 搞不好,自己的App就栽了, 好在百度也不算是个小公司,靠得住.开发者甚幸, 民族甚幸
 3. 没了, 自己瞎写的,仅供个人记录之用, 参考资料,大概就是上边的几个链接,谢谢百度地图. 




