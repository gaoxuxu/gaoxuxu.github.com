---
author: gavin
comments: false
date: 2013-09-08 16:42:25
layout: post
slug: '%e7%ae%97%e6%b3%95%e5%af%bc%e8%ae%ba%e7%ac%ac%e4%b8%80%e7%ab%a0'
title: 算法导论第一章
wordpress_id: 251
categories:
- 算法
---

## 性能





性能的好与坏，直接决定着可行与不可行，例如对实时的要求。  

性能所扮演的角色就像经济中的货币。





## 算法





算法分析是理论研究，是关于计算机程序性能和资源利用的研究，特别关注性能。  

算法也可以理解为一系列的计算步骤，用来将输入数据转换成输出结果。《The Art of Computer Programming》中对算法的特征归纳为：







  * 输入：一个算法必须有零个或以上输入量。


  * 输出：一个算法应有一个或以上输出量，输出量是算法计算的结果。


  * 明确性：算法的描述必须无歧义，以保证算法的实际执行结果是精确地符合要求或期望，通常要求实际运行结果是确定的。


  * 有限性：依据图灵的定义，一个算法是能够被任何图灵完备系统模拟的一串运算，而图灵机只有有限个状态、有限个输入符号和有限个转移函数（指令）。而一些定义更规定算法必须在有限个步骤内完成任务。


  * 有效性：又称可行性。能够实现，算法中描述的操作都是可以通过已经实现的基本运算执行有限次来实现。





## Exercises 1.1





### 1.1-1





Give a real-world example that requires sorting or a real-world example that requires computing a convex hull.  

Sorting：很常见，比如一份商品列表，可以按销量排序，也可以按价格排序等等。  

Convex Hull：图像处理、模式识别和地理信息系统等。





### 1.1-2





Other than speed, what other measures of efficiency might one use in a real-world setting?  

内存使用率、编程效率、算法的复杂度等。





### 1.1-3





Select a data structure that you hava seen previously,and discuss its strengths and limitations.  

链表是一种很常见的数据结构，其长处是很容易插入数据，插入时时间复杂度只有$$\theta(1)$$，局限性是查找一个节点时的时间复杂度为$$\theta(n)$$，对于有序链表，这个值为$$\theta(lgn)$$。





### 1.1-4





How are the shortest-path and traveling-salesman problems given above similar?How are they different?  

相同点：都是找到最短路线。不同点：查找最短路径不需要经过所有点；而TSP则需要经过所有点，并找出最短路线。





### 1.1-5





Come up with a real-world problem in which only the best solution will do.Then come up with one in which a solution that is “approximately” the best is good enough.  

就拿排序算法来说，数据量小时完全可以用时间复杂度为$$\theta(n^2)$$的算法，不过数据量大时必须使用更好的算法。





## 学习算法的重要性







  * 计算机资源是有限的，好的算法能更有效率地使用有限的时间和空间。 


  * 对于同一个问题，不同的算法对问题的影响往往比硬件和软件方面大。 


  * 作为一种技术，算法无所不在，它处于更核心的位置。 


  * 具有良好的算法基础，可以做更多的事情。





## Exercises 1.2





### 1.2-1





Give an example of an application that requires algorithmic content at the application level,and discuss the function of the algorithms involved.  

地图应用，路径查找。





### 1.2-2





Suppose we are comparing implementations of insertion sort and merge sort on the same machine.For inputs of size _n_,insertion sort runs in $$8n^2$$ steps,while merge sort runs in $$64nlgn$$ steps.For which values of _n_ does insertion sort beat merge sort?  

$$8n^2 \le 64nlgn$$  

$$\Rightarrow n \le 8lgn$$  

$$\Rightarrow 2 \le n \le 43$$  

最后一步求值的代码如下（Golang）：




    
    <code>package main
    
    import "math"
    
    func main() {
        var n float = 2
        for {
            if n <= 8*math.Log2(n) {
                n++
            } else {
                break
            }
        }
        println(n)
    }
    </code>





