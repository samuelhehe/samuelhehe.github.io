---
layout: post
title: 实现即时发送各种文件流程
category: 技术
tags: 推送 , 即时发送各种文件 
keywords: 推送文件，发送文件流程
description: 之前自己在公司设计的发送文件流程。
---

> 之前自己在公司设计的发送文件流程，后来因为太复杂没有被采用，整理文档发现了，就顺带写篇blog，以作备忘。 


## 设计流程时序图：
![sendfile](https://cloud.githubusercontent.com/assets/5669999/9132381/afdea47e-3d25-11e5-9dcd-12083e29a8aa.png)

## 推送的大概流程描述：


1. 上传文档处理

	选择要上传的文档到Server， server 进行处理，包括 文档的名称，大小，类型等等信息， 同时产生文档对应的code ，然后将文档加密存储至fileserver ， 生成  文件服务器对应的url 。   


2. 文档URL推送
 
	 将生成的文档fileServer 对应的 验证文档URL 推送给Android App 
	  
3. 文档 URL code 验证
	
	根据server推送的 URL+ code 与Server端交互 ， 访问验证code ，文档的有效期等信息，这一部分可根据实际情况自行添加验证条件。验证通过，server将fileURL 返回给App 。 


4. 下载解密
	fileURL 获取到之后即可下载对应文档。 输入在线阅读，传课文档，等各种类型的密码，进行解密，使用。

## Server端架构：

可以考虑将Server部署至四台服务器上，

* 一台处理前端用户的文档上传，初步的加密等处理。 
* 一台文件服务器，专门用于存放推送的多媒体文件。
* 一台则是负责推送的xmpp Server
* 最后一台就是DB Server 为其他Server提供数据库支持。


## Android端架构：

* 添加xmpp 连接模块

* 添加下载模块

* 添加解密与文档读取模块

## 总结：

整个推送流程很简单，清晰，中间添加过多的推送环节的验证KEY， 但实际中一个健全的推送Server是存在的。 

