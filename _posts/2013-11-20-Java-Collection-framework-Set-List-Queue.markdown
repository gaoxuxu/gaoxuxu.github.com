---
author: gavin
comments: true
date: 2013-11-20 17:28:25
layout: post
title: Java集合框架——Set、List和Queue
categories:
- 算法
- Java
---

##List

`List`就是有序的`Collection`，它也是所有`Collection`中唯一对外界暴露索引的，即可以对`List`中每个元素的插入元素进行控制，或者根据元素的索引访问元素。

`List`与`Set`不同，`List`通常允许重复元素，即满足`(e1.equals(e2))`条件的两个元素。

`List`提供了一个方法可以获得一个受其支撑的`ListIterator`：`listIterator()`，它还有一个变种`listIterator(int index)`。后者以适当顺序返回列表迭代器。`ListIterator`有一个好处是可以进行双向搜索，这在有时候会非常有用，可以参考`Collections`的`iteratorBinarySearch()`。

###ArrayList、Vector和LinkedList

1. `ArrayList`和`Vector`都是基于数组的可随机访问列表，`LinkedList`基于链表，且实现了`List`和`Deque`两个接口。
2. `Vector`是线程安全的，而`ArrayList`和`LinkedList`不是。
3. 添加新元素时，如果`ArrayList`的容量已满，则会将其容量在当前规模的基础上增加一半；相同情况下`Vector`的容量会增加为现有规模的二倍。二者都提供了`trimToSize()`方法可以释放掉没有用到的数组空间。
4. 基本操作运行时间：
    * `get()`：`ArrayList`：$O(1)$，`LinkedList`：$O(n)$
    * `add()`：`ArrayList`：$O(1)$，`LinkedList`：$O(1)$
    * `remove()`：`ArrayList`：$O(n)$，`LinkedList`：$O(1)$
5. `Java`还实现了`Stack`类，它继承自`Vector`，所以也是线程安全的。

##Set

`Set`是一种不包含重复元素的`Collection`，确切地讲，就是不会存在两个满足条件的`(e1.equals(e2))`的元素`e1`和`e2`。`Set`中最多只能有一个`null`，`Set`是对数学概念上“集合”的抽象。

注意：尽量不要将可变元素加入`Set`，如果在外界改变`Set`中某个元素的值（影响到了`equals()`的行为），那么`Set`的行为将是不确定的。

最后注意：`Set`是通过桥接模式由`Map`支撑的，它们之间的关系见下图：

![](http://p.blog.csdn.net/images/p_blog_csdn_net/turkeyzhou/EntryImages/20081105/set.png)

###HashSet、LinkedHashSet和TreeSet

* 如上图，`HashSet`包装的是`HashMap`，`LinkedHashSet`包装的是`LinkedHashMap`，`TreeSet`包装的是`TreeMap`。
* `HashSet`无序，`LinkedHashSet`保持输入时的数据，`TreeSet`是有序的。
* `HashSet`和`LinkedHashSet`基于散列法存储列表信息，`TreeSet`基于树。
* `HashSet`和`LinkedHashSet`添加删除较快，`TreeSet`遍历快。

##Queue

`Queue`是处理元素前用来存储信息的`Collection`。通常以`FIFO`的方式排序各个元素，不过也有其它情况，比如优先队列和`LIFO`队列（或堆栈）。

`Queue`的三类操作：

* 插入操作：`add(e)`和`offer(e)`，如果插入成功，则都返回`true`。在使用有容量限制的队列的情况下，如果当前没有可用空间，则前者会抛出`IllegalStateException`，后者会返回`false`。因此在一般情况下，后者会优于前者。
* 移除操作：`remove()`和`poll()`，这两个方法可以返回并移除队列的头，到底返回哪个元素取决于实现的排序策略。在队列为空时，前者会抛出一个异常，而后者返回`null`。
* 查询操作：`element()`和`peek()`，两者都会返回但不移除队列的头。当队列为空时，前者会抛出一个异常，而后者返回`null`。

`Queue`接口没有定义阻塞队列的方法，详情参考另一篇文章[阻塞队列](http://gaoxuxu.github.io/2013/02/28/blocking-queue/)。




> Written with [StackEdit](https://stackedit.io/).