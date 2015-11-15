---
layout: post
title: 怎样搭建自己的推送服务器
category: 技术
tags: 推送服务
keywords: 推送, 搭建,推送服务 
description:  怎样搭建自己的推送服务器
---

> 分析过GCM推送流程，了解了国内各大推送平台，何不自己搭建一个推送服务器呢 

**问题** 总是随着需求来的：

- 那么现在如果有这样的一条需求，客户转账，消费信息，以消息推送的方式推送给客户，推送消息内容的安全有保障吗？
- 如果公司区分内网与外网 ，要求员工到公司能够使用，到家也能够使用，准确无误的接受消息，这样怎么办？（当然这种情况也是有办法解决的。）

如果对GCM推送流程不太了解的话，可以通过下边链接去看看这篇分析文章。
[GCM(Google Cloud Messaging) 推送流程分析](http://www.samuelnotes.cn/2013/10/12/gcm-push-msg-research.html)

### 总结