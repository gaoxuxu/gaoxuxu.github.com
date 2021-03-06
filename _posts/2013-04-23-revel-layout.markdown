---
author: gavin
comments: true
date: 2013-04-23 17:57:39
layout: post
slug: revel%e6%a1%86%e6%9e%b6-%e7%bb%93%e6%9e%84%e5%b8%83%e5%b1%80
title: Revel框架——结构布局
wordpress_id: 172
categories:
- Go
tags:
- Go
- revel
---

##结构
Revel规定它自身和用户应用都要安装在通过Go命令行工具规定的GOPATH布局中。（参阅[“GOPATH环境变量”](http://golang.org/cmd/go/)）

##布局示例

下面是一个名为sample的Revel应用的默认布局：
    
        gocode                  GOPATH根目录
          src                   GOPATH源码文件夹
            revel               Revel源码
              ...
            sample              应用根目录
              app               App源码目录
                controllers     App controllers目录
                  init.go       拦截者注册器
                models          App域模型
                views           Templates（模版）
              tests             Test suites测试套件
              conf              配置文件目录
                app.conf        主配置文件
                routes          路由定义
              messages          消息文件
              public            公共目录
                css             CSS文件目录
                js              Javascript文件目录
                images          图片文件目录

##app/目录

app/目录包含你的应用的源码以及模版。

  *	app/controllers
  * app/models
  * app/views
	
Revel规定：

  * 所有的模版（template）都在app/views目录下
  * 所有的Controller都在app/controllers目录下
	
除此之外，应用可以按它希望的结构组织代码。Revel会监视app/目录下的所有文件夹，并在监视到改变时重新build应用。任何依赖于app/之外的改变不会被监视到，当必须这样时，就得程序员负责重新编译。

另外，Revel在启动时会导入app/（或导入的模块）下所有包含init()方法的包，以此来确保程序员所有的代码被初始化。

controllers/init.go文件是约定的本地用来注册所有拦截者的钩子（hooks）。对于同一个包中的源文件，init()方法的顺序是没有定义的，因此在同一个文件中收集所有的拦截者定义允许程序员指定（并且知道）它们运行的顺序。（它也可以用于未来其它顺序敏感的初始化）

##conf/目录

conf目录包含应用的配置文件，下面是两个主要的配置文件：

  * app.conf，应用主要的配置文件，它包含标准的配置参数
  * routes，路由定义文件
	
##messages/目录

messages目录包含所有本地的消息文件。

##public/目录

存储在public目录的资源是通过Web Server直接保存的静态资产。一般它被分割为三个标准的子文件夹来存放图片、CSS样式以及JavaScript文件。
