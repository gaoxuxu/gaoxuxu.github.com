---
author: gavin
comments: true
date: 2013-04-17 17:21:25
layout: post
slug: go%e8%af%ad%e8%a8%80%e4%b9%8b%e6%97%85-%e5%b9%b6%e5%8f%91
title: Go语言之旅——并发
wordpress_id: 136
categories:
- Go
tags:
- channal
- Go
- goroutine
- select
---

##Goroutine
goroutine是由Go运行时管理的轻量级线程。表达式  

        go f(x, y, z)

会在一个新的goroutine中执行  
   
        f(x, y, z)

其中f、x、y、z是在当前goroutine中赋值的，并且f的执行是在新的goroutine中进行的。

goroutine在同一个地址空间中运行，因此访问共享内存时必须同步。sync包提供了这种功能，但是在Go中这种方法并不常用（接下来会看到）

        package main
    
        import (
            "fmt"
            "time"
        )
    
        func say(s string) {
            for i := 0; i < 5; i++ {
                time.Sleep(100 * time.Millisecond)
                fmt.Println(s)
            }
        }
    
        func main() {
            go say("world")
            say("hello")
        }

##Channel
Channel是一种通过Channel操作符<-发送和接收值的有类型的渠道：  

        ch <- v // Send v to channel ch.
        v := <-ch // Receive from ch, and assign value to v.

箭头的方向就是数据流动的方向。与map和slice一样，channel必须在使用前就创建：  

        ch := make(chan int)

默认情况下，在另一端准备好前发送和接收都将阻塞。这将允许goroutine在没有明确的锁和条件变量时可以同步。  

        package main
    
        import "fmt"
    
        func sum(a []int, c chan int) {
            sum := 0
            for _, v := range a {
                sum += v
            }
            c <- sum // Send sum to c
        }
    
        func main() {
            a := []int{7, 2, 8, -9, 4, 0}
    
            c := make(chan int)
            go sum(a[:len(a)/2], c)
            go sum(a[len(a)/2:], c)
            x, y := <-c, <-c // receive from c
    
            fmt.Println(x, y, x+y)
        }

Channel也可以是带缓冲的，在初始化Channel时，为make函数第二参数的位置上提供一个缓冲区长度。  

        ch := make(chan int, 100)

只有在缓冲区满时发送数据才会阻塞，缓冲区为空时接收数据会阻塞。修改下面的例子，使缓冲区被填满，然后看看会发生什么。  

        package main
    
        import "fmt"
    
        func main() {
            c := make(chan int, 2)
            c <- 1
            c <- 2
            fmt.Println(<-c)
            fmt.Println(<-c)
        }

发送者可以close一个Channel来表明它将不会发送更多的值。接收者也可以提供赋值语句的第二个参数来测试Channel是否被关闭。  

        v, ok := <-ch

如果接收不到更多值并且Channel已被关闭，ok的值就是false。表达式  

        for i := range c

循环从Channel接收值，直到Channel被关闭。

注意：只有发送者才应该关闭Channel，而不是接收者。在一个已关闭的Channel上发送数据将会触发一个panic。

另外需要注意的是：Channel不同于文件，通常不需要关闭它们。只有在接收者不需要更多的值时才有必要关闭Channel，比如停止一个range循环。

        package main
    
        import "fmt"
    
        func fibonacci (n int, c chan int) {
            x, y := 0, 1
            for i := 0; i < n; i++ {
                c <- x
                x, y = y, x+y
            }
            close(c)
        }
    
        func main() {
            c := make(chan int, 10)
            go fibonacci(cap(c), c)
            for i := range c {
                fmt.Println(i)
            }
        }

##Select
select语句可以让goroutine等待多个通讯操作。select会阻塞，直到某个case可以运行，随即执行这个case。当多个case都准备好时，它会随机选择一个。  

        package main
    
        import "fmt"
    
        func fibonacci(c, quit chan int) {
            x, y := 0, 1
            for {
                select {
                case c <- x:
                    x, y = y, x+y
                case <-quit:
                    fmt.Println("quit")
                    return
                }
            }
        }
    
        func main() {
            c := make(chan int)
            quit := make(chan int)
            go func() {
                for i := 0; i < 10; i++ {
                    fmt.Println(<-c)
                }
                quit <- 0
            }()
            fibonacci(c, quit)
        }

如果其它case都没有准备好，select就会执行default case。尝试在default情况下不阻塞地发送和接收。  
   
        select {
        case i := <-c:
            // use i
        default:
            // receiving from c would block.
        }

示例代码：  

        package main
    
        import (
            "fmt"
            "time"
        )
    
        func main() {
            tick := time.Tick(1e8)
            boom := time.After(5e8)
            for {
                select {
                case <-tick:
                    fmt.Println("tick.")
                case <-boom:
                    fmt.Println("BOOM!")
                    return
                default:
                    fmt.Println("	.")
                    time.Sleep(5e7)
                }
            }
        }
