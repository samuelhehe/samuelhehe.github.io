---
layout: post
title: Android应用的安装，卸载，更新
category: 技术
tags: app install uninstall update 
keywords: Android ,app install ,uninstall , update 
description: Android应用的安装，卸载，更新(含代码)
---
> Android应用简单的安装，卸载，更新 

安装程序的方法： 

1、通过Intent机制，调出系统安装应用，重新安装应用的话，会保留原应用的数据。

```

1.	String fileName = Environment.getExternalStorageDirectory() +apkName;  
2.	Uri uri = Uri.fromFile(new File(fileName));  
3.	Intent intent =new Intent(Intent.ACTION_VIEW);  
4.	intent.setDataAndType(uri,"application/vnd.android.package-archive");  
5.	startActivity(intent);  

```

2、直接调用安装接口。

```

1.	Uri mPackageURI = Uri.fromFile(new File(Environment.getExternalStorageDirectory() + pkName));  
2.	int installFlags = 0;  
3.	PackageManager pm = getPackageManager();  
4.	try {  
6.		PackageInfo pi = pm.getPackageInfo(packageName,  
7.		PackageManager.GET_UNINSTALLED_PACKAGES);  
8		if(pi !=null){  
9			installFlags |= PackageManager.REPLACE_EXISTING_PACKAGE;  
10		}  
11.	}  
12.	catch (NameNotFoundException e)  
13.	{}  
14.	PackageInstallObserver observer =new PackageInstallObserver();  
15.	pm.installPackage(mPackageURI, observer, installFlags);  

```

安装应用权限：android.permission.INSTALL_PACKAGES

系统应用（安装在/system/app下面）可以采用该方式，第三方应用无法申请安装卸载权限。
java.lang.SecurityException: Neither user 10039 nor current process has android.permission.INSTALL_PACKAGES.

3、执行install命令。 install –r 更新安装，默认新安装；如果不附上-r参数，则会清楚原应用的数据，版本一致则无法安装。

```

（1）am start 

（2）Runtime.exec(String[] args)

（3）Class<?> execClass = Class.forName("android.os.Exec");

```
4、 执行cp / adb push命令。

由系统检测到应用程序有更新，自动完成重新安装。


5、 卸载程序的方法：

1、通过Intent机制，调出系统卸载应用。

```

1.	UripackageURI = Uri.parse("package: com.samuelnotes.apptest");  
2.	Intent intent =new Intent(Intent.ACTION_DELETE);  
3.	startActivity(intent);  

```

2、 直接调用卸载接口。

```

1.	PackageInstallObserver observer =new PackageInstallObserver();  
2.	pm.installPackage(mPackageURI, observer, installFlags);

```

卸载应用权限：android.permission.DELETE_PACKAGES

3、运行rm apk安装文件，由系统检测后调用卸载应用。

