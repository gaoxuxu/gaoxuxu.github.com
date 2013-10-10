---
author: gavin
comments: true
date: 2013-04-27 16:00:20
layout: post
slug: revel%e6%a1%86%e6%9e%b6-result
title: Revel框架——Result
wordpress_id: 198
categories:
- Go
tags:
- Go
- revel
---

## Results





action必须返回一个用于处理响应生成的revel.Result。它遵循一个简单的接口：




    
        type Result interface {
            Apply(req *Request, resp *Response)
        }





revel.Controller提供一些产生Result的方法：







  * Render，RenderTemplate——渲染模版，传递参数。


  * RenderJson，RenderXml——将一个结构串行化为json或xml。


  * RenderText——返回一个纯文本的响应。


  * Redirect——重定向到另外一个action或URL。


  * RenderFile——返回一个文件，通常为一个被下载的附件。


  * RenderError——返回一个500响应，并且渲染errors/500.html模版。


  * NotFound——返回一个404响应，并且渲染errors/404.html模版。


  * Todo——返回一个存根响应（500）。





另外，开发者可以定义自己的revel.Result并返回。





### 设置状态码和内容类型





每一个内建的Result都有一个默认的状态码和内容类型。覆盖这些默认值非常简单，只需要在响应中设置它们的属性即可：




    
        func (c Application) Action() revel.Result {
            c.Response.Status = http.StatusTeapot
            c.Response.ContentType = "application/dishware"
            return c.Render()
        }





## Render





在一个action中调用Render（例如“Controller.Action”），mvc.Controller.Render做两件事：







  1. 添加所有的参数到Controller的RenderArgs，使用它们当前的标识符作为key。


  2. 执行模版“views/Controller/Action.html”，并将Controller的RenderArgs作为数据的映射表传递给模版。





如果不成功（例如它找不到这个模版），它会返回一个替代的ErrorResult： 即允许开发者这样写：




    
        func (c MyApp) Action() revel.Result {
            myValue := calculateValue()
            return c.Render(myValue)
        }





并且在他们的模版中使用“myValue”。使用这种方式比构造一个明确的映射表更方便，总之在多数情况下，把将要被处理的数据作为本地变量。  
**注意：**Revel在看参数名之前会先根据方法名决定模版的路径。因此，c.Render()只能在Action中调用。





## RenderJson / RenderXml





应用可以调用RenderJson或RenderXml并且传递任意的Go类型（通常是一个Struct）。Revel会使用json.Marshal或xml.Marshal将它串行化。 如果app.conf中results.pretty=true，串行化工作将使用MarshalIndent代替，以生成可供人浏览的友好输出。





## Redirect





有一个助手函数被用来产生重定向。它被用于两种情况：







  1. 不带参数重定向到一个Action：




    
        return c.Redirect(Hotels.Settings)





这种形式非常有用，因为它从路由提供了一定程度的安全性和独立性。（它会自动生成URL。） 2. 重定向到一个已格式化的string：




    
        return c.Redirect("/hotels/%d/settings", hotelId)





这种形式必须传递参数。





它返回一个302（临时重定向）状态码。





## 添加你自己的Result





这有一个添加一个简单Result的例子：  
创建这个类型：




    
        type Html string
    
        func (r Html) Apply(req *Request, resp *Response) {
            resp.WriteHeader(http.StatusOK, "text/html")
            resp.Out.Write([]byte(r))
        }





然后在一个Action中使用它：




    
        func (c *Application) Action() revel.Result {
            return Html("Hello World")
        }





## 状态码





每个Result默认都会设置一个状态码。你可以自己设置一个以覆盖默认的状态码：




    
        func (c *Application) CreateEntity() revel.Result {
            c.Response.Status = 201
            return c.Render()
        }



