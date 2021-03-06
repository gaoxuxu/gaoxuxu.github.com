---
author: gavin
comments: true
date: 2013-04-23 22:37:40
layout: post
slug: revel%e6%a1%86%e6%9e%b6-%e5%85%b1%e4%ba%ab%e5%8a%9f%e8%83%bd
title: Revel框架——共享功能
wordpress_id: 175
categories:
- Go
tags:
- Go
- revel
---

##共享功能
* * *
Revel提供了在不同的域之间共享功能的途径：

  * actions
  * controllers
  * applications
	
##Actions
* * *
相关的actions共享一个单独的Controller类型。开发者可以为它添加数据域以便存储更多关于请求的上下文。拦截器可以在Revel运行前或后注册到Controller的任意action上。

##Controllers
* * *
Revel允许将Controller混合在一起以便跨越不同的Controller类型共享字段、方法以及拦截器。
下面是一个例子：
 
        // MongoController提供访问我们的MongoDB
        type MongoController struct {
            *revel.Controller
            Session *mgo.Session
        }
    
        func (c *MongoController) Begin() revel.Result {
            c.Session = ...
        }
    
        func init() {
            revel.InterceptMethod((*MongoController).Begin, revel.BEFORE)
        }
    
        type AppController struct {
            *revel.Controller
            MongoController
        }

在这个例子中，开发者创建了一个MongoController，它用来跨越整个应用，开发者可以将它混合到另外的Controller中。在这里，拦截器将会运行并且AppController上的action可以访问*mgo.Session。

##Application
* * *
组件可以用来跨应用共享Controller类型、模板和资产。
