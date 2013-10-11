---
author: gavin
comments: true
date: 2013-04-17 14:24:37
layout: post
slug: go%e8%af%ad%e8%a8%80%e4%b9%8b%e6%97%85-%e5%9f%ba%e6%9c%ac%e6%a6%82%e5%bf%b5%ef%bc%88%e4%ba%8c%ef%bc%89
title: Go语言之旅——基本概念（二）
wordpress_id: 145
categories:
- Go
tags:
- map
- range
- slice
- struct
- switch
---

##结构体
结构体是字段的集合，Go中使用type关键字来声明它。

        package main
    
        import "fmt" 
	    type Vertex struct { 
	        X int
	        y int
	    } 

	    func main() {
	        fmt.Println(Vertex{1, 2}) 
	    }

可以使用点号来访问结构体字段：

        package main
    
        import "fmt"
    
        type Vertex struct {
            X int
    		Y int
        }
    
        func main() {
	    	v := Vertex{1, 2}
    		v.X = 4
	    	fmt.Println(v.X)
        }

Go有指针，但是没有指针运算。可以通过指针访问结构体字段，通过指针的间接访问是透明的。

        package main
    
        import "fmt"
    
        type Vertex struct {
        	X int
	    	Y int
        }
    
        func main() {
	    	p := Vertex{1, 2}
	    	q := &p
	    	q.X = 1e9
	    	fmt.Println(p)
        }

结构体字面量（Struct Literals）表示通过列出结构体字段的值来新分配一个结构体（Literal这个词不好理解，详情猛击[维基百科](http://en.wikipedia.org/wiki/Literal_(computer_programming))）。

使用Name:语法可以只列出一部分字段，而且和字段名的顺序无关。

使用特殊的前缀&会构造一个结构体字面量的指针。

        package main
    
        import "fmt"
    
        type Vertex struct {
	    	X, Y int
        }
    
        var (
	    	p = Vertex{1, 2} // has type Vertex
	    	q = &Vertex{1, 2} // has type *Vertex
	    	r = Vertex{X: 1} // Y:0 is implicit
	    	s = Vertex{} // X:0 and Y:0
        )
    
        func main() {
	    	fmt.Println(p, q, r, s)
        }

new(T)表达式分配一个零初始化的T值，并返回一个指向它的指针。

        var t *T = new(T)

或：

        t := new(T)

示例：

        package main
    
        import "fmt"
    
        type Vertex struct {
	    	X, Y int
        }
    
        func main() {
	    	v := new(Vertex)
	    	fmt.Println(v)
	    	v.X, v.Y = 11, 9
	    	fmt.Println(v)
        }

##Slices（切片）
slice指向一个数组的值，同时还包含有长度信息。[]T是一个元素类型为T的slice。

        package main
    
        import "fmt"
    
        func main() {
    		p := []int{2, 3, 5, 7, 11, 13}
    		fmt.Println("p ==", p)
    
    		for i := 0; i < len(p); i++ {
   		 	    fmt.Printf("p[%d] == %d\n", i, p[i])	
  		  	}
        }

slice可以重新切片，即创建一个新的切片指向同一个数组。

表达式：

        s[lo:hi]

表示从lo到hi-1的元素的一个切片，包含边界。

        s[lo:lo]

为空，但是：

        s[lo:lo+1]

有一个元素。示例代码：

        package main
    
        import "fmt"
    
        func main() {
    		p := []int{2, 3, 5, 7, 11, 13}
    		fmt.Println("p ==", p)
    		fmt.Println("p[1:4] ==", p[1:4])
    
    		// missing low index implies 0
   		 	fmt.Println("p[:3] ==", p[:3])
    
    		// missing high index implies len(s)
   		 	fmt.Println("p[4:] ==", p[4:])
        }

使用make函数创建slice，它分配一个归零的数组并返回这个数组的slice：

        a := make([]int, 5) // len(a)=5

要指定容量的话，就要使用第三个参数：

        b := make([]int, 0, 5) // len(b)=0, cap(b)=5
    
        b = b[:cap(b)] // len(b)=5, cap(b)=5
        b = b[1:]      // len(b)=4, cap(b)=4

示例代码：

        package main
    
        import "fmt"
    
        func main() {
	    	a := make([]int, 5)
	    	printSlice("a", a)
	    	b := make([]int, 0, 5)
	    	printSlice("b", b)
	    	c := b[:2]
	    	printSlice("c", c)
	    	d := c[2:5]
	    	printSlice("d", d)
        }
    
        func printSlice(s string, x []int) {
	    	fmt.Printf("%s len=%d cap=%d %v\n",
	    		s, len(x), cap(x), x)
        }

slice的零值是nil。一个nil slice的长度和容量都是0。

        package main
    
        import "fmt"
    
        func main() {
    		var z []int
    		fmt.Println(z, len(z), cap(z))
  		  	if z == nil {
				fmt.Println("nil!")
  			}
        }

##Range
使用for循环的range形式可以遍历map或slice：

        package main
    
        import "fmt"
    
        var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}
    
        func main() {
	    	for i, v := range pow {
	                fmt.Printf("2**%d = %d\n", i, v)
	    	}
        }

