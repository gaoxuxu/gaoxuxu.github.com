---
author: gavin
comments: false
date: 2013-09-28 15:19:06
layout: post
slug: '%e7%ae%97%e6%b3%95%e5%af%bc%e8%ae%ba%ef%bc%88%e7%ac%ac%e4%ba%8c%e7%ab%a0%ef%bc%89'
title: 算法导论（第二章）
wordpress_id: 293
categories:
- 算法
---

##Insertion Sort
**Input**:A sequence of *n* numbers $$\langle a_1,a_2,\cdots,a_n \rangle$$  
**Output**:A permutation (reordering) $$\langle a_1^,, a_2^,,\cdots,a_n^,\rangle$$  
**INSERTION SORT(A)**  

	for j = 2 to A.length
		key = A[j]
		//Insert A[j] into the sorted sequence A[1..j-1].
		i = j - 1
		while i > 0 and A[i] > key
			A[i + 1] = A[i]
			i = i - 1
		A[i + 1] = key

##Exercises 2.1

###2.1-1

Using Figure 2.2 as a model,illustrate the operation of INSERTION-SORT on the array $$A = \langle 31, 41, 59, 26, 41, 38 \rangle$$.  
$$a. \langle 31, 41, 59, 26, 41, 38 \rangle$$  
$$b. \langle 31, 41, 59, 26, 41, 38 \rangle$$  
$$c. \langle 26, 31, 41, 59, 41, 38 \rangle$$  
$$d. \langle 26, 31, 41, 41, 59, 38 \rangle$$  
$$e. \langle 26, 31, 38, 41, 41, 59 \rangle$$  

###2.1-2

Rewrite the INSERTION-SORT procedure to sort into nonincreasing instead of nondecreasing order.  

	for j = 2 to A.length
		key = A[j]
		i = j - 1
		while i > 0 and A[i] < key
			A[i + 1] = A[i]
			i = i - 1
		A[i + 1] = key

###2.1-3

Consider the *searching problem:*  
**Input:** A sequence of $$n$$ numbers $$A = \langle a_1, a_2, \cdots, a_n\rangle$$ and a value $$v$$.  
**Output:** An index $$i$$ such that $$v = A[i]$$ or the special value **NIL** if $$v$$ does not appear in $$A$$.  
Write pseudocode for *linear search*,which scans through the sequence,looking for $$v$$.Using a loop invariant,prove that your algorighm is correct.Make sure that your loop invariant fulfills the three necessary properties.  

	i = NIL
	for j = 1 to A.length
		if A[j] = v
			i = j
			break

###2.1-4

Consider the problem of adding two *n*-bit binary integers,stored in two *n*-element arrays *A* and *B*.The sum of the two integers should be stored in binary form in an $$(n + 1)$$-element array *C*.State the problem formally and write pseudocode for adding the two integers.  

	r = 0
	for j = n to 1//这里的顺序是反向的，即数组的最后一位正好是二进制数的第一位
		if A[j] = B[j] then
			C[j + 1] = r
			r = A[j]
		else
			C[j + 1] = (r + 1) % 2
	C[1] = r

有一种更普遍的算法：

	C = [0,...,0]
	for j = 1 to n
		sum = A[j] + B[j] + C[j]
		C[j] = sum % 2
		C[j + 1] = sum / 2

这个算法的证明如下：  
根据题意，如果不考虑进位，则有

$$
\langle c_1,\cdots, c_n, 0\rangle\iff\langle a_1+b_1,\cdots,a_n+b_n,0\rangle
$$

再考虑进位，则有

$$
\langle (a_1+b_1)\%2,a_2+b_2+(a_1+b_1)/2,\cdots,a_n+b_n,0\rangle
$$

还有一种算法，其实质与上一个算法相同：

	c = 0
	for j = 1 to n
		d = ⌊(A[j] + B[j] + c) / 2⌋
		C[j] = A[j] + B[j] + c - 2⋅d
		c = d
	C[n + 1] = c

##Analyzing algorithms

假设每一行伪代码的执行时间都是常数。

###INSERTION-SORT

