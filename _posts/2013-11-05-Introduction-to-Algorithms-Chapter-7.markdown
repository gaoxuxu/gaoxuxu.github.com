---
author: gavin
comments: true
date: 2013-10-29 22:34:25
layout: post
title: 算法导论第七章——快速排序
categories:
- 算法
---

##快速排序

* 最坏运行时间为$\Theta(n^2)$。
* 期望运行时间为$\Theta(n\lg n)$，且常数因子很小。
* 原地排序。
* 基于分治模式的。

对一个子数组`A[p,...,r]`，三个步骤为：

**分解：** 将数组`A[p,...,r]`分解（Partition）为两个（可能为空的）子数组`A[p,..,q-1]`和`A[q+1,..,r]`，`A[p,..,q-1]`中的每一个元素都小于等于`A[q]`，而`A[q]`小于等于`A[q+1,..,r]`中的每一个元素。分解的过程会计算出下标`q`的值。

**解决：** 递归调用快速排序将子数组`A[p,..,q-1]`和`A[q+1,..,r]`排序。

**合并：** 因为子数组都已经排好序，所以合并这一步不用做任何事情。

快速排序的伪代码

    QUICKSORT(A,p,r)
    1   if p < r
    2       q = PARTITION(A,p,r)
    3       QUICKSORT(A,p,q-1)
    4       QUICKSORT(A,q+1,r)
    
    PARTITION(A,p,r)
    1   x = A[r]
    2   i = p - 1
    3   for j= p to r-1
    4       if A[j] <= x
    5           i = i + 1
    6           exchange A[i] with A[j]
    7   exchange A[i+1] with A[r]
    8   return i + 1

很明显，`PARTITION`过程的`3-6`行表示，在每一次迭代前，对任意的数组下标`k`，

1. 如果`p<=k<=i`，则`A[k]<=x`。
2. 如果`i+1<=k<=j-1`，则`A[k]>=x`。
3. 如果`k=r`，则`A[k]=x`。

**循环的正确性**可证明如下：

**初始化：** 在循环的第一次迭代前，`i = p-1`并且`j = p`。因为`p`和`i`之间没有任何值，并且`i+1`和`j-1`之间没有任何值，所以上一部分最后的三个条件中前两个都成立，而第`1`行的赋值语句也使第三个条件成立。

**保持：** 有两种情况：

1. `A[j]>x`，则将`A[i+1]`和`A[j]`互换，并递增`i`和`j`。此时条件1与条件2都成立，条件3也成立。
2. `A[j]<=x`，则只递增`j`。此时条件1、2、3都成立。

**终止：** 当循环终止时，有`j = r`，此时`A[p,...,i]`中的每一个元素都小于或等于`A[r]`，而`A[r]`小于或等于`A[i+1,...,r-1]`中的每一个元素。

##从直觉上分析快速排序的性能

* 快速排序的运行时间与划分是否对称有关。
* 如果划分是对称的，那么从渐近意义上来讲就与合并算法一样快。
* 如果划分是不对称的，那么从渐近意义上来讲就与插入算法一样慢。

###最坏情况划分

这种情况下划分的两个区域分别是：包含`n-1`个元素的集合和一个空集。假设算法的每一次递归调用中都出现了这种不对称划分，划分的时间代价为$\Theta(n)$。由于对一个大小为`0`的数组进行递归调用的时间代价为$T(0)=T(1)$，所以算法的运行时间可以表示为

$$
T(n)=T(n-1)+T(0)+\Theta(n)=T(n-1)+\Theta(n)=\Theta(n^2)
$$

###最好情况划分

最好情况，也就是最平衡的划分，即两个子问题的大小都不超过$n/2$，此时其中一个子问题的大小为$\lfloor n/2\rfloor$，另一个子问题的大小为$\lceil n/2\rceil - 1$。此时算法的运行时间为

$$
T(n)\le 2T(n/2)+\Theta(n)=\Theta(n\lg n)
$$

###Exercises 7.2-6$\star$

Argue that for any constant $0<\alpha\le 1/2$,the probability is approximately $1-2\alpha$ that on a random input array,`PARTITION` produces a split more balanced than $1-\alpha$ to $\alpha$.

$$
\begin{matrix}\
\begin{matrix}\alpha \\
\overbrace{-----}\end{matrix}\begin{matrix}1-\alpha\\
\overbrace{\begin{matrix}\underbrace{----}\\
1-2\alpha\end{matrix}\begin{matrix}\underbrace{-----}\\
\alpha\end{matrix}}\end{matrix}\
\end{matrix}
$$

图很粗糙，由图可以看出，对常数$0<\alpha\le 1/2$，总有$1-2\alpha$的概率使划分比$1-\alpha:\alpha$更对称。

##快速排序的随机化版本

对主元进行随机化取样，可以确保在子数组的`r-p+1`个元素中，主元元素`x=A[r]`等可能地取其中一个。

    RANDOMIZED-PARTITION(A,p,r)
    1   i = RANDOM(p,r)
    2   exchange A[r] with A[i]
    3   return PARTITION(A,p,r)
    
    RANDOMIZED-QUICKSORT(A,p,r)
    1   if p < r
    2       q = RANDOMIZED-PARTITION(A,p,r)
    3       RANDOMIZED-QUICKSORT(A,p,r)
    4       RANDOMIZED-QUICKSORT(A,p,r)

