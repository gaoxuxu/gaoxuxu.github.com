---
author: gavin
comments: true
date: 2013-11-10 15:11:25
layout: post
title: 算法导论第八章——线性排序
categories:
- 算法
---

前面学到的排序都是基于输入元素间的比较，被称为**比较排序**。

##决策树模型

比较排序可以被抽象地视为**决策树**。一棵决策树是一棵满二叉树，表示某排序算法作用于给定输入所做的所有比较，而控制结构、数据移动等都被忽略了。

在决策树中，对每个内节点都注明`i:j`，其中`1<=i,j<=n`，`n`是输入序列中的元素个数。对每个叶节点都注明排列$\langle\pi(1),\pi(2),\cdots,\pi(n)\rangle$。排序算法的执行对应于遍历一条从树的根到叶节点的路径。

**定理**  任意一个比较算法在最坏情况下，都需要做$\Omega(n\lg n)$次的比较。

**证明**  考虑一棵高度为$h$，具有$l$个可达叶节点的决策树。对于规模为$n$的输入，共有$n!$种排列。所以有$n!\le l$，又因该树中叶子的数目最多为$2^h$，所以$n!\le 2^h$，有$h\ge\lg(n!)\ge\Omega(n\lg n)$。

##计数排序

计数排序假设`n`个输入元素中的每一个都是介于`0`到`k`之间的整数，此处`k`为某个整数。当$k=O(n)$时，计数排序的运行时间为$\Theta(n)$。

计数排序的基本思想就是对每一个输入元素`x`，确定出小于`x`的元素个数。有了这一信息，就可以把`x`直接放到它在最终输出数组中的位置上。当有几个元素相同时，这个方案要略做修改，因为不能把它们放在同一个输出位置上。

在计数排序算法的代码中，假定输入是数组`A[1..n]`，`length[A]=n`。另外需要两个数组：存放排序结果的`B[1..n]`，以及提供临时存储区的`C[0..k]`。

    COUNTING-SORT(A,B,k)
    1   let C[0..k] be a new array
    2   for i=0 to k
    3       C[i] = 0
    4   for j= 1 to A.length
    5       C[A[j]] = C[A[j]] + 1
    6   // C[i] now contains the number of elements equal to i
    7   for i = 1 to k
    8       C[i] = C[i] + C[i-1]
    9   // C[i] now contains the number of elements less than or equal to i
    19  for j = A.length downto 1
    11      B[C[A[j]]] = A[j]
    12      C[A[j]] = C[A[j]] - 1

##基数排序

**基数排序（radix sort）**是一种用在老式穿卡机上的算法。其原理是将整数按**位数**切割成不同的数字，然后按每个位数分别比较。

它是这样实现的：将所有待比较数值（正整数）统一为同样的数位长度，数位较短的数前面补零。然后，从最低位开始，依次进行一次排序。这样从最低位排序一直到最高位排序完成以后, 数列就变成一个有序序列。

关于这个算法，很重要的一点就是按位排序要稳定。

假设输入数组`A`的长度为`n`，且每个数字都有`d`位，其中第`1`位为最低位，第`d`位为最高位，代码如下：

    RADIX-SORT(A,d)
    1   for i = 1 to d
    2       use a stable sort to sort array A on digit i

**引理8.3** 给定`n`个`d`位数，每一个数位可以取`k`种可能的值。基数排序算法能以$\Theta(d(n+k))$的时间正确地对这些数排序。

**证明** 当每位数字都介于`0`到`k-1`，且`k`不太大时，可以使用计数排序。此时基数排序的总时间为$d\cdot\Theta(n+k)=\Theta(d(n+k))$。

**引理8.4** 给定`n`个`b-bit`数字和任意正整数$r\le b$，`RADIX-SORT`能在$\Theta((b/r)(n+2^r))$时间内正确地对这些数进行排序。

