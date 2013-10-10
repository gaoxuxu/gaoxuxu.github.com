---
author: gavin
comments: true
date: 2013-05-08 14:28:13
layout: post
slug: revel%e6%a1%86%e6%9e%b6%e4%b9%8b%e6%a8%a1%e7%89%88%ef%bc%88template%ef%bc%89
title: Revel框架之模版（Template）
wordpress_id: 210
categories:
- Go
tags:
- Go
- revel
---

## 模版





Revel使用[Go模版](http://www.golang.org/pkg/text/template/)，它从两个目录搜索模版：







  * 应用的views目录（以及所有的子文件夹）。


  * Revel自己的templates目录。





Revel提供了错误页面的模版（在开发模式下显示友好的编译错误），不过应用需要创建一个同名的模版来重写它们，比如app/views/errors/500.html。





## Render Context





Revel使用RenderArgs数据映射表来执行模版。除应用提供的数据以外，Revel还提供以下两项：







  * “errors”——通过[Validation.ErrorMap](http://robfig.github.io/revel/docs/godoc/validation.html#Validation.ErrorMap)返回的map。


  * “flash”——通过之前的请求闪存的数据。





## 模版函数





Go提供[一些在你的模版中使用的函数](http://www.golang.org/pkg/text/template/#Functions)。Revel增加了一些。阅读下面的文档，或者[查看它们的源代码](http://robfig.github.io/revel/docs/godoc/template.html#variables)。





### eq





简单地测试“a == b”。  

示例：  

`<div class="message {{if eq .User "you"}}you{{end}}">`





### set





在给定的Context中设置一个变量。  

示例：




    
    {{set . "title" "Basic Chat room"}}
    <h1>{{.title}}</h1>





### append





在给定的Context中，向一个数组中添加一个元素，或者创建一个数组。  

示例：




    
    {{append . "moreScripts" "js/jquery-ui-1.7.2.custom.min.js"}}
    
    {{range .moreStyles}}
      <link rel="stylesheet" type="text/css" href="/public/{{.}}">
    {{end}}





### field





输入字段的一个辅助函数：  

给定一个字段名，它返回一个包含下列成员的结构体：







  * Id：字段名，转换为适当的HTML元素ID。


  * Name：字段名。


  * Value：该字段在当前RenderArgs中的值。


  * Flash：闪存中该字段的值。


  * Error：所有与该字段有关的错误信息。


  * ErrorClass：如果有错误，则为"hasError"，否则为""；





[查看文档](http://robfig.github.io/revel/docs/godoc/field.html)。  

示例：




    
    {{with $field := field "booking.CheckInDate" .}}
      <p class="{{$field.ErrorClass}}">
        <strong>Check In Date:</strong>
        <input type="text" size="10" name="{{$field.Name}}" class="datepicker" value="{{$field.Flash}}" >
        * <span class="error">{{$field.Error}}</span>
      </p>
    {{end}}





### option





结合字段助手，协助构建HTML option元素。  

示例：




    
    {{with $field := field "booking.Beds" .}}
    <select name="{{$field.Name}}">
      {{option $field "1" "One king-size bed"}}
      {{option $field "2" "Two double beds"}}
      {{option $field "3" "Three beds"}}
    </select>
    {{end}}





### radio





结合字段助手，协助构建HTML单选输入元素。  

示例：




    
    {{with $field := field "booking.Smoking" .}}
      {{radio $field "true"}} Smoking
      {{radio $field "false"}} Non smoking
    {{end}}





## nl2br





换行转换为HTML断行。  

示例：




    
    You said:
    <div class="comment">{{nl2br .commentText}}</div>  
    





### pluralize





辅助输入正确的单词复数。  

示例：  

`There are {{.numComments}} comment{{pluralize (len comments) "" "s"}}`





## Including





Go模版允许包含模版，例如：  

`{{template "header.html" .}}`  

这里需要注意两点：







  * 路径相对于app/views。


  * 所有被包含的模版必须放在根目录下（app/views），这是一个（希望是暂时的）限制。





## 提示





示例应用包括Revel都试图示范有效地利用Go模版。详情请参考：







  * revel/samples/booking/app/views/header.html


  * revel/samples/booking/app/views/Hotels/Book.html





模版自身可以利用辅助函数设置title和外部的样式。  

例如，header可以像下面这样：




    
    <html>
      <head>
        <title>{{.title}}</title>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <link rel="stylesheet" type="text/css" media="screen" href="/public/css/main.css">
        <link rel="shortcut icon" type="image/png" href="/public/img/favicon.png">
        {{range .moreStyles}}
          <link rel="stylesheet" type="text/css" href="/public/{{.}}">
        {{end}}
        <script src="/public/js/jquery-1.3.2.min.js" type="text/javascript" charset="utf-8"></script>
        <script src="/public/js/sessvars.js" type="text/javascript" charset="utf-8"></script>
        {{range .moreScripts}}
          <script src="/public/{{.}}" type="text/javascript" charset="utf-8"></script>
        {{end}}
      </head>





并且包含它的模版像下面这样：




    
    {{set . title "Hotels"}}
    {{append . "moreStyles" "ui-lightness/jquery-ui-1.7.2.custom.css"}}
    {{append . "moreScripts" "js/jquery-ui-1.7.2.custom.min.js"}}
    {{template "header.html" .}}





## 自定义函数





应用可以注册在模版中使用的自定义函数。  

这里有一个例子：




    
    func init() {
        revel.TemplateFuncs["eq"] = func(a, b interface{}) bool { return a == b }
    }



