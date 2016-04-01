---
layout: post
title: Android sqlite 数据库 使用Like语句 参数不匹配造成的错误
category: 技术
tags: [Android, sqlite statement ]
keywords:  android sqlite like 

description: Android sqlite 数据库 使用Like语句 参数不匹配造成的错误 解决办法
---

>  Android sqlite 数据库 使用Like语句 参数不匹配造成的错误 解决办法


Android sqlite 数据库 使用Like语句 参数不匹配造成的错误 解决办法
====================================

这段做项目,自己提议让把应用中使用到的离线数据放置到Sqlite中,最后根据使用类别来获取使用,结果在开发中碰到了一个问题, 折腾了半个小时,记录下来以作备忘.

问题描述:

```
String factoryId= "F00001";
String lmtid = "Q1001";
String sql = "select * from TleaveMaintype where FID like ?% and LMTID = ?";
Cursor cursor = db.rawQuery(sql, new String[]{factoryId, lmtid};
java.lang.IllegalArgumentException: Cannot bind argument at index 1 because the index is out of range.
```

提示参数绑定错误 ,定位错误为参数%号附近错误, 就干脆把整个SQL语句放置到SQlite工具中运行,没有问题. 无奈就干脆把%号去除,发现like 语句的效果达不到, 没办法把?号去除,结果还是不识别,最后找到了一个办法,比较凑 

解决办法:
```
String factoryId= "F00001";
String lmtid = "Q1001";
String sql = "select * from TleaveMaintype where FID like ? and LMTID = ?";
Cursor cursor = db.rawQuery(sql, new String[]{factoryId, lmtid+"%"};
```

总结: 问题总算解决了, 做应用得灵活变通啊,下面是一些类似的做法.

小菜:

//1.使用这种query方法%号前不能加' ;

```
Cursor c_test = db.query(tab_name, new String[]{tab_field02}, tab_field02+"  LIKE ? ",
new String[] { "%" + str[0] + "%" }, null, null, null);
```

//2.使用这种query方法%号前必须加'单引号  ;

```
//  Cursor  c_test=db.query(tab_name, new String[]{tab_field02},tab_field02+"  like '%" + str[0] + "%'", null, null, null, null);
```

3.使用这种方式必须在%号前加' 单引号 ;

```
String current_sql_sel = "SELECT  * FROM "+tab_name +" where "+tab_field02+" like '%"+str[0]+"%'";
//Cursor c_test = db.rawQuery(current_sql_sel, null);
Log.e("tag", "查询完成...");
```
#### SQL模糊查询语句
　　SQL模糊查询，使用like比较字，加上SQL里的通配符，请参考以下：

* 1、LIKE'Q001%' 将搜索以字母 Q001 开头的所有字符串（如 Q0014466）。
* 2、LIKE'%inger' 将搜索以字母 inger 结尾的所有字符串（如 Ringer、Stringer）。
* 3、LIKE'%en%' 将搜索在任何位置包含字母 en 的所有字符串（如 Bennet、Green、McBadden）。
* 4、LIKE'_heryl' 将搜索以字母 heryl 结尾的所有六个字母的名称（如 Cheryl、Sheryl）。
* 5、LIKE'[CK]ars[eo]n' 将搜索下列字符串：Carsen、Karsen、Carson 和 Karson（如 Carson）。
* 6、LIKE'[M-Z]inger' 将搜索以字符串 inger 结尾、以从 M 到 Z 的任何单个字母开头的所有名称（如 Ringer）。
* 7、LIKE'M[^c]%' 将搜索以字母 M 开头，并且第二个字母不是 c 的所有名称（如MacFeather）。
　　下面这句查询字符串，根据变量 zipcode_key 在邮政编码表 zipcode 中查询对应的数据，这句是判断变量 zipcode_key 为非数字时的查询语句，用 % 来匹配任意长度的字符串，从表中地址、市、省三列中查询包含关键字的所有数据项，并按省、市、地址排序。
  
这个例子比较简单，只要你理解了方法就可以写出更复杂的查询语句。

```	
　　sql = "select * from zipcode where (address like'%" & zipcode_key & "%') or (city like'%" & zipcode_key & "%') or (province like'%" & zipcode_key & "%') order by province,city,address
```
