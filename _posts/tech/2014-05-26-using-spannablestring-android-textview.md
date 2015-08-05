---
layout: post
title: Android Textview 中SpannableString的使用
category: 技术
tags: TextView , SpannableString 
keywords: Android,SpannableString
description: Textview 单行显示不同Style的方法， 整理了下。
---

### 方法很简单，有两种一种是使用html标签格式化文字。 另一种使用SpannableString 

## 第一种： HTML标签格式化文字,

```
 1 import android.app.Activity;   
 2 import android.os.Bundle;   
 3 import android.text.Html;   
 4 import android.widget.TextView;   
 5   
 6 public class AndroidFronColorTest extends Activity {   
 7     @Override  
 8     public void onCreate(Bundle savedInstanceState) {   
 9         super.onCreate(savedInstanceState);   
10            
11         setContentView(R.layout.main);   
12            
13         TextView htmlFormateTextView = (TextView)findViewById(R.id.testTextView);   
14            
15         String source = "我今天很开心，吃了一包<b><em>方便面</em></b>，噢耶<font color='red'>红色字</font>的格式";   
16     
17         htmlFormateTextView.setText(Html.fromHtml(source));   
18     }   
19 }  

```
## 第二种：通过SpannableString进行Style 格式化

```
 1 public class TextViewLinkActivity extends Activity {  
 2     TextView mTextView = null;     
 3     SpannableString msp = null;    
 4       
 5       
 6     @Override  
 7     public void onCreate(Bundle savedInstanceState) {  
 8         super.onCreate(savedInstanceState);  
 9         setContentView(R.layout.main);  
10           
11         mTextView = (TextView)findViewById(R.id.myTextView);  
12           
13         //创建一个 SpannableString对象    
14         msp = new SpannableString("字体测试字体大小一半两倍前景色背景色正常粗体斜体粗斜体下划线删除线x1x2电话邮件网站短信彩信地图X轴综合");   
15           
16         //设置字体(default,default-bold,monospace,serif,sans-serif)  
17         msp.setSpan(new TypefaceSpan("monospace"), 0, 2, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  
18         msp.setSpan(new TypefaceSpan("serif"), 2, 4, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  
19           
20         //设置字体大小（绝对值,单位：像素）   
21         msp.setSpan(new AbsoluteSizeSpan(20), 4, 6, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  
22         msp.setSpan(new AbsoluteSizeSpan(20,true), 6, 8, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  //第二个参数boolean dip，如果为true，表示前面的字体大小单位为dip，否则为像素，同上。  
23           
24         //设置字体大小（相对值,单位：像素） 参数表示为默认字体大小的多少倍  
25         msp.setSpan(new RelativeSizeSpan(0.5f), 8, 10, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  //0.5f表示默认字体大小的一半  
26         msp.setSpan(new RelativeSizeSpan(2.0f), 10, 12, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  //2.0f表示默认字体大小的两倍  
27           
28         //设置字体前景色  
29         msp.setSpan(new ForegroundColorSpan(Color.MAGENTA), 12, 15, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  //设置前景色为洋红色  
30           
31         //设置字体背景色  
32         msp.setSpan(new BackgroundColorSpan(Color.CYAN), 15, 18, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  //设置背景色为青色  
33        
34         //设置字体样式正常，粗体，斜体，粗斜体  
35         msp.setSpan(new StyleSpan(android.graphics.Typeface.NORMAL), 18, 20, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  //正常  
36         msp.setSpan(new StyleSpan(android.graphics.Typeface.BOLD), 20, 22, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  //粗体  
37         msp.setSpan(new StyleSpan(android.graphics.Typeface.ITALIC), 22, 24, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  //斜体  
38         msp.setSpan(new StyleSpan(android.graphics.Typeface.BOLD_ITALIC), 24, 27, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  //粗斜体  
39           
40         //设置下划线  
41         msp.setSpan(new UnderlineSpan(), 27, 30, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  
42           
43         //设置删除线  
44         msp.setSpan(new StrikethroughSpan(), 30, 33, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);  
45           
46         //设置上下标  
47         msp.setSpan(new SubscriptSpan(), 34, 35, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);     //下标     
48         msp.setSpan(new SuperscriptSpan(), 36, 37, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);   //上标              
49           
50         //超级链接（需要添加setMovementMethod方法附加响应）  
51         msp.setSpan(new URLSpan("tel:4155551212"), 37, 39, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);     //电话     
52         msp.setSpan(new URLSpan("mailto:webmaster@google.com"), 39, 41, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);     //邮件     
53         msp.setSpan(new URLSpan("http://www.baidu.com"), 41, 43, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);     //网络     
54         msp.setSpan(new URLSpan("sms:4155551212"), 43, 45, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);     //短信   使用sms:或者smsto:  
55         msp.setSpan(new URLSpan("mms:4155551212"), 45, 47, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);     //彩信   使用mms:或者mmsto:  
56         msp.setSpan(new URLSpan("geo:38.899533,-77.036476"), 47, 49, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);     //地图     
57           
58         //设置字体大小（相对值,单位：像素） 参数表示为默认字体宽度的多少倍  
59         msp.setSpan(new ScaleXSpan(2.0f), 49, 51, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE); //2.0f表示默认字体宽度的两倍，即X轴方向放大为默认字体的两倍，而高度不变  
60         //SpannableString对象设置给TextView  
61         myTextView.setText(sp);   
62         //设置TextView可点击  
63         myTextView.setMovementMethod(LinkMovementMethod.getInstance());   
64     ｝
65 }

```
SpannableString使用注意：
Spanned.SPAN_EXCLUSIVE_EXCLUSIVE等的作用：用来标识在 Span 范围内的文本前后输入新的字符时是否把它们也应用这个效果。
分别有：
-  Spanned.SPAN_EXCLUSIVE_EXCLUSIVE(前后都不包括)、
-  Spanned.SPAN_INCLUSIVE_EXCLUSIVE(前面包括，后面不包括)、
-  Spanned.SPAN_EXCLUSIVE_INCLUSIVE(前面不包括，后面包括)、
-  Spanned.SPAN_INCLUSIVE_INCLUSIVE(前后都包括)

总结：做项目时用到的，平时不注意这些细节，网上搜了下，整理了放上来，以供备用。
参考,谢谢作者：
- <a href="http://www.cnblogs.com/rayray/p/3181922.html">http://www.cnblogs.com/rayray/p/3181922.html</a>
- <a href="http://blog.csdn.net/snowdream86/article/details/6776629">http://blog.csdn.net/snowdream86/article/details/6776629</a>
- <a href="http://www.programcreek.com/java-api-examples/index.php?api=android.text.SpannableString">http://www.programcreek.com/java-api-examples/index.php?api=android.text.SpannableString</a>




