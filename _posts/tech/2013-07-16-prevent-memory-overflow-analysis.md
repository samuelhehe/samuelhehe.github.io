---
layout: post
title: Android 防止内存泄露浅析
category: 技术
tags: memory overflow
keywords: Android ,Memory ,Overflow
description: 开发中的小总结，防止内存泄露的简单分析
---

> Android的虚拟机是基于寄存器的Dalvik， 它的最大堆大小一般是16M。但是Android采用的是Java语言编写，所以在很大程度上，Android的内存机制等同于Java的内存机制，在刚开始开发的时候，内存的限制问题会给我们带来内存溢出等严重问题。 开发中程序猿们开始疯狂的寻找各种各样导致内存溢出的原因，我也来个小小的总结。


在我们不使用一些内存的时候，我们要尽量在Android或者其他平台上避免在运行其他程序时，保存必要的状态，使得一些死进程所带来的内存问题，应该尽量在关闭程序或者保存状态的时候释放掉，这样能提高系统在运行方面的流畅性。

## Android的内存主要表现在：

 在Android平台上，长期保持一些资源的引用，造成一些内存不能释放，带来的内存泄露问题很多。比如：Context（下文中提到的Activity都是Context），在一些你需要保持你的首个类对象状态，并且把状态传入其他类对象中时，这样消除掉首个类对象之前，你必须先把接收类对象释放掉。需要注意一点的是：因为在Java或者Android内存机制中，顶点的结点释放前必须保证其他对象没有调用才能被系统GC回收释放。
 
### 我们来看一段代码：

```
@Override  
protected void onCreate(Bundle state) {  
      super.onCreate(state);  
      TextViewlabel = new TextView(this);  
      label.setText("Leaksare bad");  
      setContentView(label);  
}  
```

### 分析：

这个代码的意思就是我们把一个TextView的实例加载到了我们正在运行的Activity（Context）当中，因此，通过GC回收机制，我们知道，要释放Context，就必须先释放掉引用他的一些对象。如果没有，那在要释放Context的时候，你会发现会有大量的内存溢出。所以在不小心的情况下内存溢出是一件非常容易的事情。 
保存一些对象时，同时也会造成内存泄露。
最简单的比如说位图（Bitmap），比如说：在屏幕旋转时，会破坏当前保持的一个Activity状态，并且重新申请生成新的Activity，直到新的Activity状态被保存。

### 下面一段代码：

```
private static Drawable sBackground;  

@Override  
protected void onCreate(Bundle state) {  
	super.onCreate(state);  
	TextView label = new TextView(this);  
	label.setText("Leaks are bad");  
    if (sBackground == null) {  
         sBackground =getDrawable(R.drawable.large_bitmap);  
	}  
	label.setBackgroundDrawable(sBackground);  
	setContentView(label);  
} 
 
```


### 分析：

它的内存泄露很容易出在屏幕转移的方向上。虽然我们会发现没有显示的保存Context这个实例，但是当我们把绘制的图连接到一个视图的时候，Drawable就会将被View设置为回调，这就说明，在上述的代码中，其实在绘制TextView到活动中的时候，我们已经引用到了这个Activity。链接情况可以表现为：Drawable->TextView->Context。
所以在想要释放Context的时候，其实还是保存在内存中，并没有得到释放。
如何避免这种情况：主要在于。线程最容易出错。大家不要小看线程，在Android里面线程最容易造成内存泄露。线程产生内存泄露的主要原因在于线程生命周期的不可控。

### 下面有一段代码：

```
public class MyTest extends Activity {  
     @Override  
     public void onCreate(BundlesavedInstanceState) {  
         super.onCreate(savedInstanceState);  
         setContentView(R.layout.main);  
         new MyThread().start();  
     }  
     private class MyThread extends Thread{  
         @Override  
         public void run() {  
            super.run();  
            //do somthing  
         }  
     }  
}  

```

### 分析

当我们在切换视图屏幕的时候（横竖屏），就会重新建立横屏或者竖屏的Activity。我们形象的认为之前建立的Activity会被回收，但是事实如何呢？Java机制不会给你同样的感受，在我们释放Activity之前，因为run函数没有结束，这样MyThread并没有销毁，因此引用它的Activity（Mytest）也有没有被销毁，因此也带来的内存泄露问题。 
       有些人喜欢用Android提供的AsyncTask，但事实上AsyncTask的问题更加严重，Thread只有在run函数不结束时才出现这种内存泄露问题，然而AsyncTask内部的实现机制是运用了ThreadPoolExcutor,该类产生的Thread对象的生命周期是不确定的，是应用程序无法控制的，因此如果AsyncTask作为Activity的内部类，就更容易出现内存泄露的问题。

线程问题的改进方式主要有：

1.  将线程的内部类，改为静态内部类。

2.  在程序中尽量采用弱引用保存Context。

3.  Bitmap是一个很容易导致内存溢出的对象， 对于一个内存对象，如果该对象所占内存过大，在超出了系统的内存限制时候，内存泄露问题就很明显了。

4.  因为在翻转屏幕过程中 activity 重新创建，在第一个activity 销毁时将线程资源释放。

5.  解决bitmap主要是要解决在内存尽量不保存它或者使得采样率变小。在很多场合下，因为我们的图片像素很高，而对于手机屏幕尺寸来说我们并不用那么高像素比例的图片来加载时，我们就可以先把图片的**采样率降低** 在做原来的UI操作。


官方的[如何加载大图片代码片段](http://developer.android.com/training/displaying-bitmaps/load-bitmap.html)可以参考。

## 总结：

综上所述，要避免内存泄露，主要要遵循以下几点：

第一：不要为Context长期保存引用（要引用Context就要使得引用对象和它本身的生命周期保持一致）。

第二：如果要使用到Context，尽量使用ApplicationContext去代替Context，因为ApplicationContext的生命周期较长，引用情况下不会造成内存泄露问题

第三：在你不控制对象的生命周期的情况下避免在你的Activity中使用static变量。尽量使用WeakReference去代替一个static。

第四：垃圾回收器并不保证能准确回收内存，这样在使用自己需要的内容时，主要生命周期和及时释放掉不需要的对象。尽量在Activity的生命周期结束时，在onDestroy中把我们做引用的其他对象做释放，比如：cursor.close()。 

其实我们可以在很多方面使用更少的代码去完成程序。

比如：我们可以多的使用9patch图片等。有很多细节地方都可以值得我们去发现、挖掘更多的内存问题。

也可以使用多进程来分摊主进程中的内存压力，多进程使用有利有弊，具体什么时候使用多进程，请参考[https://github.com/android-cn/android-discuss/issues/7](https://github.com/android-cn/android-discuss/issues/7)
