---
layout: post
title: Android 代码安全（代码混淆）
category: 技术
tags: [code proguard ]
keywords:  Android code proguard
description: Android 代码安全（代码混淆）
---

Android 代码安全（代码混淆）
====================================


>之前学习这一部分的时候，也废了很大的劲，工作中遇到了，就网上乱搜一通，配置好了以后就忘了各个参数的意思，为了方便进一步提升，觉得有必要将此部分记录下来。废话少说转正文

加混淆就是为了防止别人反编译自己辛辛苦苦写的代码，保护自己的劳动成果。上官网链接：[http://developer.android.com/tools/help/proguard.html](http://developer.android.com/tools/help/proguard.html)

proguard配置方法与解释链接：[https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/usage.html](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/usage.html)

###简单的描述一下我自己对该配置的认知

删除无效代码（Shrinking 收缩），和代码进行优化 （Optimization Options）。默认proguard 会混淆所有代码，但是下面几种情况是不能改变java 元素的名称，否则就会这样就会导致程序出错。

动态加载类或者字段的时候，例如用到反射的地方。（如果添加混淆则会导致引用错误，无法找到相关对象，从而报异常）
 我们代码依赖于系统的接口。例如重写系统的方法。
如果我们使用了JNI ，如果我们添加了混淆则导致引用不到lib而报错。
系统默认的并不能够满足我们所有的需求，所以我们实际开发中我们需要定制适合自己App的混淆配置文件。

### proguard 的配置

#### 最常用的配置选项

-dontwarn  缺省proguard 会检查每一个引用是否正确，但是第三方库里面往往有些不会用到的类，没有正确引用。如果不配置的话，系统就会报错。
-keep 指定的类和类成员被保留作为 入口 。
-keepclassmembers 指定的类成员被保留。
-keepclasseswithmembers 指定的类和类成员被保留，假如指定的类成员存在的话。

为了防止混淆出问题，你需要熟悉你所有的code ，系统的架构 ，以及系统和你code的集成的接口，并细心分析。 同时你必须需要一轮全面的测试。 所以混淆也还是有一定风险的。 为了避免风险，你可以只是混淆部分关键的代码，但是这样你的混淆的效果也会有所降低。

#### 常见的不能混淆的代码

被Android Resource 文件引用到的。名字已经固定的也不能混淆，比如自定义的View ，好像混淆不通过，不要在Resource中添加Onclick事件，有一次因为这个耽误了我两个小时，原因竟然是因为把Onclick事件添加到了resource中。

Android Parcelable ，需要使用android 序列化的 ，系统默认配置文件就包含这个。其他Anroid 官方建议 不混淆的，如：

* android.app.backup.BackupAgentHelper 
* android.preference.Preference com.android.vending.licensing.ILicensingService
* Java序列化方法，系统序列化需要固定的方法。
* 枚举 ，系统需要处理枚举的固定方法。
* 本地方法，不能修改本地方法名
* annotations 注释
* 数据库驱动
* 有些resource 文件
* Android系统组件
* 自定义View
* Android Parcelable
* Android R 文件
* Android Parcelable
* 
各个开发人员必须检查自己的code 是否用到反射 ，和其他不能混淆的地方。
告诉我来修改配置文件（已经保留的就不需要了）

#### 常见的目前系统不检查的第三方库为

```
-dontwarn android.support.**
-dontwarn com.tencent.**
-dontwarn org.dom4j.**
-dontwarn org.slf4j.**
-dontwarn org.http.mutipart.**
-dontwarn org.apache.**
-dontwarn org.apache.log4j.**
-dontwarn org.apache.commons.logging.**
-dontwarn org.apache.commons.codec.binary.**
-dontwarn weibo4android.**
```
#### proguard 参数
-include {filename}    从给定的文件中读取配置参数

-basedirectory {directoryname}    指定基础目录为以后相对的档案名称

-injars {class_path}    指定要处理的应用程序jar,war,ear和目录

-outjars {class_path}    指定处理完后要输出的jar,war,ear和目录的名称

-libraryjars {classpath}    指定要处理的应用程序jar,war,ear和目录所需要的程序库文件

-dontskipnonpubliclibraryclasses    指定不去忽略非公共的库类。

-dontskipnonpubliclibraryclassmembers    指定不去忽略包可见的库类的成员。

#### 保留选项

-keep {Modifier} {class_specification}    保护指定的类文件和类的成员

-keepclassmembers {modifier} {class_specification}    保护指定类的成员，如果此类受到保护他们会保护的更好

-keepclasseswithmembers {class_specification}    保护指定的类和类的成员，但条件是所有指定的类和类成员是要存在。

-keepnames {class_specification}    保护指定的类和类的成员的名称（如果他们不会压缩步骤中删除）

-keepclassmembernames {class_specification}    保护指定的类的成员的名称（如果他们不会压缩步骤中删除）

-keepclasseswithmembernames {class_specification}    保护指定的类和类的成员的名称，如果所有指定的类成员出席（在压缩步骤之后）

-printseeds {filename}    列出类和类的成员-keep选项的清单，标准输出到给定的文件

#### 压缩
-dontshrink    不压缩输入的类文件

-printusage {filename}

-whyareyoukeeping {class_specification}  不懂

#### 优化
-dontoptimize    不优化输入的类文件

-assumenosideeffects {class_specification}    优化时假设指定的方法，没有任何副作用

-allowaccessmodification    优化时允许访问并修改有修饰符的类和类的成员

#### 混淆
-dontobfuscate    不混淆输入的类文件

-printmapping {filename} 打印映射

-applymapping {filename}    重用映射增加混淆

-obfuscationdictionary {filename}    使用给定文件中的关键字作为要混淆方法的名称

-overloadaggressively    混淆时应用侵入式重载

-useuniqueclassmembernames    确定统一的混淆类的成员名称来增加混淆

-flattenpackagehierarchy {package_name}    重新包装所有重命名的包并放在给定的单一包中

-repackageclass {package_name}    重新包装所有重命名的类文件中放在给定的单一包中

-dontusemixedcaseclassnames    混淆时不会产生形形色色的类名

-keepattributes {attribute_name,…}    保护给定的可选属性，例如LineNumberTable, LocalVariableTable, SourceFile, Deprecated, Synthetic, Signature, and InnerClasses.

-renamesourcefileattribute {string}    设置源文件中给定的字符串常量

#### 解决export打包的报错

这个时候export提示“conversion to Dalvik format failed with error 1”错误，
网上说法有好多种，最后我还是把proguard从4.4升级到4.8就解决了。官方地址是[http://proguard.sourceforge.net](http://proguard.sourceforge.net)。上面的配置文件参数可以在这里查阅。

升级办法很简单，就是把android sdk目录下的tool/proguard目录覆盖一下即可。

打包出来的程序如何调试
一旦打包出来，就不能用eclipse的logcat去看了，这里可以用android sdk中ddms.bat的tool来看，一用就发现和logcat其实还是一个东西，就是多了个设备的选择。

使用 gson 需要的配置
当Gson用到了泛型就会有报错，这个真给郁闷了半天，提示“Missing type parameter”。最后找到一个资料给了一个解决办法，参考：http://stackoverflow.com/questio … sing-type-parameter。

另外我又用到了JsonObject，提交的Object里面的members居然被改成了a。所以上面给的东西还不够，还要加上
#### 用到自己拼接的JsonObject

```
-keep class com.google.gson.JsonObject { *; }
```

个人建议减少这些依赖包混淆带来的麻烦，干脆都全部保留不混淆。例如
```
-keep class com.badlogic.** { *; }
-keep class * implements com.badlogic.gdx.utils.Json*
-keep class com.google.** { *; }
```
使用libgdx需要的配置参考[http://code.google.com/p/libgdx-users/wiki/Ant](http://code.google.com/p/libgdx-users/wiki/Ant)

#### 验证打包效果

利用了apktool的反编译工具，把打包文件又解压了看了一下，如果包路径、类名、变量名、方法名这些变化和你期望一致，那就OK了。命令：

```
apktool.bat  d xxx.apk destdir
```

#### 配置实例

```

-injars  androidtest.jar【jar包所在地址】

-outjars  out【输出地址】

-libraryjars    ‘D:\android-sdk-windows\platforms\android-9\android.jar’ 【引用的库的jar，用于解析injars所指定的jar类】

-optimizationpasses 5    性能优化

-dontusemixedcaseclassnames 【混淆时不会产生形形色色的类名 】

-dontskipnonpubliclibraryclasses 【指定不去忽略非公共的库类。 】

-dontpreverify 【不预校验】

-verbose

-optimizations !code/simplification/arithmetic,!field/*,!class/merging/* 【优化】

-keep public class * extends android.app.Activity　　【不进行混淆保持原样】

-keep public class * extends android.app.Application

-keep public class * extends android.app.Service

-keep public class * extends android.content.BroadcastReceiver

-keep public class * extends android.content.ContentProvider

-keep public class * extends android.app.backup.BackupAgentHelper

-keep public class * extends android.preference.Preference

-keep public class com.android.vending.licensing.ILicensingService

-keep public abstract interface com.asqw.android.Listener{
public protected <methods>;  【所有方法不进行混淆】
}

-keep public class com.asqw.android{
public void Start(java.lang.String); 【对该方法不进行混淆】
}

-keepclasseswithmembernames class * { 【保护指定的类和类的成员的名称，如果所有指定的类成员出席（在压缩步骤之后）】
native <methods>;
}

-keepclasseswithmembers class * { 【保护指定的类和类的成员，但条件是所有指定的类和类成员是要存在。】
public <init>(android.content.Context, android.util.AttributeSet);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet, int);
}

-keepclassmembers class * extends android.app.Activity {【保护指定类的成员，如果此类受到保护他们会保护的更好 】
public void *(android.view.View);
}

-keepclassmembers enum * {
public static **[] values();
public static ** valueOf(java.lang.String);
}

-keep class * implements android.os.Parcelable {【保护指定的类文件和类的成员】
public static final android.os.Parcelable$Creator *;
}
//不混淆指定包下的类
-keep class com.abcd.**

```

### proguard 问题和风险

代码混淆后虽然有混淆优化的好处，但是它往往也会带来如下的几点问题

* 1，混淆错误，用到第三方库的时候，必须告诉 proguard 不要检查，否则proguard 会报错。
* 2，运行错误，当code 不能混淆的时候，我们必须要正确配置，否则程序会运行出错，这种情况问题最多。
* 3，调试难，出错了，错误堆栈是混淆后的代码 ，自己也看不懂,最后苦的还是开发的,泪奔。官网上提出的使用

```
retrace.bat|retrace.sh [-verbose] mapping.txt [<stacktrace_file>]
```

工具进行反mapping文件的操作：[http://developer.android.com/tools/help/proguard.html#decoding](http://developer.android.com/tools/help/proguard.html#decoding)
