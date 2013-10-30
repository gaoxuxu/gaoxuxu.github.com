---
author: gavin
comments: true
date: 2013-10-29 22:34:25
layout: post
title: 算法导论第六章——堆排序
categories:
- 算法
---

#堆排序（Heapsort）

******

##二叉堆

--------------

一般所说的堆排序都是基于二叉堆的。二叉堆数据结构是一种数组对象，它可以被视为一颗完全二叉树。表示堆的数组`A`是一个具有两个属性的对象：

* `A.length`是数据中的元素个数，`A.heap-size`是堆中存放于`A`中的元素个数，有`0<=A.heap-size<=A.length`。
* 树的根元素是`A[1]`，给定节点的索引`i`，我们可以很容易计算它的父节点、左子节点和右子节点的索引。  

        PARENT(i)
        1   return ⌊i/2⌋
        
        LEFT(i)
        1   return 2i
        
        RIGHT(i)
        1   return 2i + 1
        
二叉堆有两种：

* 最大堆：`A[PARENT(i)]>=A[i]`
* 最小堆：`A[PARENT(i)]<=A[i]`

堆中某个节点的高度定义为从本节点到叶子节点的最长简单下降路径上的数目，并且定义堆的高度为树根的高度。因为具有`n`个元素的堆是基于一颗完全二叉树的，因此其高度为`⌊lgn⌋`。下面是五个基本过程：

* `MAX-HEAPIFY`，运行时间为$O(\lg n)$，是保持最大堆特性的关键。
* `BUILD-MAX-HEAP`，运行时间为$O(n)$，可以构造一个基于无序输入数组的最大堆。
* `HEAPSORT`，运行时间为$O(n\lg n)$，将一个数组原地排序。
* `MAX-HEAP-INSERT`，`HEAP-EXTRACT-MAX`，`HEAP-INCREASE-KEY`，和`HEAP-MAXIMUM`。它们的运行时间都为$O(\lg n)$，可以让堆数据结构实现优先队列。

###Maintaining the heap property

**输入：**数组`A`和下标`i`。

**前提：**假设根在索引`LEFT(i)`和`RIGHT(i)`的二叉树都是最大堆。

**输出：**根在索引`i`的二叉树为最大堆。

伪代码：

    MAX-HEAPIFY(A,i)
    1   l = LEFT(i)
    2   r = RIGHT(i)
    3   if l<=A.heap_size and A[l]>A[i]
    4       largest = l
    5   else largest = i
    6   if r<=A.heap_size and A[r]>A[largest]
    7       largest = r
    8   if largest != i
    9       exchange A[i] with A[largest]
    10      MAX-HEAPIFY(A,largest)

当最底层恰好半满时，`i`节点的子树大小最多为`2n/3`。因此，`MAX-HEAPIFY`的运行时间为

$$
T(n)\le T(2n/3)+\Theta(1)=\Theta(\lg n)=\Theta(h)
$$

###Building a heap

**输入：**一个无序数组。

**输出：**一个最大堆

    BUILD-MAX-HEAP(A)
    1   A.heap_size = A.length
    2   for i = ⌊A.length/2⌋ downto 1
    3       MAX-HEAPIFY(A,i)

###正确性

**初始化：**针对有`n`个元素的数组`A`，第一轮迭代之前有`i=⌊n/2⌋`，而节点`⌊n/2⌋+1`、`⌊n/2⌋+2`…`n`都是叶子节点。

**保持：**由于节点`i`的子节点的编号总比`i`大，根据循环不变式，这些子节点都是最大堆的根。`MAX-HEAPIFY`的调用保持了节点`i+1`、`i+2`…`n`为最大堆的根的性质。递减`i`为下一次迭代重新建立循环不变式。

**终止：**当`i=0`时，循环终止。通过循环不变式，可知所有节点都是最大堆的根。

###运行时间

**简单界：**可以简单认为，调用`MAX-HEAPIFY`过程的次数为$O(n)$，且每一次调用都用时$O(\lg n)$，因此可以认为其运行时间为$O(n\lg n)$。

**紧确界：**在树中不同高度的节点处调用`MAX-HEAPIFY`的时间不同。有这样一个性质：一个$n$元素堆的高度为$\lfloor\lg n\rfloor$，并且在任意高度$h$上，至多有$\lceil n/2^{h+1}\rceil$个节点。由于`MAX-HEAPIFY`过程的运行时间为$O(h)$，所以可得`BUILD-MAX-HEAP`的运行时间为

