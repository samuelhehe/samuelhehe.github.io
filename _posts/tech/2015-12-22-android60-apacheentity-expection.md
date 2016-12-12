---
layout: post
title: Android6.0 sdk  apache 相关包移除异常解决
category: 技术
tags: [android6.0 exception ]
keywords:  Android6.0 apache entity exception 
description: Android6.0 apache 相关包移除异常解决
---

Android6.0 apache 相关包移除异常解决
====================================

android6.0 sdk  apache httpclient 相关包移除后 导致相关依赖包的库不能使用，这里列一下异常和解决方法。

官网发布的changes 我当时没有怎么关注，后来在使用友盟统计的时候碰到了这个问题，才猛地想起来是这个原因
看一下log截图：

<br/>
 ![image](https://raw.githubusercontent.com/samuelhehe/samuelhehe.github.io/master/res/android6_apache_entity_error.png")
将 下边代码配置gradle 文件 dependencies 中

<br/>
 ![image](https://raw.githubusercontent.com/samuelhehe/samuelhehe.github.io/master/res/android6_apache_entity_config.png")

问题已解决， 还是希望以后多注意下依赖包的依赖项，并且考虑系统版本。
<br/>
附 官网的相关链接，[https://developer.Android.com/preview/behavior-changes.html](https://developer.Android.com/preview/behavior-changes.html)