第$$(1)$$行：			cost:	$$c_1$$			times:	$$n$$  
第$$(2)$$行：			cost:	$$c_2$$			times:	$$n-1$$  
第$$(3)$$行：			cost:	$$c_3 = 0$$		times:	$$n-1$$  
第$$(4)$$行：			cost:	$$c_4$$			times:	$$n-1$$  
第$$(5)$$行：			cost:	$$c_5$$			times:	$$\sum_{j=2}^nt_j$$  
第$$(6)$$行：			cost:	$$c_6$$			times:	$$\sum_{j=2}^n(t_j-1)$$  
第$$(7)$$行：			cost:	$$c_7$$			times:	$$\sum_{j=2}^n(t_j-1)$$  
第$$(8)$$行：			cost:	$$c_8$$			times:	$$n-1$$  

由上可知，INSERTION-SORT的总时间为：

$$
T(n) = c_1n + c_2(n-1) + c_4(n-1) + c_5\sum_{j=2}^nt_j + c_6\sum_{j=2}^n(t_j-1) + c_7\sum_{j=2}^n(t_j-1) + c_8(n-1)
$$

最佳情况下：

$$
\begin{equation}\begin{split}T(n) &= c_1n + c_2(n-1) + c_4(n-1) + c_5(n-1) + c_8(n-1)\\&= (c_1 + c_2n + c_4n + c_5 + c_8)n - (c_2 + c_4 + c_5 + c_8)\\&= an + b\end{split}\end{equation}
$$

最坏情况下：
由于

$$
\sum_{j=2}^nj = \frac{n(n + 1)}{2} - 1
$$

并且

$$
\sum_{j=2}^n(j-1) = \frac{n(n - 1)}{2}
$$

因此

$$
\begin{equation}\begin{split}T(n) &= c_1n + c_2(n - 1) + c_4(n-1) + c_5\left(\frac{n(n + 1)}{2} - 1\right) + c_6\left(\frac{n(n-1)}{2}\right) + c_7\left(\frac{n(n-1)}{2}\right) + c_8(n-1)\\&= \left(\frac{c_5 + c_6 + c_7}{2}\right)n^2 + \left(c_1 + c_2 + c_4 + \frac{c_5}{2} - \frac{c_6}{2} - \frac{c_7}{2} + c_8\right)n - (c_2 + c_4 + c_5 + c_8)\\&= a^,n^2 + b^,n + c\end{split}\end{equation}
$$

##Exercises 2.2

###2.2-1

Express the function $$n^3/1000 - 100n^2 -100n + 3$$ in terms of $$\Theta$$-notation.

$$
\Theta(n^3)
$$

###2.2-2

Consider sorting *n* numbers stored in array *A* by first finding the smallest element of *A* and exchanging it with the element in *A*[1].Then find the second smallest element of *A*,and exchange it with *A*[2].Continue in this manner for the first *n - 1* elements of *A*.Write pseudocode for this algorithm,which is known as **selection sort**.What loop invariant does this algorithm maintian?Why does it need to run for only the first *n - 1* elements,rather than for all *n* elements?Give the best-case and worst-case running times of selection sort in $$\Theta$$-notation.  

	SELECTION-SORT(A):
	for j = 1 to A.length - 1
		k = j
		i = j + 1
		while i < A.length + 1 and A[i] < A[k]
			k = i
			i = i + 1
		t = A[k]
		A[k] = A[j]
		A[j] = t

根据以上伪代码：

$$
\begin{equation}\begin{split}T(n) &= c_1n + c_2(n - 1) + c_3(n - 1) + c_4\sum_{j=2}^nt_j + c_5\sum_{j=2}^n(t_j - 1) + c_6\sum_{j=2}^n(t_j - 1) + c_7(n - 1) + c_8(n - 1) + c_9(n - 1)\\&= c_1n + c_2(n - 1) + c_3(n - 1) + c_4\left(\frac{n(n + 1)}{2} - 1\right) + c_5\left(\frac{n(n - 1)}{2}\right) + c_6\left(\frac{n(n - 1)}{2}\right) + c_7(n - 1) + c_8(n - 1) + c_9(n - 1)\\&= \left(\frac{c_4 + c_5 + c_6}{2}\right)n^2 + \left(c_1 + c_2 +c_3 + \frac{c_4}{2} - \frac{c_5}{2} - \frac{c_6}{2} + c_7 + c_8 + c_9\right)n - (c_2 + c_3 + c_4 + c_7 + c_8 + c_9)\\&= an^2 + bn + c \\&= \Theta(n^2)\end{split}\end{equation}
$$

