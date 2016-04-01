---
layout: post
title: Android OnLowMemory和OnTrimMemory 简单比较
category: 技术
tags: [Android, OnLowMemory OnTrimMemory ]
keywords:  Android OnLowMemory和OnTrimMemory 
description: Android OnLowMemory和OnTrimMemory 简单比较
---

Android OnLowMemory和OnTrimMemory 简单比较
====================================

* 1. OnLowMemory

OnLowMemory是Android提供的API，在系统内存不足，所有后台程序（优先级为background的进程，不是指后台运行的进程）都被杀死时，系统会调用OnLowMemory。系统提供的回调有：Application/Activity/Fragementice/Service/ContentProvider

除了上述系统提供的API，还可以自己实现ComponentCallbacks，通过API注册，这样也能得到OnLowMemory回调。例如：

```
public static class MyCallback implements ComponentCallbacks {

@Override
<br/>
public void onConfigurationChanged(Configuration arg) {

}

@Override
<br/>
public void onLowMemory() {

//do release operation

}

}
```

然后，通过Context.registerComponentCallbacks ()在合适的时候注册回调就可以了。通过这种自定义的方法，可以在很多地方注册回调，而不需要局限于系统提供的组件。

* 2.  OnTrimMemory

OnTrimMemory是Android 4.0之后提供的API，系统会根据不同的内存状态来回调。系统提供的回调有：Application/Activity/Fragement/Service/ContentProvider

OnTrimMemory的参数是一个int数值，代表不同的内存状态：

TRIM_MEMORY_COMPLETE：内存不足，并且该进程在后台进程列表最后一个，马上就要被清理
TRIM_MEMORY_MODERATE：内存不足，并且该进程在后台进程列表的中部。
TRIM_MEMORY_BACKGROUND：内存不足，并且该进程是后台进程。
TRIM_MEMORY_UI_HIDDEN：内存不足，并且该进程的UI已经不可见了。
以上4个是4.0增加

 
TRIM_MEMORY_RUNNING_CRITICAL：内存不足(后台进程不足3个)，并且该进程优先级比较高，需要清理内存
TRIM_MEMORY_RUNNING_LOW：内存不足(后台进程不足5个)，并且该进程优先级比较高，需要清理内存
TRIM_MEMORY_RUNNING_MODERATE：内存不足(后台进程超过5个)，并且该进程优先级比较高，需要清理内存
以上3个是4.1增加
系统也提供了一个ComponentCallbacks2，通过Context.registerComponentCallbacks()注册后，就会被系统回调到。

**OnLowMemory和OnTrimMemory的比较**

* 1，OnLowMemory被回调时，已经没有后台进程；而onTrimMemory被回调时，还有后台进程。
* 2，OnLowMemory是在最后一个后台进程被杀时调用，一般情况是low memory killer 杀进程后触发；而OnTrimMemory的触发更频繁，每次计算进程优先级时，只要满足条件，都会触发。
* 3，通过一键清理后，OnLowMemory不会被触发，而OnTrimMemory会被触发一次
