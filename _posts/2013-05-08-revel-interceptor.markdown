---
author: gavin
comments: true
date: 2013-05-08 15:16:51
layout: post
slug: revel%e6%a1%86%e6%9e%b6%e4%b9%8b%e6%8b%a6%e6%88%aa%e5%99%a8
title: Revel框架之拦截器
wordpress_id: 212
categories:
- Go
tags:
- Go
- revel
---

## 拦截器
拦截器是在一个Action实施之前或之后由框架调用的一个函数。它允许一种面向方面编程的形式，这对一些常见的问题非常有用：

  * 请求记录
  * 错误处理
  * 状态保持

在Revel中，一个拦截器可以有下面两种形式之一：

  1. 功能拦截器：一个匹配InterceptorFunc接口的函数。
    * 无法访问特殊的应用Controller调用。
    * 可以施加到应用中任意/所有的Controller。
  2. 方法拦截器：不接受参数的Controller方法，并且返回一个revel.Result。
    * 只能拦截绑定的Controller的调用。
    * 可以随意修改调用的Controller。

拦截器以被添加的顺序调用。

## 拦截时间

一个拦截器可以在请求生命周期的四个时间点上注册并运行：

  1. BEFORE：请求被路由，Session、Flash和参数被解码后，Action运行前。
  2. AFTER：请求返回一个Result之后，返回的Result被应用前。如果Action得到一个panic，这些拦截器就不会运行。
  3. PANIC：一个panic退出Action或返回Result之后。
  4. FINALLY：一个Action完成并且返回的Result被应用后。

## Results

拦截器通常返回nil，在这种情况下请求会被继续处理，而不中断。  

返回一个非空的revel.Result的结果依赖于拦截器运行的时间。

  1. BEFORE:没有进一步的拦截器被调用。
  2. AFTER：所有的拦截器仍然会运行。
  3. PANIC：所有的拦截器仍然会运行。
  4. FINALLY：所有的拦截器仍然会运行。

在所有情况下，所有返回的Result会替换现有的Result。  

然而，在BEFORE情况下，返回的Result肯定是最终的。而在AFTER情况下，有一种可能是进一步的拦截器可以发出自己的Result。

## 示例

### 功能拦截器

下面是一个定义和注册功能拦截器的简单例子：
    
    func checkUser(c *revel.Controller) revel.Result {
        if user := connected(c); user == nil {
            c.Flash.Error("Please log in first")
            return c.Redirect(Application.Index)
        }
        return nil
    }
    
    func init() {
        revel.InterceptFunc(checkUser, revel.BEFORE, &Hotels;{})
    }

### 方法拦截器

方法拦截器的签名可以是下列两项之一：
    
    func (c AppController) example() revel.Result
    func (c *AppController) example() revel.Result

下面是只能在应用Controller上运作的例子：
   
    func (c Hotels) checkUser() revel.Result {
        if user := connected(c); user == nil {
            c.Flash.Error("Please log in first")
            return c.Redirect(Application.Index)
        }
        return nil
    }
    
    func init() {
        revel.InterceptMethod(checkUser, revel.BEFORE)
    }