###2.2-3

Consider linear search again (see Exercises 2.1-3).How many elements of the input sequence need to be checked on the average,assuming that the element being searched for is equally likely to be any element int the array?How about in the worst case？What are the average-case and worst-case running times of linear search in $$\Theta$$-notation?Justify your answers.  
Best Case: $$1$$  
Worst Case: $$n$$  
Avarge Case: $$\frac{n + 1}{2}$$
  
###2.2-4

How can we modify almost any algorithm to have a good best-case running time?  
要写出一个好的算法，应该把问题尽量拆分，然后根据拆分的问题组织算法。比如在排序算法中，我们可以先将其预先排成局部有序，再整体排序。

##Designing algorithms

###The divide-and-conquer approach

“分治策略”：将原问题划分成*n*个规模较小而结构与原问题相似的子问题；递归地解决这些子问题，然后再合并其结果，最终得到原问题的解。  
分治模式在每一层递归上都有3个步骤：

* **分解（Divide）：**将原问题分解成一系列子问题；
* **解决（Conquer）：**递归地解决各子问题。若子问题足够小，则直接求解。
* **合并（Combine）：**将子问题的结果合并成原问题的解。

###MERGE SROT

合并排序遵循的就是分治策略，合并时使用了哨兵值。合并排序递归时使用一个辅助过程，其伪代码如下：

	MERGE(A,p,q,r)
	1	n1 = q - p + 1
	2	n2 = r - q
	3	let L[1..n1+1] and R[1..n2+1] be new array
	4	for i = 1 to n1
	5		L[i] = A[p + i - 1]
	6	for j = 1 to n2
	7		R[j] = A[q + 1]
	8	L[n1 + 1] = $\infty$
	9	R[n2 + 1] = $\infty$
	10	i = 1
	11	j = 1
	12	for k = p to r
	13		if L[i] <= R[j]
	14			A[k] = L[i]
	15			i = i + 1
	16		else A[k] = R[j]
	17			j = j + 1

上面辅助过程的时间复杂度为$$\Theta(n)$$，因为其主要循环判断两个子数组的最小值的大小并将其放入结果数组中。  

	MERGE-SORT(A,p,r)
	(1)	if p < r
	(2)		q = $\lfloor$(p + r) / 2$\rfloor$
	(3)		MERGE-SORT(A,p,q)
	(4)		MERGE-SORT(A,q+1,r)
	(5)		MERGE(A,p,q,r)

###Analyzing divide-and-conquer algorithms

分治算法中的递归式基于基本模式中的三个步骤（分解、解决、合并）。假设$$T(n)$$为一个规模为$$n$$的问题的运行时间。

* 假设问题规模足够小，使得$$n \leqslant c$$（c是一个常量），则得到它的直接解的时间为常量，写作$$\Theta(1)$$。
* 将原问题分解成$$a$$个子问题，每一个的大小是原问题的$$1/b$$。如果分解该问题和合并该问题的时间各为$$D(n)$$和$$C(n)$$，则可以得到递归式：

