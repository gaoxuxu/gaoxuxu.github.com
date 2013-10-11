---
author: gavin
comments: true
date: 2013-04-23 15:35:00
layout: post
slug: revel%e6%a1%86%e6%9e%b6-%e5%9f%ba%e6%9c%ac%e6%a6%82%e5%bf%b5
title: Revel框架——基本概念
wordpress_id: 167
categories:
- Go
tags:
- framework
- Go
- revel
---

##概念
* * *
Revel依赖于约定，即在应用中规定一个明确的架构，这使你用MVC模式构建web应用变得简单。作为回报，它的结构非常轻量级，并且开发周期非常快。

##MVC
* * *
快速总结：
	
  * Model是描述应用域的重要数据对象，它也封装了指定域查询和更新数据的逻辑。
  * View描述了数据怎么呈现以及操作。在revel中，使用模板（template）来呈现数据以及引导用户。
  * Controller处理请求。它们执行用户期待的动作，并决定要显示哪个view，以及准备和提供view渲染必需的数据。
	
网上有很多优秀的MVC架构的概述，尤其是Play！Framework，revel的思路完全来源于Play!Framework。

##Goroutine每一个请求
* * *
Revel构建于Go HTTP Server之上，它为每一个到来的请求都创建一个goroutine（轻量级的线程）来进行处理。它的含义是你的代码可以自由地阻塞，但是必须能够处理并发的请求进程。

##Controllers和Actions
* * *
一个HTTP请求调用一个action，action处理请求并且将结果写入到响应中。相关的action组成了controllers。
* * *
Controller可以是嵌入*revel.Controller为第一个字段的任意类型。

例如：
  
        type AppController struct {
    	*revel.Controller
        }

revel.Controller是请求的上下文（Context）。它包含了请求和响应的数据。下面是它及其辅助类型的定义，详情请参阅[godoc](http://robfig.github.io/revel/docs/godoc/controller.html#Controller)：
  
        type Controller struct {
            Name          string          // controller的名字, 例如："Application"
            Type          *ControllerType // controller类型的描述
            MethodType    *MethodType     // 调用的action类型的描述
            AppController interface{}     // 实例化的controller
    
            Request  *Request
            Response *Response
            Result   Result
    
    	Flash      Flash                  // 用户的cookie, 每个请求后都会清除
    	Session    Session                // Session, 存储在cookie, 已签名.
    	Params     Params                 // 来自Url和表格的参数 (包含多个).
    	Args       map[string]interface{} // 每个请求暂存的空间.
    	RenderArgs map[string]interface{} // 传递给template的参数.
    	Validation *Validation            // 数据验证助手
    	Txn        *sql.Tx                // 默认为Nil, 但可用于app / 插件
        }
    
        // Flash表示一个每次请求都会被覆盖的cookie.
        // 它允许跨页面存储数据.
        // 它通常用于实现成功或失败信息.
        // 例如Post/Redirect/Get模式: http://en.wikipedia.org/wiki/Post/Redirect/Get
        type Flash struct {
    	Data, Out map[string]string
        }
    
        // 它提供一个关于请求参数的统一视图.
        // 包括:
        // - URL查询字符串
        // - 表格值
        // - 文件上传
        type Params struct {
    	url.Values
    	Files map[string][]*multipart.FileHeader
        }
    
        // 一个已签名的cookie(从而大小被限制为4kb).
        // 约束: Key中不能有冒号(:).
        type Session map[string]string
    
        type Request struct {
    	*http.Request
    	ContentType string
        }
    
        type Response struct {
    	Status      int
    	ContentType string
    	Headers     http.Header
    	Cookies     []*http.Cookie
    
    	Out http.ResponseWriter
        }

作为处理HTTP请求的一部分，Revel实例化一个你Controller的实例，并且为嵌入的revel.Controller设置所有的属性。Revel不在请求间共享Controller实例。
* * *
Action可以是满足下列条件的任意Controller上的方法：

  * 是可输出的
  * 返回一个revel.Result
	
例如：
    
        func (c AppController) ShowLogin(username string) revel.Result {
    	..
    	return c.Render(username)
        }

上例中调用revel.Controller.Render来执行一个template，并将username作为参数传递给它。revel.Controller上有很多生产revel.Result的方法，但是应用也可以自由创建自己的方法。

##Result
* * *
Result可以是任意符合下面接口的类型：
    
        type Result interface {
    	Apply(req *Request, resp *Response)
        }
通常情况下，在action返回一个Result之前不会将数据写入到响应中。接收到Result时，Revel先写入响应头和cookies（例如设置会话cookie），然后调用Result.Apply来写入真正的响应内容。
（action可以选择直接写入到响应，但这估计只能用于特殊情况。例如，在这些情况下，需要自己处理保存会话和Flash数据。）
