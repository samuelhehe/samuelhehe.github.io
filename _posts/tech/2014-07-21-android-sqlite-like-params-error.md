---
layout: post
title: Android sqlite使用Like语句 参数不匹配造成的错误 解决办法
category: 技术
tags: Android sqlite使用Like语句 , 参数不匹配造成的错误 
keywords: 应用开发流程,开发流程管理 
description: Android sqlite 数据库 使用Like语句 参数不匹配造成的错误 解决办法
---

>这段做项目,自己提议让把应用中使用到的离线数据放置到Sqlite中,最后根据使用类别来获取使用,结果在开发中碰到了一个问题, 折腾了半个小时,记录下来以作备忘.
 
### 问题描述:

>
String factoryId= "F00001";
String lmtid = "Q1001";
String sql = "select * from TleaveMaintype where FID like ?% and LMTID = ?";
Cursor cursor = db.rawQuery(sql, new String[]{factoryId, lmtid};
java.lang.IllegalArgumentException: Cannot bind argument at index 1 because the index is out of range.

提示参数绑定错误 ,定位错误为参数%号附近错误, 就干脆把整个SQL语句放置到SQlite工具中运行,没有问题. 无奈就干脆把%号去除,发现like 语句的效果达不到, 没办法把?号去除,结果还是不识别,最后找到了一个办法,比较凑 

### 解决办法:
>
String factoryId= "F00001";
String lmtid = "Q1001";
String sql = "select * from TleaveMaintype where FID like ? and LMTID = ?";
Cursor cursor = db.rawQuery(sql, new String[]{factoryId, lmtid+"%"};

### 总结: 
问题总算解决了, 做应用得灵活变通啊,下面是一些类似的做法.

### 小菜:

1.使用这种query方法%号前不能加' ;
```
Cursor c_test = db.query(tab_name, new String[]{tab_field02}, tab_field02+"  LIKE ? ",
new String[] { "%" + str[0] + "%" }, null, null, null);
```
2.使用这种query方法%号前必须加'单引号  ;
```
Cursor  c_test=db.query(tab_name, new String[]{tab_field02},tab_field02+"  like '%" + str[0] + "%'", null, null, null, null);
```
3.使用这种方式必须在%号前加' 单引号 ;
```
String current_sql_sel = "SELECT  * FROM "+tab_name +" where "+tab_field02+" like '%"+str[0]+"%'";
//Cursor c_test = db.rawQuery(current_sql_sel, null);
Log.e("tag", "查询完成...");
```