$$
T(n) = \left\{\begin{array}{l}\Theta(1)\\aT(n/b) + D(n) + C(n)\end{array}\right.
$$

###Analysis of merge sort

还是基于三个基本步骤来分析合并排序的运行时间，当$$n > 1$$时：

* 分解：这一步仅计算出子数组的中间位置，因此$$D(n) = \Theta(1)$$.
* 解决：递归地解决两个规模为$$n/2$$的子问题，时间为$$2T(n/2)$$.
* 合并：在一个含有$$n$$个元素的子数组上，MERGE过程的运行时间为$$\Theta(n)$$，则$$C(n) = \Theta(n)$$。

由上所述，$$D(n) + C(n) = \Theta(1) + \Theta(n) = \Theta(n) = cn$$，于是:

$$
T(n) = \left\{\begin{array}{1}\Theta(1)&n=1\\2T(n/2) + cn&n>1\end{array}\right.
$$

解方程组，可以得到结果为：

$$
T(n) = \left\{\begin{array}{1}\Theta(1)&n=1\\cn\lg{n} + cn = \Theta(n\lg n)&n>1\end{array}\right.
$$

##Exercises 2.3

###2.3-1

Using Figure 2.4 as a model,illustrate the operation of merge sort on the array $$A = \langle3,41,52,26,38,57,9,49\rangle$$.

###2.3-2

Rewrite the MERGE procedure so that it does not use sentinels,instead stopping once either array $$L$$ or $$R$$ has had all its elements copied back to $$A$$ and then copying the remainder of the other array into $$A$$.

	MERGE(A,p,q,r)
	1	n1 = q - p + 1
	2	n2 = r - q
	3	let L[1..n1] and R[1..n2] be new array
	4	for i = 1 to n1
	5		L[i] = A[p + i - 1]
	6	for j = 1 to n2
	7		R[j] = A[q + 1]
	8   i = 1
	9	j = 1
	10	for k = p to r
	11      if i <= n1 and j <= n2
	12	    	if L[i] <= R[j]
	13		    	A[k] = L[i]
	14			    i = i + 1
	15  		else A[k] = R[j]
	16	    		j = j + 1
	17      else if i > n1 and j < n2
	18          A[k] = R[j]
	19          j = j + 1
	20      else if i < n1 and j > n2
	21          A[k] = L[i]
	22          i = i + 1

###2.3-3

Use mathematical induction to show that when $$n$$ is an exact power of 2,the solution of the recurrence

$$
T(n) = \left\{\begin{array}{1}2&if \ n = 2\\2T(n/2) + n & if \ n=2^k,for \ k > 1\end{array}\right.
$$

is $$T(n) = n\lg n.$$  
解答：当$$k = 1$$时，有

$$
T(2)=2=2\cdot\lg2
$$

假设$$n=2^k(k>1)$$时，有

$$
T(2^k) = 2^k\lg{2^k}
$$

又根据方程组，当$$n=2^{k+1}(k>1)$$时，有

$$
T(2^{k+1}) = 2T(2^k) + 2^{k+1} = 2\cdot2^k\lg{2^k} + 2^{k+1} = 2^{k+1}\lg{2^{k+1}}
$$

由上可知，$$T(n)=n\lg n$$方程成立。

###2.3-4

We can express insertion sort as a recursive procedure as follows.In order to sort $$A[1\dots n]$$,we recursively sort $$A[1\dots n-1]$$ and then insert $$A[n]$$ into the sorted array $$A[1\dots n-1]$$.Write a recurrence for the running time of this recursive version of insertion sort.  

    INSERT(A,n)
    key = A[n]
    i = n - 1
    while i > 1 and A[i] > key
        A[i+1] = A[i]
        i = i - 1
    A[i + 1] = key
    
很明显，INSERT(A,n)的运行时间为$$\Theta(n)$$.

###2.3-5

Referring back to the searching problem (see Exercise 2.1-3),observe that if the sequence $$A$$ is sorted,we can check the midpoint of the sequence against *v* and eliminate half of the sequence from further consideration.The *binary search* algorithm repeats this rocedure,halving the size of the remaining portion of the sequence each time.Write pseudocode, either iterative or recursive, for binary search.Argue that the worst-case running time of binary search is $$\theta(\lg{n})$$  

    BINARY-SEARCH(A,l,h,value)
    if l > h
        return -1
    mid = (l + h)/2
    if A[mid] > value
        return BINARY-SEARCH(A,l,mid-1,value)
    else A[mid] < value
        return BINARY-SEARCH(A,mid+1,h,value)
    else
        return A[mid]
        
###2.3-6

Observe that the **while** loop of lines 5-7 of the INSERTION-SORT procedure in Section 2.1 uses a linear search to scan (backward) through the sorted subarray $$A[1\dots j-1]$$.Can we use a binary search (see Exercise 2.3-5) instead to improve the overall worst-case running time of insertion sort to $$\theta(n\lg{n})$$?  
如果是针对链表结构，这样确实可以达到$$\theta(n\lg{n})$$。但是针对数组的情况下，影响运行时间的是数组移位，所以这种情况下运行时间还是$$\theta(n^2)$$.

###2.3-7 $4\star$$

Describe a $$\theta(n\lg{n})$$-time algorithm that, given a set $$S$$ of $$n$$ integers and another integer $$x$$, determines whether or not there exist two elements in $$S$$ whose sum is exactly $$x$$.  
看到$$\lg{n}$$，很自然地想到二分查找上去了。最终代码如下：

    SEARCH-CHILD(S,x)
    (1) MERGE-SORT(S)
    (2) i = 1
    (3) j = S.length
    (4) while i < j
    (5)     if S[i] + S[j] > x
    (6)         j = j - 1
    (7)     else if S[i] + S[j] < x
    (8)         i = i + 1
    (9)     else
    (10)        return true
    (11)return false

使用二分法查找时先要保证数组是已排序的，于是有了第(1)步，这一步的运行时间是$$\theta(n\lg{n})$$。`while`循环的运行时间为$$\theta(n)$$，所以总时间就是$$\theta(n\lg{n})$$。

##Problems

###2.1 *Insertion sort on small arrays in merge sort*

Although merge sort runs in $$\theta(n\lg{n})$$ worst-case time and insertion sort runs in $$\theta(n^2)$$ worst-case time,the constant factors in insertion sort can make it faster in practice for small problem sizes on many machines.Thus,it makes sense to ***coarsen*** the leaves of the recursion by using insertion sort within merge sort when subproblems become sufficiently small.Consider a modification to merge sort in which $$n/k$$ sublists of length $$k$$ are sorted using insertion sort and then merged using the standard merging mechanism,where $$k$$ is a value to be determined.

***a.*** Show that insertion sort can sort the $$n/k$$ sublists,each of length $$k$$,in $$\Theta(nk)$$ worst-case time.  
这个很简单，就是$$n/k \cdot \Theta(k^2) = \Theta(nk)$$。   
***b.*** Show how to merge the sublists in $$\Theta(n\lg(n/k))$$ worst-case time.  
合并这些子列表的的总时间就是完全使用合并排序时排序整个列表的总时间减去排序$$n/k$$个子列表的总时间，也就是

$$
T = \Theta(n\lg{n}) - n/k\cdot\Theta(k\lg{k}) = \Theta(n\lg{n}) - \Theta(n\lg{k}) = \Theta(n\lg{n/k})
$$
  
***c.*** Given that the modified algorithm runs in $$\Theta(nk + n\lg(n/k))$$ worst-case time,what is the largest value of $$k$$ as a function of $$n$$ for which the modified algorithm has the same running time as standard merge sort,in terms of $$\Theta$$-notation?  

$$
\begin{align*}&\because \Theta(nk+n\lg(n/k)) = \Theta(nk + n\lg{n} - n\lg{k}) = \Theta(nk + n\lg{n})\\
&\therefore 当k = \lg{n}时，有\Theta(nk + n\lg{n/k}) = \Theta(n\lg{n})\\\end{align*}
$$

***d.*** How should we choose $$k$$ in practice?  
由c的答案可知，我们平时可以将$k$定位在$\lg{n}$附近。

###2-2 ***Correctness of bubblesort***

Bubblesort is a pupular,but inefficient,sorting algoritm.It works by repeatedly swapping adjacent elements that are out of order.

    BUBBLESORT(A)
    1   for i = 1 to A.length - 1
    2       for j = A.length downto i + 1
    3           if A[j] < A[j - 1]
    4               exchange A[j] with A[j-1]
    
***a.*** Let $$A'$$ denote the output of BUBBLESORT(A).To prove that BUBBLESORT is correct,we need to prove that it terminates and that 

$$
A'[1] \le A'[2] \le \cdots \le A'[n]
$$

where $$n=A.length$$.In order to show that BUBBLESORT actually sorts,what else do we need to prove?  
算法的几个准则：

* 输入（Input）：必须有0个或多个输入值，这些值取自某个特定对象集合。
* 输出（Output）：有1个或者多个输出值，这些输出值是与输入值有着特定关系的值。
* 有穷性（Finiteness）：一个算法必须总是（对任意合法的输入值）在执行有穷步之后结束，且每一步都可在有穷时间内完成。
* 确定性（Definiteness）：算法中的每一条指令必须有确切的含义，不会产生二义性。在任何条件下，算法只能有唯一的一条执行路径，即每条指令的含义都必须明确，没有二义性。
* 可行性（Effectiveness）：即算法中描述的操作都是可以通过已经实现的基本运算执行有限次来实现的。

对比这几个准则，可以知道，要想证明BUBBLESORT是正确的，还得证明输出值中的元素都来自输入值，以及证明对于同样的输入，输出都一样。  
**The next two parts will prove inequality**  
***b.*** State precisely a loop invariant for the for loop in lines 2-4,and prove that this loop invariant holds.Your proof should use the structure of the loop invariant proof presented in this chapter.  
初始化：$$j=A.length$$，此时子数组中只有一个值，所以它是在正确的位置上的。即循环不变式的初始条件是成立的。  
保持：从最外层的for循环看，首先比较$$A[n]$$和$$A[n-1]$$，如果前者小于后者，则交换两者的值。然后再比较$$A[n-1]$$和$$A[n-2]$$，依比较情况决定是否交换值。如此循环，最终将整个子数组的最小值移动到最左端。所以这一步也是成立的。  
终止：当$$j=i$$时，循环不变式终止，此时最小值已移到最左边，所以这一步也是成立的。  
***c.*** Using the termination condition of the loop invariant proved in part(b),state a loop invariant for the for loop in lines 1-4 that will allow you prove inequality.Your proof should use the structure of the loop invariant proof presented in this chapter.  
初始化：当$$i=1$$时，此时子数组中只有一个值，所以它是排好序的。即循环不变式在第一轮迭代前是成立的。  
保持：由***b***的结论可知，每轮迭代后，子数组$$\langle A[i],\dots,A[n]\rangle$$中，$$A[i]$$必定是最小的。这也意味着，每轮迭代后，子数组$$\langle A[1],\dots,A[i]\rangle$$是有序的，所以此过程也成立。  
终止：当$$i=n$$时，循环终止，此时子数组$$\langle A[1],\dots,A[n]\rangle$$已经是有序的，因此终止条件也成立。  
***d.*** What is the worst-case running time of bubblesort?How does it compare to the running time of insertion sort?  
运行时间由内层循环决定，此处最坏情况下运行时间为$$\Theta(n^2)$$。原因是

$$
\sum_{j=2}^nj = \frac{n(n + 1)}{2} - 1
$$

###2-3 ***Correctness of Horner's rule***

The following code fragment implements Horner's rule for evaluating a polynomial

$$
\begin{equation}\begin{split}P(x)&=\sum_{k=0}^{n}a_kx^k\\&=a_0+x(a_1 + x(a_2+\cdots + x(a_{n-1} + xa_n)\cdots))\end{split}\end{equation}
$$

given the coefficients $$a_0,a_1,\dots,a_n$$ and a value for $$x$$:

    1   y=0
    2   for i = n downto 0
    3       y = a_i + x * y
    
***a.*** In terms of $$\Theta$$-notation,what is the running time of this code fragment for Horner's rule?  
由代码可知，每次迭代前$$y$$的值都是确定的，所以Horner's rule的运行时间是$$\Theta(n)$$。  
***b.*** Write pseudocode to implement the native polynomial-evaluation algorithm that computes each term of the polynomial from scratch.What is the running time of this algorithm?How does it compare to Horner's rule?  
Horner's rule的朴素多项式为：

$$
P(x)=a_0+a_1x+\cdots+a_nx^n
$$

    NATIVE-POLYNOMIAL
    1   y = 0
    2   for i = 0 to n
    3       z = 1
    4       for j = 1 to i
    5           z = z * x
    6       y = y + a_i * x
    
由伪代码可知，Horner's rule的朴素多项式求值的运行时间是$$\Theta(n^2)$$  
***c.*** Consider the following loop invariant:  
At the start of each iteration of the for loop of lines 2-3,

$$
y=\sum_{k=0}^{n-(i+1)}a_{k+i+1}x^k.
$$

Interpret a summation with no terms as equaling 0.Following the structure of the loop invariant proof presented in this chapter,use this loop invariant to show that,at termination,$$y=\sum_{k=0}^na_kx^k.$$  
起始：第一次迭代前$$y=\sum_{k=0}^{-1}a_{k+n+1}x^k=0$$，根据题意，起始条件成立。  
保持：当$$i=j$$时，有

$$
\begin{equation}\begin{split}y_{j}&=\sum_{k=0}^{n-(j+1)}a_{k+j+1}x^k\\
&=a_{j+1}+a_{j+2}x+\cdots+a_nx^{n-(j+1)}\end{split}\end{equation}
$$

当$$i=j-1$$时，有

$$
\begin{equation}\begin{split}y_{j-1}&=\sum_{k=0}^{n-j}a_{k+j}x^k\\
&=a_{j}+a_{j+1}x+\cdots+a_nx^{n-j}\\&=a_{j}+x\cdot y_{j}\end{split}\end{equation}
$$

以上可以证明循环不变式的主体成立。  
终止：终止时，有$$j=0$$，有$$j_{-1}=\sum_{k=0}^na_kx^k$$，所以终止条件也成立。  
***d.*** Conclude by arguing that the given code fragment correctly evaluates a polynomial characterized by the coefficients $$a_0,a_1,\cdots,a_n.$$  
对于$$i=n$$来说，第一轮迭代后系数$$a_n$$刻划的项的级数为0，第二轮迭代后系数$$a_n$$刻划的项级数为1，直到第$$n+1$$轮后，系数$$a_n$$刻划的项级数为n。  
同理，对于$$i=k$$来说，第$$n-k+1$$轮迭代后系数$$a_k$$刻划的项的级数为0，第$$n-k+2$$轮迭代后系数$$a_n$$刻划的项级数为1，直到第$$n+1$$轮后，系数$$a_k$$刻划的项级数为k。  

###2-4 ***Inversions***

Let $$A[1..n]$$ be an array of $$n$$ distinct numbers.If $$i < j$$ and $$A[i] > A[j]$$,then the pair $$(i,j)$$ is called an ***inversion*** of $$A$$.  
***a.*** List the five inversions of array $$\langle2,3,8,6,1\rangle.$$  
$$(6,1),(8,1),(3,1),(2,1),(8,6)$$  
***b.*** What array with elements from the set $$\{1,2,\dots,n\}$$ has the most inversions? How many does it have?  
倒序的数组$$\langle n, n-1,\dots,1\rangle$$具有最多的倒序对，其数量是

$$
\sum_{j=2}^nj=\frac{n(n+1)}{2}-1
$$

***c.*** What is the relationship between the running time of insertion sort and the number of inversions in the input array? Justify your answer.  
插入排序的最坏情况，就是要排序的数组具有最多的倒序对，最终决定其运行时间为$$\Theta(n^2)$$。  
***d.*** Given an algorithm that determines the number of inversions in any permutation on $$n$$ elements in $$\Theta(n\lg n)$$ worst-case time.(Hint:Modifiy merge sort).  

    INVERSIONS(A,p,q,r)
    1   n1 = q - p + 1
    2   n2 = r - q
    3   let L[1..n_1+1] and R[1..n_2+1] be new arrays
    4   for i = 1 to n_1
    5       L[i] = A[p + i - 1]
    6   for j = 1 to n_2
    7       R[j] = A[q + j]
    8   L[n_1 + 1] = \infty
    9   R[n_2 + 1] = \infty
    10  i = 1
    11  j = 1
    12  s = 0
    13  for k = p to r
    14      if L[i] <= R[j]
    15          i = i + 1
    16      else j = j + 1
    17          s = s + n_1 - i
    18  return s
    
    COUNT-INVERSIONS(A,p,r)
    1   count = 0
    2   if p < r
    3       q = (p + r) / 2
    4       count = count + COUNT-INVERSIONS(A,p,q)
    5       count = count + COUNT-INVERSIONS(A,q,r)
    6       count = count + INVERSIONS(A,p,q,r)
    7   return count

> Written with [StackEdit](http://benweet.github.io/stackedit/).aligned