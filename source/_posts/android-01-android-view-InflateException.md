---
layout: post
title: "android.view.InflateException: Binary XML file line 错误"
date: 2016-01-27 10:03
comments: true
tags: 
	- Java 
	- 坑
	- android 
	- 笔记
---

问题描述
------------------
自定义的View，在XML中调用出现android.view.InflateException: Binary XML file line 错误。

解决方法
---------

```java
    View(Context context)     //Simple constructor to use when creating a view from code

    View(Context context, AttributeSet attrs)     //Constructor that is called when inflating a view from XML

    View(Context context, AttributeSet attrs, int defStyle)     //Perform inflation from XML and apply a class-specific base style
```

第二个和第三个构造函数对于XML这种引用方式是必须实现的，这三个构造函数应该是在不同的应用场合来实例化一个View对象。