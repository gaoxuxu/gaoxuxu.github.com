---
author: gavin
comments: true
date: 2013-04-25 17:49:41
layout: post
slug: revel%e6%a1%86%e6%9e%b6-%e5%8f%82%e6%95%b0%e9%aa%8c%e8%af%81
title: Revel框架——参数验证
wordpress_id: 189
categories:
- Go
tags:
- Go
- revel
---

##参数验证 

Revel为参数验证提供了内建的功能。包括几个部分：

  * 一个Validation上下文收集和管理验证错误（包含键和信息）。
  * 检查并把错误放到上下文中的助手运行。
  * 一个模版函数通过key从Validation上下文得到错误信息。
	
比较深入的例子参见[验证示例](http://robfig.github.io/revel/samples/validation.html) 

##内嵌错误信息

下面的例子演示了使用内嵌错误信息验证字段：
    
        func (c MyApp) SaveUser(username string) revel.Result {
    	// Username字段是必须的，并且它的长度为4-15个字母（包含边界）。
    	c.Validation.Required(username)
    	c.Validation.MaxSize(username, 15)
    	c.Validation.MinSize(username, 4)
    	c.Validation.Match(username, regexp.MustCompile("^\\w*$"))
    
    	if c.Validation.HasErrors() {
    	    // 在flash上下文中存储验证错误并重定向。
    	    c.Validation.Keep()
    	    c.FlashParams()
    	    return c.Redirect(Hotels.Settings)
    	}
    
    	// 所有数据已验证!
    	...
        }

一步一步看：

  1. 验证username上的四个不同条件（必需的、最小长度、最大长度、匹配）。
  2. 每一次验证都返回一个ValidationResult，失败的ValidationResult被记录在Validation上下文中。
  3. 作为构建app的一部分，Revel会记录验证成功的变量名，并在Validation上下文中使用其作为默认的key（以后用来查询）。
  4. 当上下文非空时Validation.HasErrors()返回true。
  5. Validation.Keep()告诉Revel将ValidationErrors串行化到Flash Cookie。
  6. Revel重定向到Hotels.Settings action。
	
Hotels.Settings action提供一个模版：
   
        {{/* app/views/Hotels/Settings.html */}}
        ...
        {{if .errors}}Please fix errors marked below!{{end}}
        ...
        <p class="{{if .errors.username}}error{{end}}">
    	Username:
    	<input name="username" value="{{.flash.username}}"/>
    	<pan class="error">{{.errors.username.Message}}</span>
        </p>

它做了三件事情：

  1. 如果username字段有错误，则为username键检查errors映射表。
  2. 将flash中的username参数值预填到输入框。
  3. 在下面显示错误信息。（我们没有指定任何错误信息，因为每个验证函数都提供了一个默认值）。
	
**注意：**使用验证错误框架的域模版助手函数使得写模版更方便一点。

##顶部错误信息

如果错误信息被收集在一个单独的地方，模版还可以简化（例如屏幕上方的红色大方块）。  

这与上面的例子仅有两个不同点：

  1. 我们指定了一个Message代替ValidationError的Key。
  2. 我们在表格的上方打印所有信息。
	
下面是代码：
   
        func (c MyApp) SaveUser(username string) revel.Result {
    	// Username字段是必须的，并且它的长度为4-15个字母（包含边界）。
    	c.Validation.Required(username).Message("Please enter a username")
    	c.Validation.MaxSize(username, 15).Message("Username must be at most 15 characters long")
    	c.Validation.MinSize(username, 4).Message("Username must be at least 4 characters long")
    	c.Validation.Match(username, regexp.MustCompile("^\\w*$")).Message("Username must be all letters")
    
    	if c.Validation.HasErrors() {
    	    // 在flash上下文中存储验证错误并重定向。
    	    c.Validation.Keep()
    	    c.FlashParams()
    	    return c.Redirect(Hotels.Settings)
    	}
    
    	// 所有数据已验证!
    	...
        }

此时，模版代码为：
   
        {{/* app/views/Hotels/Settings.html */}}
        ...
        {{if .errors}}
        <div class="error>
    	<ul>
    	{{range .errors}}
    		<li> {{.Message}}
    	{{end}}
    	</ul>
        </div>
        {{end}}
        ...
