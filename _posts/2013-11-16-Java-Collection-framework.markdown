---
author: gavin
comments: true
date: 2013-11-16 11:16:25
layout: post
title: Java集合框架
categories:
- 算法
- Java
---

看完《算法导论》的基本数据结构部分，突然想起我从来都没系统地总结过Java集合框架的知识，所以趁着这段时间好好写几篇这方面的笔记。

##Java集合框架

![Class diagram of Java Collections framework](http://www.codejava.net/images/articles/javacore/collections/collections%20framework%20overview.png)

如图，`Collection`和`Map`接口是Java集合框架的两个顶层接口。其中`Collection`接口继承了`Iterable`接口，因此可以用增强`for`循环遍历任何`Collection`。

`Collection`接口描述的方法列表如下：

* 查询类方法：
    * `int size()`：返回集合中的元素个数，如果元素个数大于`Integer.MAX_VALUE`，则返回`Integer.MAX_VALUE`。
    * `boolean isEmpty()`：如果集合中没有元素，则返回`true`。
    * `boolean contains(Object o)`：如果集合中至少有一个元素`e`满足条件`o==null ? e == null : o.equals(e)`，则返回`true`。
    * `Iterator<E> iterator<E>()`：返回一个可以对集合中元素进行迭代的迭代器。需要注意的是，这里不保证返回的元素的顺序（除非这个集合的类型提供了一个保证机制）。
    * `Object[] toArray()`：返回一个包含集合中所有元素的数组。数组中元素的顺序与集合的迭代器返回元素的顺序相同。返回的数组是“安全”的，即集合不维护对它的引用（也就是说，即使集合是基于数组的，这个也必定会分配一个新的数组）。这个方法也是基于数组和基于集合的API之间的桥梁。
    * `<T> T[] toArray(T[] a)`：功能与`toArray()`相同，不过此方法允许对输出数组的运行时类型进行精确控制，并且在某些情况下可以节省分配开销。如果指定的数组能容纳该集合，并有剩余空间，那么会将数组中紧接集合尾部的元素设置为`null`；如果指定的数组无法容纳该集合，则分配一个具有指定运行时类型和此集合大小的新数组。
* 修改操作：
    * `boolean add(E e)`：这个方法会确保集合包含指定的元素（可选操作）。如果集合由于调用而被改变，则返回`true`；如果集合不允许元素重复并且已经包含了指定元素，则返回`false`。需要注意的是，如果集合由于某些原因（除已包含该元素的原因）拒绝添加指定元素，那么它应该抛出一个异常而不是返回`false`。
    * `boolean remove(Object o)`：从集合中移除一个指定元素的单个实例，如果存在的话（可选操作）。确切地讲，就是如果集合中有一个或多个满足条件`(o == null ? e == null : o.equals(e))`的元素`e`，则移除一个这样的元素。如果集合由于调用而被改变，则返回`true`。
* 批处理操作（这类方法实际使用中比较重要，后面会详细说明）：
    * `boolean containsAll(Collection<?> c)`：如果集合中包含指定集合中所有的元素，则返回`true`。
    * `boolean addAll(Collection<? extends E> c)`：将指定集合中的所有元素都添加到集合中（可选操作）。如果在操作的同时修改指定的集合，那么结果将是不确定的。
    * `boolean removeAll(Collection<?> c)`：从集合中移除同时存在于指定集合中的元素（可选操作）。此操作后，此集合和指定集合的交集为空集。
    * `boolean retainAll(Collection<?> c)`：仅保留存在于指定集合中的元素（可选操作）。即操作后的集合是此集合与指定集合的交集。
    * `void clear()`：移除集合中的所有元素（可选操作），方法返回后此集合将为空。
* 比较和散列：`equals()`和`hashCode()`，都是继承自`Object`类的。

##Collection中的批处理

###addAll

如果集合是基于数组的，那么使用这个方法要比`Collections.addAll()`快很多，因为基于数组的集合使用`native`的`System.copyarray()`方法。

如果集合是基于链表的，那么`Collections.addAll()`要比这个方法快很多，是因为后者需要先调用`c.toArray`。

###removeAll

批量删除，一个经典错误就是：

    for(int i = 0; i < someint; i++) {
        list.remove(i);
    }

如果这样写代码，就会出错，因为在集合上的每一次`remove`都会改变集合的`size`及元素下标，所以上面的代码要么无法达到我们的期望，要么会抛出异常。

解决问题的方法就是使用`iterator.remove()`，这种操作是安全的。`AbstractCollection`中实现的`removeAll()`核心代码如下：

    Iterator<?> it = iterator();
    while (it.hasNext()) {
        if (c.contains(it.next())) {
            it.remove();
        }
    }

`ArrayList`中重写了`removeAll`方法，它会将集合中不包含在指定集合中的元素前移，最后将哨兵位置前置。

###retainAll

此方法解决的问题与`removeAll`类似。

下一篇写`Collections`类。


> Written with [StackEdit](https://stackedit.io/).
