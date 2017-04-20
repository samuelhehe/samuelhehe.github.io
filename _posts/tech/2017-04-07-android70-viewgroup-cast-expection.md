---
layout: post
title: Android 7.0  viewgroup cast exception
category: 技术
tags: [android 7.0 exception ]
keywords:  Android 7.0  viewgroup cast exception
description: Android 7.0  viewgroup 强制转换相关异常解决
---

Android 7.0  viewgroup 强制转换相关异常
====================================

android 7.0 ListView 添加FootView 动态设置 LayoutParams  导致强制转换异常，这里列一下异常和解决方法。
（这里的错误仅在7.0及以上出现，其他版本暂无发现该问题）

该部分代码是出现在ListView  动态添加FootView的时候。 

用代码添加了一个View ， LayoutParams 没有使用ListView父类AbsListView而是使用ViewGroup.LayoutParams 引起的。


具体的异常信息如下：
<br/>

```

java.lang.ClassCastException: android.view.ViewGroup$LayoutParams cannot be cast to android.widget.AbsListView$LayoutParams
	at android.widget.ListView.removeUnusedFixedViews(ListView.java:1908)
	at android.widget.ListView.layoutChildren(ListView.java:1769)
	at android.widget.AbsListView.onLayout(AbsListView.java:2162)
	at android.view.View.layout(View.java:17637)
	at android.view.ViewGroup.layout(ViewGroup.java:5575)
	at android.widget.RelativeLayout.onLayout(RelativeLayout.java:1079)
	at android.view.View.layout(View.java:17637)
	at android.view.ViewGroup.layout(ViewGroup.java:5575)
	at android.widget.RelativeLayout.onLayout(RelativeLayout.java:1079)
	at android.view.View.layout(View.java:17637)
	at android.view.ViewGroup.layout(ViewGroup.java:5575)
	at android.widget.FrameLayout.layoutChildren(FrameLayout.java:323)
	at android.widget.FrameLayout.onLayout(FrameLayout.java:261)
	at android.view.View.layout(View.java:17637)
	at android.view.ViewGroup.layout(ViewGroup.java:5575)
	at android.widget.LinearLayout.setChildFrame(LinearLayout.java:1741)
	at android.widget.LinearLayout.layoutVertical(LinearLayout.java:1585)
	at android.widget.LinearLayout.onLayout(LinearLayout.java:1494)
	at android.view.View.layout(View.java:17637)
	at android.view.ViewGroup.layout(ViewGroup.java:5575)
	at android.widget.FrameLayout.layoutChildren(FrameLayout.java:323)
	at android.widget.FrameLayout.onLayout(FrameLayout.java:261)
	at android.view.View.layout(View.java:17637)
	at android.view.ViewGroup.layout(ViewGroup.java:5575)
	at android.widget.LinearLayout.setChildFrame(LinearLayout.java:1741)
	at android.widget.LinearLayout.layoutVertical(LinearLayout.java:1585)
	at android.widget.LinearLayout.onLayout(LinearLayout.java:1494)
	at android.view.View.layout(View.java:17637)
	at android.view.ViewGroup.layout(ViewGroup.java:5575)
	at android.widget.FrameLayout.layoutChildren(FrameLayout.java:323)
	at android.widget.FrameLayout.onLayout(FrameLayout.java:261)
	at com.android.internal.policy.DecorView.onLayout(DecorView.java:726)
	at android.view.View.layout(View.java:17637)
	at android.view.ViewGroup.layout(ViewGroup.java:5575)
	at android.view.ViewRootImpl.performLayout(ViewRootImpl.java:2346)
	at android.view.ViewRootImpl.performTraversals(ViewRootImpl.java:2068)
	at android.view.ViewRootImpl.doTraversal(ViewRootImpl.java:1254)
	at android.view.ViewRootImpl$TraversalRunnable.run(ViewRootImpl.java:6337)
	at android.view.Choreographer$CallbackRecord.run(Choreographer.java:874)
	at android.view.Choreographer.doCallbacks(Choreographer.java:686)
	at android.view.Choreographer.doFrame(Choreographer.java:621)
	at android.view.Choreographer$FrameDisplayEventReceiver.run(Choreographer.java:860)
	at android.os.Handler.handleCallback(Handler.java:751)
	at android.os.Handler.dispatchMessage(Handler.java:95)
	at android.os.Looper.loop(Looper.java:154)
	at android.app.ActivityThread.main(ActivityThread.java:6119)
	at java.lang.reflect.Method.invoke(Native Method)
	at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:886)
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:776)


```
 
<br/>

很简洁的异常信息，除了系统sdk 中的代码错误，并没有发现自己代码中的异常部分。 

出现的机型：

而且是Android 7.0 的新机器才会出现的问题（华为P9， 小米 5c 等 安装Android7.0 系统及其相关7.0 定制rom）


解决办法 ：

很无奈的办法

<b>注意将ViewGroup.LayoutParams 改成 ListView 需要的 AbsListView.LayoutParams  </b>

看图：
<br/>
![image](https://raw.githubusercontent.com/samuelhehe/samuelhehe.github.io/master/res/android7.0_listview_viewgroup_cast_abslistview_exception.png")

问题已解决， 具体问什么Android7.0 才出现这样的问题。 详细的原因还没有深入研究，随后续上（个人估计是7.0 做了细致的转化限制，优化了没必要的多态性能损耗）。 



