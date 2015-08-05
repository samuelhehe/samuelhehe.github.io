---
layout: post
title: Android-jni 引用so包种类探究
category: 技术
tags: [Android, jni]
keywords: Android,abi,jni
description: android jni 引用so包种类，application.md 配置问题探究
---

> 出了错，改正，记录下来，保证下次不再犯。 但是事情发生了，发生的原因，如何适配这些问题还是要搞清楚的。

查了一大圈，找到了一个可靠的文档，还有文档中的一份重要的截图。
<a href="http://developer.android.com/ndk/guides/application_mk.html">http://developer.android.com/ndk/guides/application_mk.html</a>
![spic](https://cloud.githubusercontent.com/assets/5669999/9082112/55c8399c-3b94-11e5-9caf-231b9c1df030.png)

图中圈中的部分就是目前所适用的cpu架构，下部，官方文档所推荐的四种型号。
至于64位的cpu架构，我觉得暂时没有必要考虑， 目前世界各大手机厂商没有达成一致，没有推广开。
即使有64位CPU的手机，也会兼容x86 CPU架构 的abi 配置 。
还是慢慢的等待他们达成一致再说吧。
总结：个人觉得如果so文件不大的话，或者apk打包大小没有限制的话，那就把armeabi armeabi-v7a ，mips ， x86 都给打包了，这是最为完美的解决方案。
