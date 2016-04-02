---
layout: post
title: Android 获取应用的大小
category: 技术
tags: [getAppsize ]
keywords:  Android appSize
description: Android 获取应用的大小
---

Android 获取应用的大小
====================================

>之前项目里边碰到了一个问题，就是获取App的大小，当时做了没问题，后来系统版本升级之后，查阅后发现 4.2以及以上版本就报错了，就无法获取到应用的大小。然后疯狂的查找资料，终于解决，现在将解决办法贴出来，记录下来，以便提升。

关于这方面的知识见官方文档：[http://developer.android.com/reference/android/content/pm/PackageStats.html](http://developer.android.com/reference/android/content/pm/PackageStats.html)

4.2之前的版本处理方法：

```
public void queryPacakgeSize(String pkgName) throws Exception {
	if (pkgName != null) {
	    PackageManager pm = getPackageManager();
	try {
	Method getPackageSizeInfo = pm.getClass().getDeclaredMethod("getPackageSizeInfo", String.class,IPackageStatsObserver.class);
	getPackageSizeInfo.invoke(pm, pkgName, new PkgSizeObserver());
	   } catch (Exception ex) {
	Log.e(TAG, "NoSuchMethodException");
		ex.printStackTrace();
		throw ex;
		}
	}
}
```
4.2以上的版本处理方法：

```
public void queryPacakgeSize2(String pkgName) throws Exception {
	if (pkgName != null) {
		PackageManager pm = getPackageManager();
		try {
		Method getPackageSizeInfo = pm.getClass().getDeclaredMethod("getPackageSizeInfo", String.class, int.class,IPackageStatsObserver.class);
		getPackageSizeInfo.invoke(pm, pkgName,
		Process.myUid() / 100000, new PkgSizeObserver());
		} catch (Exception ex) {
			Log.e(TAG, "NoSuchMethodException");
			ex.printStackTrace();
			throw ex;
			}
		}
	}
```

**注意事项：注意添加权限，不要忘记。否则报错**
```
  android.permission.GET_PACKAGE_SIZE
```

### 总结：在Mi3 上安装运行，测试结果正常。测试项目地址：

[获取Android App 大小](https://github.com/samuelhehe/AppSizeGet)