**证明** 对于有`n`个`b-bit`数字的数组，排序的时间是$\Theta(b(n+2))$。对于所有的正整数$r\le b$，可以令$d = \lceil b/r\rceil$，此时$k=2^r$，排序所用时间为$\Theta((b/r)(n+2^r))$。

##桶排序

桶排序的思想就是把区间`[0,1)`划分成`n`个相同大小的子区间，或称桶。然后，将`n`个输入数分布到各个桶中去。因为输入数均匀分布在`[0,1)`上，所以，一般不会有很多数落在一个桶中的情况。为得到结果，先对各个桶中的数进行排序，然后按次序把各桶中的元素列出来即可。

    BUCKET-SORT(A)
    1   let B[0...n-1] be a new array
    2   n = A.length
    3   for i = 0 to n-1
    4       make B[i] an empty list
    5   for i = 1 to n
    6       insert A[i] into list B[nA[i]]
    7   for i = 0 to n-1
    8       sort list B[i] with insertion sort
    9   concatenate the lists B[0],B[1],...,B[n-1] together in order

###桶排序的运行时间

除第8行外，其它各行在最坏情况下的时间都是$O(n)$，要计算桶排序的运行时间，只需要分析$n$次调用插入排序的时间即可。

设$n _ i$为表示桶$B[i]$中元素个数的随机变量，则桶排序的运行时间为

$$
T(n)=\Theta(n)+\sum _ {i=0}^{n-1}O(n _ i^2)
$$

对上式两边取期望，并利用期望的线性性质，有

$$
\begin{equation}\begin{split}\
E[T(n)]&=E\left[\Theta(n)+\sum _ {i=0}^{n-1}O(n _ i^2)\right]\\
&=\Theta(n)+\sum _ {i=0}^{n-1}E[O(n _ i^2)]\\
&=\Theta(n)+\sum _ {i=0}^{n-1}O(E[n _ i^2])\
\end{split}\end{equation}
$$

对$i=0,1,2,\dots,n$和$j=1,2,\dots,n$，定义指示器随机变量

$$
X _ {ij}=I\{A[j]\ falls\ in\ bucket\ i\}
$$

因此，有

$$
n _ i=\sum _ {j=1}^nX _ {ij}\\
\begin{equation}\begin{split}\
E[n _ i^2]&=E\left[\left(\sum _ {j=1}^nX _ {ij}\right)^2\right]\\
&=E\left[\sum _ {j=1}^n\sum _ {k=1}^nX _ {ij}X _ {ik}\right]\\
&=E\left[\sum _ {j=1}^nX _ {ij}^2+\sum _ {1\le j\le n}\sum _ {1\le k\le n\ k\ne j}X _ {ij}X _ {ik}\right]\\
&=\sum _ {j=1}^nE[X _ {ij}^2]+\sum _ {1\le j\le n}\sum _ {1\le k\le n\ k\ne j}E[X _ {ij}X _ {ik}]\
\end{split}\end{equation}
$$

由于指示器随机变量$X _ {ij}$为$1$的概率为$1/n$，其它情况为$0$。于是

$$
E[X _ {ij}^2]=1\cdot\frac{1}{n}+0\cdot\left(1-\frac{1}{n}\right)=\frac{1}{n}\\
E[X _ {ij}X _ {ik}]=E[X _ {ij}]E[X _ {ik}]=\frac{1}{n}\cdot\frac{1}{n}=\frac{1}{n^2}
$$

于是可得

$$
\begin{equation}\begin{split}\
E[n _ i^2]&=\sum _ {j=1}^n\frac{1}{n}+\sum _ {1\le j\le n}\sum _ {1\le k\le n\ k\ne j}\frac{1}{n^2}\\
&=n\cdot\frac{1}{n}+n(n-1)\cdot\frac{1}{n^2}\\
&=1+\frac{n-1}{n}\\
&=2-\frac{1}{n}\
\end{split}\end{equation}
$$

于是可得桶排序的运行时间

$$
T(n)=\Theta(n)+n\cdot O(2-1/n)=\Theta(n)
$$


> Written with [StackEdit](https://stackedit.io/).