##快速排序性能的严格分析

###最坏情况分析

设$T(n)$是`QUICKSORT`作用于规模为$n$的输入上的最坏情况时间，假设$T(n)\le cn^2$，则有

$$
\begin{equation}\begin{split}\
T(n)&=\max _{0\le q\le n-1}(T(q)+T(n-q-1))+\Theta(n)\\
&\le \max _{0\le q\le n-1}(cq^2+c(n-q-1)^2)+\Theta(n)\\
&=c\cdot \max _{0\le q\le n-1}(q^2+(n-q-1)^2)+\Theta(n)\\
&\le c\cdot (n-1)^2+\Theta(n)\\
&=cn^2-c(2n-1)+\Theta(n)\\
&\le cn^2\
\end{split}\end{equation}
$$

###期望的运行时间

1. `QUICKSORT`的运行时间是由花在过程`PARTITION`上的时间所决定的。
2. 每次调用`PARTITION`过程时都会选出一个主元，并且后续对`QUICKSORT`和`PARTTION`的各次递归调用都不包含该主元元素。所以，整个快速排序算法的执行过程中，最多只可能调用`PARTITION`过程$n$次。
3. 调用一次`PARTITION`过程的时间为$O(1)$加上一段时间，这段时间与第`3-6`行中`for`循环中的迭代次数成正比。由于`for`循环中每一次迭代都要进行一次比较，因此，只要清楚第`4`行执行的总次数，就能够给出`QUICKSORT`所花时间的界了。

**引理7.1** 设当`QUICKSORT`在一个包含$n$个元素的数组上运行时，`PARTITION`在第`4`行中所做比较的次数为$X$。那么，`QUICKSORT`的运行时间为$O(n+X)$。

**证明：** 根据上面提到的3点，对`PARTITION`的调用共有`n`次，每一次调用都需做固定量的工作，再执行若干次`for`循环，在`for`循环的每一次迭代中，都要执行第`4`行。

为了计算出$X$，必须了解算法什么时候要对数组中的两个元素进行比较，什么时候不进行比较。为便于分析，将数组$A$的各个元素命名为$z _1,z _2,\cdots,z _n$，其中$z _i$是数组$A$中第$i$小的元素。此外，还定义$Z _{ij}=\{z _i,z _{i+1},\cdots,z _j\}$为$z _i$和$z _j$之间（包含边界）的元素集合。定义

$$
X _{ij}=I\{z _i\ is\ compared\ to\ z _j\}
$$

观察可以发现，每对元素最多比较一次。并且在一次`PARTITION`调用结束后，该次调用所用到的主元元素就再也不会与其它任何元素进行比较了。所以算法所执行的总的比较次数

$$
X=\sum _{i=1}^{n-1}\sum _{j=i+1}^{n}X _{ij}
$$

对上式两边取期望值，可以得到

$$
\begin{equation}\begin{split}\
E[X]\quad&=\quad E\left[\sum _{i=1}^{n-1}\sum _{j=i+1}^{n}X _{ij}\right]\\
&=\quad \sum _{i=1}^{n-1}\sum _{j=i+1}^{n}E[X _{ij}]\\
&=\quad \sum _{i=1}^{n-1}\sum _{j=i+1}^{n}\Pr\{z _i\ is\ compared\ to\ z _j\}\
\end{split}\end{equation}
$$

由于当主元落在$z _i$和$z _j$之间时，$z _i$和$z _j$不可能进行比较。所以

$$
\begin{equation}\begin{split}\
\Pr\{z _i\ is\ compared\ to\ z _j\}\ &=\ \Pr\{z _i\ or\ z _j\ is\ first\ pivot\ chosen\ from \ Z _{ij}\}\\
&=\ \Pr\{z _i\ is\ first\ pivot\ chosen\ from \ Z _{ij}\}\\
&\quad\quad+\Pr\{z _j\ is\ first\ pivot\ chosen\ from \ Z _{ij}\}\\
&=\ \frac{1}{j-i+1}+\frac{1}{j-i+1}\\
&=\ \frac{2}{j-i+1}\
\end{split}\end{equation}
$$

综合上面两式，令$k=j-i$，可以得到

$$
\begin{equation}\begin{split}\
E[X]\quad&=\quad\sum _{i=1}^{n-1}\sum _{j=i+1}^{n}\frac{2}{j-i+1}\\
&=\quad\sum _{i=1}^{n-1}\sum _{j=i+1}^{n}\frac{2}{k+1}\\
&<\quad\sum _{i=1}^{n-1}\sum _{j=i+1}^{n}\frac{2}{k}\\
&=\quad\sum _{i=1}^{n-1}O(\lg n)\\
&=\quad O(n\lg n)\
\end{split}\end{equation}
$$

##Problems

###7-1 ***Hoare partition correctness***

The version of `PARTITION` given in this chapter is not the original partitioning algorithm. Here is the original partition algorithm,which is due to C.A.R.Hoare:

    HOARE-PARTITION(A,p,r)
    1   x = A[p]
    2   i = p - 1
    3   j = r + 1
    4   while TRUE
    5       repeat
    6           j = j - 1
    7       until A[j]<=x
    8       repeat
    9           i = i + 1
    10      until A[i]>=x
    11      if i < j
    12          exchange A[j] with A[i]
    13      else return j


> Written with [StackEdit](https://stackedit.io/).