$$
T(n)=\sum _{h=0}^{\lfloor\lg n\rfloor}\left\lceil\frac{n}{2^{h+1}}\right\rceil O(h)=O\left(n\sum _{h=0}^{\lfloor\lg n\rfloor}\frac{h}{2^h}\right)=O(n)
$$

###The heapsort algorithm

给定一个输入数组`A`，堆排序算法的执行过程如下：

1. 首先调用`BUILD-MAX-HEAP`过程将输入数组构造成一个最大堆。
2. 此时数组中的最大元素为`A[1]`，将它与最后一个元素`A[n]`交换。
3. 通过减小堆大小的方式丢弃最后一个节点，并且在新的根节点上调用`MAX-HEAPIFY`过程。
4. 重复这种丢弃节点的过程，直到还剩1个节点，至此数组是有序的。

伪代码：

    HEAPSORT(A)
    1   BUILD-MAX-HEAP(A)
    2   for i=A.length downto 2
    3       exchange A[1] and A[n]
    4       A.heap_size = A.heap_size - 1
    5       MAX-HEAPIFY(A,1)

很明显，堆排序算法的运行时间为$T(n)=O(n)+(n-1)(O(1)+O(\lg n))=O(n\lg n)$。

##Priority queue

优先级队列是堆数据结构的一种常见应用。队列也有两种：最大优先级队列和最小优先级队列。下面讨论的是基于最大堆实现的最大优先级队列。

优先队列是一种用来维护由一组元素构成的集合`S`的数据结构，这一组元素中的每一个都有一个关联的被称为`key`的值。一个最大优先队列支持以下操作：

**INSERT(S，x)** 将元素`x`插入集合`S`，相当于操作$S=S\cup\{x\}$。

**MAXIMUM(S)** 返回`S`中具有最大`key`的元素。

**EXTRACT-MAX(S)** 移除并返回`S`中具有最大`key`的元素。

**INCREASE-KEY(S,x,k)** 将`S`中元素`x`的`key`增加到新值`k`，这里`k>=x.key`。

`HEAP-MAXIMUM`过程用$\Theta(1)$实现实现了`MAXIMUM`操作：

    HEAP-MAXIMUM(A)
    1   return A[1]

程序`HEAP-EXTRACT-MAX`实现了`EXTRACT-MAX`操作，它的运行时间为$O(\lg n)$：

    HEAP-EXTRACT-MAX(A)
    1   if A.heap_size < 1
    2       error "heap underflow"
    3   max = A[1]
    4   A[1] = A[A.heap_size]
    5   A.heap_size = A.heap_size - 1
    6   MAX-HEAPIFY(A,1)
    7   return max

程序`HEAP-INCREASE-KEY`实现了`INCREASE-KEY`操作，它的运行时间为$O(\lg n)$：

    HEAP-INCREASE-KEY(A,i,key)
    1   if key < A[i]
    2       error "new key is smaller than current key"
    3   A[i] = key
    4   while i > 1 and A[PARENT] < A[i]
    5       exchange A[i] with A[PARENT]
    6       i = PARENT(i)

程序`MAX-HEAP-INSERT`实现了`INSERT`操作。它的运行时间为$O(\lg n)$：

    MAX-HEAP-INSERT(A,key)
    1   A.heap_size = A.heap_size + 1
    2   A[A.heap_size] = -∞
    3   HEAP-INCREASE-KEY(A,A.heap_size,key)

##Problems

###6-1 Building a heap using insertion

We can build a heap by repeatedly calling `MAX-HEAP-INSERT` to insert the elements into the heap. Consider the following variation on the `BUILD-MAX-HEAP` procedure:

    BUILD-MAX-HEAP'(A)
    1   A.heap_size = 1
    2   for i = 2 to A.length
    3       MAX-HEAP-INSERT(A,A[i])

**a.** Do the procedures `BUILD-MAX-HEAP` and `BUILD-MAX-HEAP'` always create the same heap when run on the same input array? Prove that they do, or provide a counterexample.

不一样，比如考虑数组$\langle 1,2,3\rangle$，前者的结果中，左节点为`2`，而后者的左节点为`1`。

