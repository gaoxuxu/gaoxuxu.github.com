---
author: gavin
comments: true
date: 2013-11-17 18:20:25
layout: post
title: Java集合框架——Collections
categories:
- 算法
- Java
---

本类完全由在`collection`上进行操作或返回`collection`的静态方法组成。包含多种在`collection`上操作的算法，即“包装器（Wrapper）”。注意这些方法如果接收的参数为`null`，都会抛出`NullPointerException`.

`Collections`类的方法主要有以下几个分类：

*   算法类操作。
*   不可修改包装器（这部分的API有问题，可以参考[Google Guava Collections](https://code.google.com/p/guava-libraries/)）。
*   同步包装器。
*   动态类型安全包装器。
*   空集合。
*   单例集合。
*   其它操作。

##算法类操作

许多`List`的算法类操作都有两种实现：一些适用于可随机访问的`List`，另一些适用于顺序访问的`List`。

###排序

有两个为`List`排序的方法：`sort(List<T> list)`和`sort(List<T> list, Comparator<? super T> c)`，前者需要`List`存储的元素类型`T`实现`Comparator`接口。

这两个方法实际排序时使用的都是`Arrays.sort`方法，此方法使用合并排序算法，最坏情况运行时间为$O(n\lg n)$。

###二分查找

`binarySearch(List<? extends Comparable<? super T>> list, T key)`和`binarySearch(List<? extends T> list, T key, Comparator<? super T> c)`。与`sort`方法一样，前者要求`list`中存储的元素类型实现`Comparator`接口。进行二分查找的前提是`list`必须是有序的。

对于可随机访问的`List`和`size <= 5000`的顺序访问`List`，这两个方法都使用`indexedBinarySearch`进行查找，对于大于此阈值的顺序访问`List`，使用`iteratorBinarySearch`查找:

    public static <T>
    int binarySearch(List<? extends Comparable<? super T>> list, T key) {
        if (list instanceof RandomAccess || list.size()<BINARYSEARCH_THRESHOLD)
            return Collections.indexedBinarySearch(list, key);
        else
            return Collections.iteratorBinarySearch(list, key);
    }

前者与后者的区别在于定位中间元素时的策略，前者使用`list.get(mid)`，而后者利用了`ListIterator`来进行移动。在规模较大时，后者的效率是很显著的。

###反转

`reverse(List<?> list)`会将`list`中元素的顺序倒转，以线性时间运行。

###洗牌算法（shuffle）

此方法会将`list`中的元素打乱顺序。同样有两种实现，对可随机访问的以及规模小于阈值`5`的`List`直接调用`swap(List list, int i, int j)`，否则先以数组形式打乱顺序，再使用迭代器`set`。

###交换元素（swap）

同样有两种实现：

    public static void swap(List<?> list, int i, int j) {
        final List l = list;
        l.set(i, l.set(j, l.get(i)));
    }

    private static void swap(Object[] arr, int i, int j) {
        Object tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }

###使用指定元素填充（fill）

`fill(List<? super T> list, T obj)`可以将`list`中的所有元素都替换为`obj`。

###将一个List中的元素复制到另一个List中

`copy(List<? super T> dest, List<? extends T> src)`将`src`中的元素复制到`dest`中，`dest`至少要与`src`的长度相同。

###最大值与最小值

有四个相关方法，都以线性时间运行。

    <T extends Object & Comparable<? super T>> T min(Collection<? extends T> coll);
    <T> T min(Collection<? extends T> coll, Comparator<? super T> comp);
    <T extends Object & Comparable<? super T>> T max(Collection<? extends T> coll);
    <T> T max(Collection<? extends T> coll, Comparator<? super T> comp)

###回转（rotate）

当调用`Collections.rotate(list, distance)`后，`list`中位于索引`i`的将是之前位于索引`(i - distance) mod list.size()`的元素。此方法可以用于在列表中回转移动一个或多个元素，`distance`就是移动的距离。

###全部替换（replaceAll）

`boolean replaceAll(List<T> list, T oldVal, T newVal)`，将`list`中所有值为`oldVal`的元素都替换为`newVal`。

###子列表第一次出现的位置（indexOfSubList）

`int indexOfSubList(List<?> source, List<?> target)`：如果`target`存在于`source`中，则返回`target`在`source`中第一次出现的位置（即满足这样条件的i——`source.subList(i, i+target.size()).equals(target)`）。如果不存在，则返回`-1`。

###子列表最后一次出现的位置（lastIndexOfSubList）

`lastIndexOfSubList`方法与`indexOfSubList`方法类似，只不过前者从高位索引开始向前查找，而后者从低位索引开始向后查找。

##不可修改包装器

此类方法提供将`Collection`变为无法修改的`Collection`的包装器，不过有个问题，如果修改（指`add`或`remove`操作）源`Collection`，照样可以成功。

##同步包装器

这部分操作返回一个线程安全的集合。需要注意的是，在返回的集合上进行迭代时，必须手工同步：

    Collection c = Collections.synchronizedCollection(myCollection);
        ...
    synchronized (c) {
        Iterator i = c.iterator(); // Must be in the synchronized block
        while (i.hasNext())
            foo(i.next());
    }

或者

    Map m = Collections.synchronizedMap(new HashMap());
        ...
    Set s = m.keySet();  // Needn't be in synchronized block
        ...
    synchronized(m) {  // Synchronizing on m, not s!
        Iterator i = s.iterator(); // Must be in synchronized block
        while (i.hasNext())
            foo(i.next());
    }

##动态类型安全包装器

此类操作会返回指定集合的动态类型安全版本。试图在返回的集合中插入一个类型错误的元素会立即抛出`ClassCastException`。

##空集合

此类操作会返回类型安全且可序列化的空集合，此类空集合的作用与赋值字段相同，但是类型安全的。

##单例集合

此类操作返回的集合仅仅包含指定的元素，并且是不可修改的。

##其它操作

###nCopies

`List<T> nCopies(int n, T o)`返回一个不可修改的`List`，此`List`中包含`n`个`o`的副本。返回的`List`是可序列化的。

###reverseOrder

有两个版本：`reverseOrder()`强行逆转了实现`Comparator`接口的对象的自然顺序；`reverseOrder(Comparator<T> cmp)`强行逆转了指定比较器`cmp`的顺序。两者返回的比较器都是可序列化的。

###枚举类操作（enumeration和list）

`Enumeration<T> enumeration(final Collection<T> c)`返回一个指定`Collection`上的枚举。

`ArrayList<T> list(Enumeration<T> e)`返回一个与指定枚举返回顺序相同的数组列表。

###指定元素出现的频率

`int frequency(Collection<?> c, Object o)`方法返回指定`Collection`中指定对象的元素数。确切地讲，返回`c`中满足`(o == null ? e == null : o.equals(e))`的元素`e`的个数。

###两个Collection的交集是否为空

`boolean disjoint(Collection<?> c1, Collection<?> c2)`：如果`c1`与`c2`中没有相同的元素，则返回`true`。在使用此方法时，要保证两个`Collection`都使用相同的相等性测试方法。

需要注意的是，在两个`Collection`都为`null`时，返回值是`true`。

###addAll

`boolean addAll(Collection<? super T> c, T... elements)`方法与`c.addAll(Arrays.asList(elements))`的行为相同，但大多数实现下要比它快很多。

###newSetFromMap

`newSetFromMap(Map<E, Boolean> map)`方法返回指定`Map`支撑的`Set`，返回的`Set`与底层的`Map`有同样的顺序、并发性和性能特征。每次在返回的`Set`上调用方法都将导致在底层的`Map`或其`KeySet`上调用该方法一次，并伴随一个异常。

调用此方法时，指定的`Map`必须为空，并且不能在此方法返回后直接访问。一种可以保证这些条件的代码方法如下：

    Set<Object> weakHashSet = Collections.newSetFromMap(
        new WeakHashMap<Object, Boolean>());

###asLifoQueue

`Queue<T> asLifoQueue(Deque<T> deque)`可以返回一个`Deque`（双向队列）的后进先出形式的`Queue`。方法`add`被映射到`push`，`remove`被映射到`pop`等等。

> Written with [StackEdit](https://stackedit.io/).
