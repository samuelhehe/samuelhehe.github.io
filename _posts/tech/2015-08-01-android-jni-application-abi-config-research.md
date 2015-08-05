---
layout: post
title: Android_JNI_ABI兼容问题
category: 技术
tags: [Android, jni]
keywords: Android,abi,jni
description: android jni 引用so包种类，application.md 配置兼容问题
---

> 出了错，改正，记录下来，保证下次不再犯。 但是事情发生了，发生的原因，如何适配兼容这些问题还是要搞清楚的。

先粘一个官方链接：
<a href="http://developer.android.com/ndk/guides/application_mk.html">http://developer.android.com/ndk/guides/application_mk.html</a>

### 科普

ABI=Application Binary Interface

在我们android APK的根目录有一个 libs文件夹，此文件夹下基本包含了armeabi 和armeabi-v7a， 甚至还有两个 mips ， x86文件夹，  我们的c代码编译成的本地库（各种.so）就会放在这几个文件夹其中的一个。
那armeabi-v7a 与 armeabi ，x86 ， mips  有什么区别，都是什么意思呢？

armeabi和armeabi-v7a是表示cpu的类型，我们知道一般的手机或平板都是用arm的cpu（mips的市场份额少，基本没有地位了），不同的cpu的特性不一样，


armeabi 就是针对普通的或旧的arm v5 cpu，

armeabi-v7a是针对有浮点运算或高级扩展功能的arm v7 cpu。 


在android.mk里可配置以下宏：

```
TARGET_CPU_API := armeabi
APP_ABI := armeabi
```

在application.mk里可配置以下宏：

```
APP_ABI := armeabi armeabi-v7a mips x86
```

或者使用all 代替。

看一下官方文档 Application.md 中 table 1 中参数， 左侧是指令集，右侧是需要配置的value：

armeabi 、armeabi-v7a 和x86是编译 NDK 库时，可以使用三种支持的应用二进制接口(ABI)：

armeabi

默认选项，
支持基于 ARM* v5TE 的设备
支持软浮点运算（不支持硬件辅助的浮点计算）
支持所有 ARM* 设备

armeabi-v7a 
支持基于 ARM* v7 的设备  
支持硬件 FPU 指令
支持硬件浮点运算

x86 支持基于硬件的浮点运算的 IA-32 指令集

mips  支持二进制接口


### 如何配置

> 既然armeabi通用，为什么要使用其它指令集呢？

虽然armeabi通用性强，但速度慢，而v7a能充分发挥v7a CPU的能力。
armeabi就是针对普通的或旧的armcpu，
armeabi-v7a是针对有浮点运算或高级扩展功能的arm cpu。x86、mips同理。

armeabi-v7a确实是可以兼容armeabi的，而v7a的CPU支持硬件浮点运算，目前绝大对数设备已经是v7a了，所以为了性能上的更优，就不要为了兼容放到armeabi。
现在市面上很多追求高性能的手机，在应用程序编译时，会直接到libs下找armeabi-v7a 目录下so库，如果找不到就不会去找兼容包，armeabi 。
 为此我还曾出了个错误，
见ISSUE：<a href="https://github.com/samuelhehe/androiddev_issues/issues/7">java.lang.unsatisfiedlinkerror native method not found JNI armeabi so 包引用问题</a>


### 实际项目运用中总结:
 如果项目只包含了 armeabi，那么在所有Android设备都可以运行；
 如果项目只包含了 armeabi-v7a，除armeabi架构的设备外都可以运行；
 如果项目只包含了 x86，那么armeabi架构和armeabi-v7a的Android设备是无法运行的； 
 如果同时包含了 armeabi， armeabi-v7a和x86，所有设备都可以运行，程序在运行的时候去加载不同平台对应的so，这是较为完美的一种解决方案，同时也会导致包变大。






