**b.** Show that in the worst case, `BUILD-MAX-HEAP'` requires $\Theta(n\lg n)$ time to build an $n$-element heap.

循环的次数为$n-1$，`MAX-HEAP-INSERT(A,A[i])`过程的运行时间为$O(\lg n)$，所以`BUILD-MAX-HEAP'`的运行时间上界为$O(n\lg n)$，其下界也很容易得到：

$$
\begin{equation}\begin{split}\
\sum _{i=1}^{n}\Theta(\lfloor\lg i\rfloor)\quad&\ge\quad\sum _{i=\lceil n/2\rceil}^n\Theta(\lfloor\lg \lceil n/2\rceil\rfloor)\\
&\ge\quad\sum _{i=\lceil n/2\rceil}^n\Theta(\lfloor\lg(n/2)\rfloor)\\
&=\quad\sum _{i=\lceil n/2\rceil}^n\Theta(\lfloor\lg n-1\rfloor)\\
&\ge\quad n/2\cdot\Theta(\lg n)\\
&=\quad\Omega(n\lg n)\
\end{split}\end{equation}
$$

###6-2 Analysis of d-ary heaps
A ***d-ary heap*** is like a binary heap, but (with one possible exception) non-leaf nodes have `d` children instead of `2` children.

**a.** How would you represent a `d-ary` heap in an array?

    D-ARY-PARENT(i)
    1   return (i-2)/d + 1
    
    D-ARY-CHILD(i,j)
    1   return (i-1)*d + j+1

**b.** What is the height of a `d-ary heap` of `n` elements in terms of `n` and `d` ?

高度为$\Theta(\log _dn)$

**c.** Give an efficient implementation of `EXTRACT-MAX` in a `d-ary` max-heap. Analyze its running time in terms of `d` and `n`.

运行时间为$T(n)=d\cdot O(\log _dn)=O(\log _dn)$，关键是`MAX-HEAPIFY`。

**d.** Give an efficient implementation of `INSERT` in a `d-ary` max-heap. Analyze its running time in terms of `d` and `n`.

运行时间为$T(n)=O(\log _dn)$

**e.** Give an efficient implementation of `INCREASE-KEY(A, i, k)`, which flags an error if `k < A[i]`, but otherwise sets `A[i] = k` and then updates the `d-ary` max-heap structure appropriately. Analyze its running time in terms of `d` and `n`.

与`d`部分一样。

###6-3 Young tableaus

An $m\times n$ ***Young tableau*** is an $m\times n$ matrix such that the entries of each row are in sorted order from left to right and the entries of each column are in sorted order from top to bottom. Some of the entries of a Young tableau may be $\infty$, which we treat as nonexistent elements. Thus, a Young tableau can be used to hold $r\le mn$ finite numbers.

**a.** Draw a $4\times 4$ Young tableau containing the elements $\{9, 16, 3, 2, 4, 8, 5, 14, 12\}$

略！

**b.** Argue that an $m\times n$ Young tableau $Y$ is empty if $Y[1, 1]=\infty$. Argue that $Y$ is full (contains $mn$ elements) if $Y[m, n] < \infty$.

很明显，`Y[1,1]`是`Y`中的最小值；而`Y[m,n]`是`Y`中的最大值。

**c.** Give an algorithm to implement `EXTRACT-MIN` on a nonempty $m\times n$ Young tableau that runs in $O(m+n)$ time. Your algorithm should use a recursive subroutine that solves an $m\times n$ problem by recursively solving either an $(m-1)\times n$ or an $m\times(n-1)$ subproblem. (Hint: Think about `MAX-HEAPIFY`.) Define $T(p)$, where $p=m+n$, to be the maximum running time of `EXTRACT-MIN` on any $m\times n$ Young tableau. Give and solve a recurrence for $T(p)$ that yields the $O(m+n)$ time bound.



**d.** Show how to insert a new element into a nonfull $m\times n$ Young tableau in $O(m+n)$ time.

**e.** Using no other sorting method as a subroutine, show how to use an $n\times n$ Young tableau to sort $n^2$ numbers in $O(n^3)$ time.

**f.** Give an $O(m+n)$-time algorithm to determine whether a given number is stored in a given $m\times n$ Young tableau.



> Written with [StackEdit](https://stackedit.io/).