### 1.2-3





What is the smallest value of _n_ such that an algorithm whose running time is $$100n^2$$ runs faster than an algorithm whose running time is $$2^n$$ on the same machine?  

$$100n^2 \ge 2^n$$  

$$\Rightarrow n > 14$$  

$$\Rightarrow n = 15$$ 求解代码如下:




    
    <code>package main
    
    import "math"
    
    func main() {
        var n float64 = 1
        for {
            if 100*n*n > math.Exp2(n) {
                n++
            } else {
                break
            }
        }
        println(n)
    }
    </code>





## Problems





### _1-1 Comparison of running times_





For each function $$f(n)$$ and time $$t$$ in the following table,determine the largest size $$n$$ of a problem that can be solved in time $$t$$,assuming that the algorithm to solve the problem takes $$f(n)$$ microseconds.  

解决这个问题，有个通解的公式：  

$$\frac{f(n)}{10^6} \le t$$  

其中t的单位为秒。






  


    

    

    
    

      1 second
    

    
    

      1 minute
    

    
    

      1 hour
    

    
    

      1 day
    

    
    

      1 month
    

    
    

      1 year
    

    
    

      1 century
    

  
  
  


    

      $$logn$$
    

    
    

      $$2^{10^6}$$
    

    
    

      $$2^{6\cdot10^7}$$
    

    
    

      $$2^{3.6\cdot10^9}$$
    

    
    

      $$2^{8.64\cdot10^{10}}$$
    

    
    

      $$2^{2.59\cdot10^{11}}$$
    

    
    

      $$2^{3.11\cdot10^{12}}$$
    

    
    

      $$2^{3.11\cdot10^{14}}$$
    

  
  
  


    

      $$\sqrt{n}$$
    

    
    

      $$10^{12}$$
    

    
    

      $$3.6\cdot10^{15}$$
    

    
    

      $$1.296\cdot10^{19}$$
    

    
    

      $$7.46\cdot10^{21}$$
    

    
    

      $$6.72\cdot10^{24}$$
    

    
    

      $$9.67\cdot10^{26}$$
    

    
    

      $$9.67\cdot10^{30}$$
    

  
  
  


    

      $$n$$
    

    
    

      $$10^6$$
    

    
    

      $$6\cdot10^7$$
    

    
    

      $$3.6\cdot10^9$$
    

    
    

      $$8.64\cdot10^{10}$$
    

    
    

      $$2.59\cdot10^{12}$$
    

    
    

      $$3.11\cdot10^{13}$$
    

    
    

      $$3.11\cdot10^{15}$$
    

  
  
  


    

      $$n\lg{n}$$
    

    
    

      $$6.24\cdot10^4$$
    

    
    

      $$2.80\cdot10^6$$
    

    
    

      $$1.33\cdot10^8$$
    

    
    

      $$2.76\cdot10^9$$
    

    
    

      $$7.18\cdot10^{10}$$
    

    
    

      $$7.87\cdot10^{11}$$
    

    
    

      $$6.76\cdot10^{13}$$
    

  
  
  


    

      $$n^2$$
    

    
    

      $$10^3$$
    

    
    

      $$7.75\cdot10^3$$
    

    
    

      $$6\cdot10^4$$
    

    
    

      $$2.94\cdot10^5$$
    

    
    

      $$1.61\cdot10^6$$
    

    
    

      $$5.58\cdot10^6$$
    

    
    

      $$5.58\cdot10^7$$
    

  
  
  


    

      $$n^3$$
    

    
    

      $$100$$
    

    
    

      $$391$$
    

    
    

      $$1532$$
    

    
    

      $$4420$$
    

    
    

      $$13736$$
    

    
    

      $$31440$$
    

    
    

      $$145935$$
    

  
  
  


    

      $$2^n$$
    

    
    

      $$19$$
    

    
    

      $$25$$
    

    
    

      $$31$$
    

    
    

      $$36$$
    

    
    

      $$41$$
    

    
    

      $$44$$
    

    
    

      $$51$$
    

  
  
  


    

      $${n!}$$
    

    
    

      $$9$$
    

    
    

      $$11$$
    

    
    

      $$12$$
    

    
    

      $$13$$
    

    
    

      $$15$$
    

    
    

      $$16$$
    

    
    

      $$17$$
    

  




