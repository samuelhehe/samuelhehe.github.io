---
layout: post
title: Android 软键盘常用控制
category: 技术
tags: [Android, keyboard ]
keywords:  Android 键盘控制
description: Android 软键盘控制
---

Android 键盘控制
====================================

软件盘在输入完成后不消失，或者输入的时候不出来，等等。最近开发中同事遇到这个问题，

### 问题描述： 输入完成后键盘不消失，耽误意见反馈按钮。最后查找资料后解决了。

对软键盘的控制：

在Activity中设置：android:windowSoftInputMode=”stateUnspecified”, 默认设置：软键盘的状态(隐藏或可见)没有被指定。系统将自适应Activity的输入模式或依赖于主题的设置。

"stateUnchanged", 软键盘被保持上次的状态，保持状态不变。
"stateHidden", 隐藏软件盘
 "stateAlwaysHidden", 软键盘总是隐藏
"stateVisible",. 软键盘可见
"stateAlwaysVisible", 当用户选择这个Activity时，软键盘总是可见的。
"adjustResize",  当软键盘弹出时，要对主窗口调整屏幕的大小以便留出软键盘的空间。
"adjustPan" （平移模式：当输入框不会被遮挡时，该模式没有对布局进行调整，然而当输入框将要被遮挡时，窗口就会进行平移。也就是说，该模式始终是保持输入框为可见。 这个参数对于输入框被遮挡可以自动调整来适应输入位置

隐藏

```
EditText edit=(EditText)findViewById(R.id.edit);
InputMethodManager imm =InputMethodManager)getSystemService(Context.INPUT_METHOD_SERVICE);
imm.hideSoftInputFromWindow(edit.getWindowToken(),0);
```

默认不弹出软件键盘

#### 方法一：
在AndroidMainfest.xml中选择哪个activity，设置windowSoftInputMode属性为adjustUnspecified|stateHidden
eg：

```
<activity android:name=".aty"
android:label="@string/app_name"
android:windowSoftInputMode="adjustUnspecified|stateHidden"
android:configChanges="orientation|keyboardHidden">
</activity>
```

#### 方法二：
让EditText失去焦点，使用EditText的clearFocus方法
eg：

```
EditText edit=(EditText)findViewById(R.id.edit);
edit.clearFocus();
```

#### 方法三：
强制隐藏android输入法窗口
eg：

```
EditText edit=(EditText)findViewById(R.id.edit);
InputMethodManager imm = (InputMethodManager)getSystemService(Context.INPUT_METHOD_SERVICE);
imm.hideSoftInputFromWindow(edit.getWindowToken(),0);
```

### 始终不弹出软件键盘
eg：

```
EditText edit=(EditText)findViewById(R.id.edit);
edit.setInputType(InputType.TYPE_NULL);
```

### 代表控件Action动作的 ：
 
android:imeOptions="actionNext"  下一步
actionUnspecified   未指定的
actionNone  没有任何行为
actionGo  相当于enter键
actionSearch  搜索框处使用
actionSend  发送
actionDone 完成，软键盘自动消失
actionPrevious  上一步

### 总结：问题看上去虽很简单的问题，这是在Android入门时候就能够接触到的内容，但是开发中却容易忽略，导致开发不乐。
