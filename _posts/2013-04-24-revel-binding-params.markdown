---
author: gavin
comments: true
date: 2013-04-24 23:56:25
layout: post
slug: revel%e6%a1%86%e6%9e%b6-%e7%bb%91%e5%ae%9a%e5%8f%82%e6%95%b0
title: Revel框架——绑定参数
wordpress_id: 185
categories:
- Go
tags:
- Go
- revel
---

##绑定参数
* * *
Revel尝试尽可能地简单地将参数转换为它们期望的Go类型。这种从字符串到其他类型的转换被称为“数据绑定”。

##参数
* * *
所有的请求参数都被集中在了一个单独的Params对象中。其中包括：

  * URL路径参数
  * URL查询参数
  * 表格数据（multipart或非multipart）
  * 文件上传
	
这里是定义（[godoc](http://robfig.github.io/revel/docs/godoc/binder.html)）:
   
        type Params struct {
            url.Values
            Files map[string][]*multipart.FileHeader
        }

内嵌的url.Values([godoc](http://www.golang.org/pkg/net/url/#Values))提供了一个简单值的存取器，但是开发者会发现使用Revel对任意非字符串值的数据绑定机制更简单。

##Action参数
* * *
参数可以直接被接收为action的方法参数，例如：
    
        func (c AppController) Action(name string, ids []int, user User, img []byte) revel.Result {
            ...
        }
在调用action之前，Revel要求它的Binder将这些参数转换为请求的数据类型。如果转换不成功，参数将会被赋予它的类型的零值。

##Binder
* * *
绑定一个参数到一个数据类型，就要使用Revel的Binder（[godoc](http://robfig.github.io/revel/docs/godoc/binder.html)）。它像下面例子中展示的那样整合参数对象：
   
        func (c SomeController) Action() revel.Result {
            var ids []int = c.Params.Bind("ids[]", reflect.TypeOf([]int{}))
            ...
        }

下列的数据类型支持拆箱：

  * 所有宽度的int
  * 布尔值
  * 所有支持类型的指针
  * 所有支持类型的切片
  * struct
  * 用于日期和时间的time.Time
  * 用于文件上传的*os.File,[]byte,io.Reader,io.ReadSeeker
	
下面的部分描述了这些类型的语法。如果需要更多有用的信息，请参阅[源码](http://robfig.github.io/revel/docs/src/binder.html) 

###布尔值

字符串“true”，“on”和“1”都被认为是true。其它绑定的值将是false。

###Slices

Revel支持两种绑定Slice的语法：有序的和无序的。

有序的：
   
        ?ids[0]=1
        &ids[1]=2
        &ids[3]=4

slice中的结果为[]int{1,2,0,4}

无序的：
   
        ?ids[]=1
        &ids[]=2
        &ids[]=3

slice中的结果为[]int{1,2,3}  
**注意：**当绑定一个struct的slice时，应该仅仅使用有序slice：
    
        ?user[0].Id=1
        &user[0].Name=rob
        &user[1].Id=2
        &user[1].Name=jenny

###Struct

使用一个简单的句点（.）来绑定struct：

        ?user.Id=1
        &user.Name=rob
        &user.Friends[]=2
        &user.Friends[]=3
        &user.Father.Id=5
        &user.Father.Name=Hermes

将要绑定的结构定义为：
    
        type User struct {
            Id int
            Name string
            Friends []int
            Father User
        }

**注意：**为了方便绑定，属性必须是可导出的。

###日期和时间

内建的日期时间格式为SQL标准时间格式["2006-01-02","2006-01-02 15:04"]。

使用[官方模式](http://golang.org/pkg/time/#constants)可以为应用添加更多的格式。简单点的方法是：添加识别模式到TimeFormats变量即可，像这样：
   
        func init() {
            revel.TimeFormats = append(revel.TimeFormats, "01/02/2006")
        }

###文件上传

文件上传可以绑定到下列任意的类型：

  * *os.File
  * []byte
  * io.Reader
  * io.ReadSeeker
	
这是一个通过[Go的multipart包](http://golang.org/pkg/mime/multipart/)提供的围绕上传处理的包装者。字节保留在内存中，除非它们超过阈值（默认为10MB），在那种情况下，它们将被写入到一个临时文件。 **注意：**将一个文件上传绑定到os.File需要Revel将其写入到一个临时文件中,这样会使它比其它类型效率低。

###自定义Binder

应用可以自定义Binder以获得这个框架的优点。

它只需要注册[binder接口](http://robfig.github.io/revel/docs/godoc/binder.html#Binder)并且注册每一个它要访问的类型：
   
        func myBinder(params Params, name string, typ reflect.Type) reflect.Value {
            ...
        }
    
        func init() {
            revel.TypeBinders[reflect.TypeOf(MyType{})] = myBinder
        }