以上就$$n\lg{n}$$和$$n{!}$$的相关值不好求，还是用golang写了个小程序，过程如下：  

对于$$n\lg{n}$$，可以使用二分法找到合适的n值。  

对于$$n{!}$$，由斯特林公式估计：  

$$\because n! = \sqrt{2\pi{n}}\left(\frac{n}{e}\right)^n$$  

$$\therefore \ln{n!} = n\ln{n} -n + \frac{1}{2}\ln{(2n\pi)}$$  

$$\because 10^l \le n! < 10^{l+1}$$  

$$\therefore l \le \log_{10}{n!} < l+1$$  

$$\therefore n{!}$$的位数为$$\log_{10}{n!} + 1 = \frac{\ln{n!}}{\ln{10}} + 1$$  

$$\therefore n{!}$$的位数为$$\left(\frac{n\ln{n} -n + \frac{1}{2}\ln{(2n\pi)}}{\ln{10}}\right) + 1$$




    
    <code>package main
    
    import "math"
    
    func main() {
    //  var key float64 = 1000000
    //  var low float64 = 1
        println(search_factorial(initHigh(7, 1000000), 1000000))
        println(search_factorial(initHigh(8, 60000000), 60000000))
        println(search_factorial(initHigh(10, 3600000000), 3600000000))
        println(search_factorial(initHigh(11, 86400000000), 86400000000))
        println(search_factorial(initHigh(13, 2592000000000), 2592000000000))
        println(search_factorial(initHigh(15, 31080000000000), 31080000000000))
        println(search_factorial(initHigh(17, 3108000000000000), 3108000000000000))
    //  println(binary_search(low, 1000000, key))
    //  println(binary_search(low, 60000000, 60000000))
    //  println(binary_search(low, 3600000000, 3600000000))
    //  println(binary_search(low, 86400000000, 86400000000))
    //  println(binary_search(low, 2592000000000, 2592000000000))
    //  println(binary_search(low, 31080000000000, 31080000000000))
    //  println(binary_search(low, 3108000000000000, 3108000000000000))
    }
    
    func binary_search(l, h, key float64) float64{
        var mid float64 = (l + h)/2
        var result float64 = f(mid)
        if l >= h {
            return h
        } else {
            if result == key {
                return mid
            }else if result < key {
                return binary_search(mid, h, key)
            }else {
                return binary_search(l, mid, key)
            }
        }
    }
    
    func f(n float64) float64{
    //  return n * math.Log2(n)
        return factorial(n)
    }
    
    func factorial(n float64) float64{
        if n <= 1 {
            return 1
        } else {
            return n * factorial(n - 1)
        }
    }
    
    func initHigh(figures int, key float64) int64{
        var i int64 = 0
        for ; i < int64(key); i++{
            var n float64 = float64(i)
            var fig float64 = (n * math.Log(n) - n + 0.5 * math.Log(2 * n * math.Pi))/math.Log(10) + 1
            if int(fig) > figures {
                return i
            }
        }
        return -1
    }
    
    func search_factorial(h int64, key float64) int64{
        var i int64 = 0
        for ; i < h; i++ {
            var f float64 = factorial(float64(i))
            if f == key {
                return i
            }else if f > key {
                return i - 1
            }
        }
        return h - 1
    }
    </code>





> 
  
> 
> 《算法导论》  

  [维基百科——算法](http://zh.wikipedia.org/wiki/算法)
> 
> 




