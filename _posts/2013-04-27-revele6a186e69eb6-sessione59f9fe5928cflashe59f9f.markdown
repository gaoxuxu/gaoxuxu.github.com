---
author: gavin
comments: true
date: 2013-04-27 13:47:00
layout: post
slug: revel%e6%a1%86%e6%9e%b6-session%e5%9f%9f%e5%92%8cflash%e5%9f%9f
title: Revel框架——Session域和Flash域
wordpress_id: 194
categories:
- Go
tags:
- Go
- revel
---

## 
	Session / Flash域






	Revel提供两种基于Cookie的存储机制：




    
        // 一个已签名的Cookie（从而大小被限制为4kb）。
        // 约束: Key中不能有冒号（:）。
        type Session map[string]string
    
        // Flash表示一个每次请求都会被覆盖的Cookie。
        // 它允许数据每跨一个页面就被存储一次。
        // 它通常被用来实现成功或失败信息。
        // 例如Post/Redirect/Get模式: http://en.wikipedia.org/wiki/Post/Redirect/Get
        type Flash struct {
        	Data, Out map[string]string
        }




## 
	Session（会话）






	在Revel中，Session的概念是一个string类型的map，存储为加密签名的Cookie。  

这意味着：






	
  * 
		大小被限制为4kb。
	

	
  * 
		所有的数据必须被串行化为一个string以用于存储。
	

	
  * 
		用户可以查看所有的数据（未加密的），但修改是安全的。
	




## 
	Flash






	Flash提供一次性的string存储。它在实现[Post/Redirect/Post模式](http://en.wikipedia.org/wiki/Post/Redirect/Get)或短暂的“操作成功！”或“操作失败！”信息时非常有用。  

下面是这种用法的一个例子：




    
        // 显示设置表格
        func (c App) ShowSettings() revel.Result {
        	return c.Render()
        }
    
        // 处理一个请求
        func (c App) SaveSettings(setting string) revel.Result {
    	c.Validation.Required(setting)
        	if c.Validation.HasErrors() {
    	    c.Flash.Error("Settings invalid!")
    	    c.Validation.Keep()
                c.Params.Flash()
    	    return c.Redirect(App.ShowSettings)
    	}
    
    	saveSetting(setting)
    	c.Flash.Success("Settings saved!")
    	return c.Redirect(App.ShowSettings)
        }





	例子的来龙去脉：






	
  1. 
		用户取得一个设置页面。
	

	
  2. 
		用户提交一个设置（POST方式）
	

	
  3. 
		应用处理这个请求，保存一个错误或成功信息到flash，并重定向到设置页面（REDIRECT方式）。
	

	
  4. 
		用户取得设置页面，页面的模版显示flash中的信息。
	





	它使用了两个便利的函数：






	
  1. 
		Flash.Success(message string)是Flash.Out[“success”] = message的一个简便写法。
	

	
  2. 
		Flash.Error(message string)是Flash.Out["error"] = message的一个简便写法。
	



