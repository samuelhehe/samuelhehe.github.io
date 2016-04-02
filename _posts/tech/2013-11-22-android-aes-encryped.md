---
layout: post
title: 对称加密解密系列使用 –AES加解密算法
category: 技术
tags: [AES ]
keywords:  AES加解密算法
description: 对称加密解密系列使用 –AES加解密算法
---

对称加密解密系列–AES加解密算法
====================================

前一段工作中遇到对称加密解密模块，索性网上找了一些资料把常用的对称加解密算法都给学习了。 对称加密解密系列– AES加解密算法

学习 AES 256 加解密算法在Java ， Android端， C# ， IPhone_OC 联合使用解决方案

以下介绍基于 AES 加解密在Java端的应用，图片来自imooc网课程截图：

![aes](http://samuelnotes-wordpress.stor.sinaapp.com/uploads/2015/03/aes.png)


基于AES加密算法在Client端与Server端的消息传递图解：

![aes1](http://samuelnotes-wordpress.stor.sinaapp.com/uploads/2015/03/aes1.png)

首先需要添加生成公用KEY的代码：

```
private static byte[] getRawKey(byte[] seed) throws Exception {
	KeyGenerator kgen = KeyGenerator.getInstance(“AES”);
	SecureRandom sr = SecureRandom.getInstance(“SHA1PRNG”);
	sr.setSeed(seed);   // 设置加密的种子
	kgen.init(128, sr);  /// 设置加密的初始化长度
	SecretKey sKey = kgen.generateKey();
	byte[] raw = sKey.getEncoded();
	return raw;
}
```
接下来添加加密解密代码：

加密Code

```
private static byte[] encrypt(byte[] raw, byte[] clear) throws Exception {
	SecretKeySpec skeySpec = new SecretKeySpec(raw, “AES”);
	// Cipher cipher = Cipher.getInstance(“AES”);
	Cipher cipher = Cipher.getInstance(“AES/CBC/PKCS5Padding”);
	cipher.init(Cipher.ENCRYPT_MODE, skeySpec, new IvParameterSpec(
	new byte[cipher.getBlockSize()]));
	byte[] encrypted = cipher.doFinal(clear);
	return encrypted;
}
```

添加解密Code 

```
private static byte[] decrypt(byte[] raw, byte[] encrypted)
throws Exception {
	SecretKeySpec skeySpec = new SecretKeySpec(raw, “AES”);
	// Cipher cipher = Cipher.getInstance(“AES”);
	Cipher cipher = Cipher.getInstance(“AES/CBC/PKCS5Padding”);
	cipher.init(Cipher.DECRYPT_MODE, skeySpec, new IvParameterSpec(
	new byte[cipher.getBlockSize()]));
	byte[] decrypted = cipher.doFinal(encrypted);
	return decrypted;
}
```

接下来添加转换16进制转换的Code 。

```

public static byte[] toByte(String hexString) {

	int len = hexString.length() / 2;
	
	byte[] result = new byte[len];
	
	for (int i = 0; i < len; i++)
	
	result[i] = Integer.valueOf(hexString.substring(2 * i, 2 * i + 2), 16).byteValue();
	
	return result;

}

public static String toHex(byte[] buf) {

	if (buf == null)
	
	return “”;
	
	StringBuffer result = new StringBuffer(2 * buf.length);
	
	for (int i = 0; i < buf.length; i++) {
	
	appendHex(result, buf[i]);
	
	}
	
	return result.toString();
}

private static void appendHex(StringBuffer sb, byte b) {
	
	final String HEX = “0123456789ABCDEF”;
	
	sb.append(HEX.charAt((b >> 4) & 0x0f)).append(HEX.charAt(b & 0x0f));

}
```

尽管人们对 AES 还有不同的看法,但总体说,AES 作为新一代的数据加密标准汇聚了强安全性、高性能、高效率、易用和 灵活等优点。AES 设计有三个密钥长度:128,192,256 位，相对而言， AES的 128 密钥比 DES 的 56 密钥强 1021 倍， 对于对称加解密的话，个人推荐使用AES加密。
