---
author: gavin
comments: true
date: 2013-02-28 10:06:39
layout: post
slug: java-lang-void%e7%b1%bb
title: java.lang.Void类
wordpress_id: 39
categories:
- Java
tags:
- Java
- Void
---

java.lang.Void类是基本类型（primitive type）void的包装类型（wrapped type），final修饰符修饰，不可修改。

唯一属性：

	public static final Class<Void> TYPE = Class.getPrimitiveClass("void");  

在Android中，这个值取的是Thread类run()的返回值类型。

无参构造函数私有。
