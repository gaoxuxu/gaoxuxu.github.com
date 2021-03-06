---
author: gavin
comments: true
date: 2013-04-24 16:47:50
layout: post
slug: revel%e6%a1%86%e6%9e%b6-%e8%b7%af%e7%94%b1
title: Revel框架——路由
wordpress_id: 182
categories:
- Go
tags:
- Go
- revel
---

##路由

路由被定义在单独的routes文件中，使用原始的Play!语法。

基本语法为：
    
    (METHOD) (URL Pattern) (Controller.Action)

下面这个例子演示了所有功能：
    
        # conf/routes
        # 这个文件定义了应用所有的路由（以优先级从高到低排列）
        GET    /login                 Application.Login      # 这是一个简单的路径
        GET    /hotels/?              Hotels.Index           # 匹配 /hotels 和 /hotels/ (尾部的斜线是可选的)
        GET    /hotels/{id}           Hotels.Show            # 提取一个URI参数(匹配 /[^/]+/)
        POST   /hotels/{<[0-9]+>id}   Hotels.Save            # 匹配自定义正则表达式的URI参数
        WS     /hotels/{id}/feed      Hotels.Feed            # WebSockets.
        POST   /hotels/{id}/{action}  Hotels.{action}        # 自动路由一些action.
        GET    /public/{<.*>filepath} Static.Serve("public") # 映射到 /app/public 下的资源 /public/...
        *      /{controller}/{action} {controller}.{action}  # 捕获所有; 自动生成URL

让我们一行一行来看。

##一个简单的路径
    
        GET    /login                 Application.Login

最简单的路由使用精确匹配的方法和路径，在上例中调用了Application Controller的Login Action。

##可选的尾部斜线
    
        GET    /hotels/?              Hotels.Index

问号在正则表达式中作用为：它既允许路径匹配前面的字符也允许路径不匹配前面的字符。上例中，在路径为/hotels和/hotels/两种情况下，路由都会调用Hotels.Index。

##URL参数
   
        GET    /hotels/{id}           Hotels.Show

路径的片段可匹配并可提取。默认{id}会匹配除斜线（[^/]+）外的任何事物。在这种情况下，/hotels/123和/hotels/abc都匹配这个路由。

提取的参数可以在Controller.Params这个映射表中得到，也可以通过action方法的参数得到。例如：
    
        func (c Hotels) Show(id int) revel.Result {
    	...
        }

或者
  
        func (c Hotels) Show() revel.Result {
    	var id string = c.Params.Get("id")
    	...
        }

或者
   
        func (c Hotels) Show() revel.Result {
    	var id int = c.Params.Bind("id", reflect.TypeOf(0))
    	...
        }

##采用自定义正则表达式的URL参数
   
        POST   /hotels/{<[0-9]+>id}   Hotels.Save

路由为url参数指定了约束它们的正则表达式。正则表达式的位置在尖括号<>之间，参数名之前。

在这个例子中，我们限制Hotel ID为数字。

##WebSockets
    
        WS     /hotels/{id}/feed      Hotels.Feed

WebSockets的路由方式和其它请求相同，只不过使用一个含有WS标识符的方法：

相应的action必须有这样的签名：
    
        func (c Hotels) Feed(ws *websocket.Conn, id int) revel.Result {
    	...
        }

##静态服务
    
        GET    /public/{<.*>filepath}       Static.Serve("public")
        GET    /favicon.ico                 Static.Serve("public", "img/favicon.png")

关于对静态资产目录的服务，Revel提供一个static组件，它包含一个单独的Static Controller。它服务action需要两个参数：

  * 前缀（字符串）——一个关于资产根目录的相对或绝对路径
  * 文件路径（字符串）——一个指定请求文件的相对路径

（请参阅[结构布局](http://droider.sinaapp.com/?p=172)）

##固定参数

与静态服务部分演示的一样，路由可以为action指定一个或多个值。例如：
    
        GET    /products/{id}    ShowList("PRODUCT")
        GET    /menus/{id}       ShowList("MENU")

提供的参数被绑定到这里使用的参数名上。在这种情况下，列表类型的字符串将被绑定到action的第一个参数。  

这样做可能对下面的情况有帮助：

  * 你有几个类似的action
  * 你有几个在不同模式下工作的action在做相同的事情
  * 你有几个操作不同类型数据的action在做相同的事情
	
##自动路由
    
        POST   /hotels/{id}/{action}  Hotels.{action}
        *      /{controller}/{action} {controller}.{action}

提取的URL参数也用于确定要调用的action。匹配Controller和action时不区分大小写。  

上面示例中的第一行会产生下面的路由：
    
        /hotels/1/show    => Hotels.Show
        /hotels/2/details => Hotels.Details

同样的，示例中第二行的路由可以用来访问应用的任意action。
    
        /application/login => Application.Login
        /users/list        => Users.List

由于匹配到Controller时不区分大小写，所以下面的路由也可以工作：
    
        /APPLICATION/LOGIN => Application.Login
        /Users/List        => Users.List

使用文件最后的自动路由来捕获所有请求对于快速地将action挂钩到非虚的URL非常有用。
