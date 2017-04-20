---
layout: post
title: Android 7.0  groupview cast exception
category: 技术
tags: [android 7.0 exception ]
keywords:  Android 7.0  groupview cast exception
description: Android 7.0  groupview 强制转换相关异常解决
---

Android 7.0  groupview 强制转换相关异常
====================================

android 7.0 ListView 添加FootView 动态设置 LayoutParams  导致强制转换异常，这里列一下异常和解决方法。
（这里的错误仅在7.0及以上出现，其他版本暂无发现该问题）

官网发布的changes 我当时没有怎么关注，后来在使用友盟统计的时候碰到了这个问题，才猛地想起来是这个原因
看一下log截图：

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
 ![image](https://raw.githubusercontent.com/samuelhehe/samuelhehe.github.io/master/res/android6_apache_entity_config.png")

问题已解决， 还是希望以后多注意下依赖包的依赖项，并且考虑系统版本。
<br/>
附 官网的相关链接，[https://developer.Android.com/preview/behavior-changes.html](https://developer.Android.com/preview/behavior-changes.html)