如果想要略过索引或值，可以将其设置为_。如果只想使用索引，可以直接去掉“,value”部分：

        package main
    
        import "fmt"
    
        func main() {
	    	pow := make([]int, 10)
	    	for i, value := range pow {
	    	    fmt.Printf("%d : %d\n", i, value)	
	    	}
	    	for i := range pow {
	    	    pow[i] = 1 << uint(i)
	    	}
	    	for _, value := range pow {
	    	    fmt.Printf("%d\n", value)
	    	}
        }

##Map
map是映射键到值的映射表。map必须在使用前用make函数创建（不是new），一个值为nil的map是空的，并且不能被赋值：

        package main
    
        import "fmt"
    
        type Vertex struct {
	    	Lat, Long float64
        }
    
        var m map[string]Vertex
    
        func main() {
	    	m = make(map[string]Vertex)
	    	m["Bell Labs"] = Vertex{
	    	    40.68433, -74.39967,
	    	}
	    	fmt.Println(m["Bell Labs"])
        }

map字面量与结构体字面量相似，只不过必须有键：

        package main
    
        import "fmt"
    
        type Vertex struct {
 		   	Lat, Long float64
        }
    
        var m = map[string]Vertex{
 		   	"Bell Labs" : Vertex{
 		   	    40.68433, -74.39967,
 		   	},
    		"Google" : Vertex{
    		    37.42202, -122.08408,
    		},
        }
    
        func main() {
    		fmt.Println(m)
        }

如果顶层类型只有类型名的话，就可以忽略字面量中元素的类型：

        package main
    
        import "fmt"
    
        type Vertex struct {
 		   	Lat, Long float64
        }
    
        var m = map[string]Vertex{
 		   	"Bell Labs" : {40.68433, -74.39967},
 		   	"Google" : {37.42202, -122.08408},
        }
    
        func main() {
 		   	fmt.Println(m)
        }

在map m中插入或更新一个元素：

        m[key] = elem

获得一个元素：

        elem = m[key]

删除一个元素：

        delete[m, key]

通过双赋值来检测某个键是否存在：

        // 如果m中存在key，ok为true；否则ok为false，并且elem的值为map元素类型的零值。
        // 同样，如果读取一个不存在于map中的元素时，得到的将会是map中元素类型的零值。
        elem, ok := m[key]
    
        package main
    
        import "fmt"
    
        func main() {
	    	m := make(map[string]int)
    
	    	m["Answer"] = 42
	    	fmt.Println("The value:", m["Answer"])
    
	    	m["Answer"] = 48
	    	fmt.Println("The value:", m["Answer"])
    
	    	delete(m, "Answer")
	    	fmt.Println("The value:", m["Answer"])
    
	    	v, ok := m["Answer"]
	    	fmt.Println("The value:", v, "Present?", ok)
        }

##函数
函数也是值：

        package main
    
        import(
    		"fmt"
    		"math"
        )
    
        func main() {
	    	hypot := func(x, y float64) float64 {
	    	    return math.Sqrt(x*x + y*y)
	    	}
    	
	    	fmt.Println(hypot(3, 4))
        }

函数是完全闭包的。下例中，函数adder返回一个闭包，每个闭包都被绑定到了它自己的sum变量上：

        package main
    
        import "fmt"
    
        func adder() func(int) int {
	    	sum := 0
	    	return func(x int) int {
	    	    sum += x
	    	    return sum
	    	}
        }
    
        func main() {
	    	pos, neg := adder(), adder()
	    	for i := 0; i < 10; i++ {
	    	    fmt.Println(
		    		pos(i),
		    		neg(-2*i),
	    	    )
	    	}
        }

##Switch
case体是自动break的，除非它以一个fallthrough语句结束。

        package main
    
        import (
 		   	"fmt"
 		   	"runtime"
        )
    
        func main() {
 		   	fmt.Print("Go runs on ")
 		   	switch os := runtime.GOOS; os {
 		   	case "darwin":
 		   	    fmt.Println("OS X.")
 		   	case "linux":
 		   	    fmt.Println("Linux.")
 		   	case "windows":
 		   	    fmt.Println("Windows.")
 		   	    fallthrough
 		   	default:
 		   	    // freesd, openbsd
 		   	    // plan9
 		   	    fmt.Printf("%s.", os)
 		   	}
        }

switch的条件从上到下执行，当匹配成功时停止：

        package main
    
        import (
	    	"fmt"
	    	"time"
        )
    
        func main() {
	    	fmt.Println("When's Saturday?")
	    	today := time.Now().Weekday()
	    	switch time.Saturday {
		   	case today + 0:
    		    fmt.Println("Today.")
    		case today + 1:
    		    fmt.Println("Tomorrow.")
    		case today + 2:
    		    fmt.Println("In two days.")
    		default:
    		    fmt.Println("Too far aways.") 
			} 
		}

没有条件的switch与switch true一样。这种构造可以以一种更简洁的方式书写if-then-else：

        package main
    
        import (
	    	"fmt"
	    	"time"
        )
    
        func main() {
	    	t := time.Now()
	    	switch {
	    	case t.Hour() < 12:
	    	    fmt.Println("Good morning!")
	    	case t.Hour() < 17:
	    	    fmt.Println("Good afternoon.")
	    	default:
	    	    fmt.Println("Good evening")
	    	}
        }
