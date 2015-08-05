---
layout: post
title: Android 横竖屏切换生命周期
category: 技术
tags: 横竖屏切换
keywords: Android ,横竖屏切换,生命周期
description: 开发中的小总结，Android activity 横竖屏切换生命周期 
---

> 学好Android 首要研究Activity的生命周期， 研究一下Activity生命周期的横屏竖屏切换回调函数
测试环境 ： eclipse + Android手机

1、新建一个 Activity，并将把各个生命周期打印出来。  

2、运行Activity，得到如下信息 

```
onCreate------ 

onStart------ 

onResume------ 
```

3、切换成横屏时 

```
onSaveInstanceState------ 

onPause------ 

onStop------ 

onDestroy------ 

onCreate------ 

onStart------ 

onRestoreInstanceState------ 

onResume------ 

```

4、再切换成竖屏时，发现打印了两次相同的log 

```
onSaveInstanceState------ 

onPause------ 

onStop------ 

onDestroy------ 


onCreate------ 

onStart------ 

onRestoreInstanceState------ 

onResume------ 

onSaveInstanceState------ 

onPause------ 

onStop------ 

onDestroy------ 


onCreate------ 

onStart------ 

onRestoreInstanceState------ 

onResume------ 


```
5、修改AndroidManifest.xml，添加 android:configChanges="orientation" 后切换屏幕

```
onSaveInstanceState------ 

onPause------ 

onStop------ 

onDestroy------ 

onCreate------ 

onStart------ 

onRestoreInstanceState------ 

onResume------ 

```

6、再切换屏幕，发现不会再打印相同信息，但多打印了一行onConfigChanged 

```

onSaveInstanceState------ 

onPause------ 

onStop------ 

onDestroy------ 

onCreate------ 

onStart------ 

onRestoreInstanceState------ 

onResume------ 

onConfigurationChanged------ 

```

7、配置文件中的 android:configChanges="orientation" 改成  android:configChanges="orientation|keyboardHidden"，切换屏幕，就只打印 

```
onConfigurationChanged------ 

onConfigurationChanged------ 

```
8、执行步骤切换屏幕

```
onConfigurationChanged------ 

onConfigurationChanged------ 

```

## 总结： 

1、不设置Activity的android:configChanges时，切屏会重新调用各个生命周期， 
切横屏时会执行一次，切竖屏时会执行两次 

2、设置Activity的android:configChanges="orientation"时，切屏还是会重新调 
用各个生命周期，切横、竖屏时只会执行一次 

3、设置Activity的android:configChanges="orientation|keyboardHidden"时， 
切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法 


4、当前Activity产生事件弹出Toast和AlertDialog的时候Activity的生命周期不会有改变 

Activity运行时按下HOME键和被完全覆盖是一样的：

```
onSaveInstanceState ------ 

onPause ------ 

onStop  ------

onRestart ------

onStart-------

onResume
``` 
Activity未被完全覆盖只是失去焦点：

```
onPause-------

onResume ------

```

5、运行时生命周期的变化官方文档[http://developer.android.com/guide/topics/resources/runtime-changes.html](http://developer.android.com/guide/topics/resources/runtime-changes.html)

6、至于 android:configChanges="orientation"就是 Activity 中 onConfigurationChanged 方法需要监听到的事件类型。 
请参考 [Android之屏幕方向改变以及onConfigurationChanged事件](http://www.linuxidc.com/Linux/2011-06/36828.htm)
