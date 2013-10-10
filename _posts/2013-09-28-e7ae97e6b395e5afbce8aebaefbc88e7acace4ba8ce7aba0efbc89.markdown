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

## Insertion Sort





**Input**:A sequence of _n_ numbers ⟨a1,a2,⋯,an⟩   

**Output**:A permutation (reordering) ⟨a,1,a,2,⋯,a,n⟩   

**INSERTION SORT(A)**  




    
    <code>for j = 2 to A.length                                   (1)
        key = A[j]                                          (2)
        //Insert A[j] into the sorted sequence A[1..j-1].   (3)
        i = j - 1                                           (4)
        while i > 0 and A[i] > key                          (5)
            A[i + 1] = A[i]                                 (6)
            i = i - 1                                       (7)
        A[i + 1] = key                                      (8)
    </code>





## Exercises 2.1





### 2.1-1





Using Figure 2.2 as a model,illustrate the operation of INSERTION-SORT on the array A=⟨31,41,59,26,41,38⟩.   

a.⟨31,41,59,26,41,38⟩   

b.⟨31,41,59,26,41,38⟩   

c.⟨26,31,41,59,41,38⟩   

d.⟨26,31,41,41,59,38⟩   

e.⟨26,31,38,41,41,59⟩  





### 2.1-2





Rewrite the INSERTION-SORT procedure to sort into nonincreasing instead of nondecreasing order.  




    
    <code>for j = 2 to A.length
        key = A[j]
        i = j - 1
        while i > 0 and A[i] < key
            A[i + 1] = A[i]
            i = i - 1
        A[i + 1] = key
    </code>





### 2.1-3





Consider the _searching problem:_   

**Input:** A sequence of n numbers A=⟨a1,a2,⋯,an⟩ and a value v.   

**Output:** An index i such that v=A[i] or the special value **NIL** if v does not appear in A.   

Write pseudocode for _linear search_,which scans through the sequence,looking for v.Using a loop invariant,prove that your algorighm is correct.Make sure that your loop invariant fulfills the three necessary properties.  




    
    <code>i = NIL
    for j = 1 to A.length
        if A[j] = v
            i = j
            break
    </code>





### 2.1-4





Consider the problem of adding two _n_-bit binary integers,stored in two _n_-element arrays _A_ and _B_.The sum of the two integers should be stored in binary form in an (n+1)-element array _C_.State the problem formally and write pseudocode for adding the two integers.  




    
    <code>r = 0
    for j = n to 1//这里的顺序是反向的，即数组的最后一位正好是二进制数的第一位
        if A[j] = B[j] then
            C[j + 1] = r
            r = A[j]
        else
            C[j + 1] = (r + 1) % 2
    C[1] = r
    </code>





有一种更普遍的算法：




    
    <code>C = [0,...,0]
    for j = 1 to n
        sum = A[j] + B[j] + C[j]
        C[j] = sum % 2
        C[j + 1] = sum / 2
    </code>





这个算法的证明如下：   

根据题意，如果不考虑进位，则有

⟨c1,⋯,cn,0⟩⟺⟨a1+b1,⋯,an+bn,0⟩


再考虑进位，则有

⟨(a1+b1)%2,a2+b2+(a1+b1)/2,⋯,an+bn,0⟩


还有一种算法，其实质与上一个算法相同：




    
    <code>c = 0
    for j = 1 to n
        d = ⌊(A[j] + B[j] + c) / 2⌋
        C[j] = A[j] + B[j] + c - 2⋅d
        c = d
    C[n + 1] = c
    </code>





## Analyzing algorithms





假设每一行伪代码的执行时间都是常数。





### INSERTION-SORT





第(1)行：           cost:   c1          times:  n   

第(2)行：           cost:   c2          times:  n−1   

第(3)行：           cost:   c3=0      times:  n−1   

第(4)行：           cost:   c4          times:  n−1   

第(5)行：           cost:   c5          times:  ∑nj=2tj   

第(6)行：           cost:   c6          times:  ∑nj=2(tj−1)   

第(7)行：           cost:   c7          times:  ∑nj=2(tj−1)   

第(8)行：           cost:   c8          times:  n−1  





由上可知，INSERTION-SORT的总时间为：


T(n)=c1n+c2(n−1)+c4(n−1)+c5∑j=2ntj+c6∑j=2n(tj−1)+c7∑j=2n(tj−1)+c8(n−1)


最佳情况下：


T(n)=c1n+c2(n−1)+c4(n−1)+c5(n−1)+c8(n−1)=(c1+c2n+c4n+c5+c8)n−(c2+c4+c5+c8)=an+b


最坏情况下：
由于

∑j=2nj=n(n+1)2−1


并且


∑j=2n(j−1)=n(n−1)2


因此


T(n)=c1n+c2(n−1)+c4(n−1)+c5(n(n+1)2−1)+c6(n(n−1)2)+c7(n(n−1)2)+c8(n−1)=(c5+c6+c72)n2+(c1+c2+c4+c52−c62−c72+c8)n−(c2+c4+c5+c8)=a,n2+b,n+c





## Exercises 2.2





### 2.2-1





Express the function n3/1000−100n2−100n+3 in terms of Θ-notation.


Θ(n3)





### 2.2-2





Consider sorting _n_ numbers stored in array _A_ by first finding the smallest element of _A_ and exchanging it with the element in _A_[1].Then find the second smallest element of _A_,and exchange it with _A_[2].Continue in this manner for the first _n - 1_ elements of _A_.Write pseudocode for this algorithm,which is known as **selection sort**.What loop invariant does this algorithm maintian?Why does it need to run for only the first _n - 1_ elements,rather than for all _n_ elements?Give the best-case and worst-case running times of selection sort in Θ-notation.  




    
    <code>SELECTION-SORT(A):
    for j = 1 to A.length - 1
        k = j
        i = j + 1
        while i < A.length + 1 and A[i] < A[k]
            k = i
            i = i + 1
        t = A[k]
        A[k] = A[j]
        A[j] = t
    </code>





根据以上伪代码：


T(n)=c1n+c2(n−1)+c3(n−1)+c4∑j=2ntj+c5∑j=2n(tj−1)+c6∑j=2n(tj−1)+c7(n−1)+c8(n−1)+c9(n−1)=c1n+c2(n−1)+c3(n−1)+c4(n(n+1)2−1)+c5(n(n−1)2)+c6(n(n−1)2)+c7(n−1)+c8(n−1)+c9(n−1)=(c4+c5+c62)n2+(c1+c2+c3+c42−c52−c62+c7+c8+c9)n−(c2+c3+c4+c7+c8+c9)=an2+bn+c=Θ(n2)





### 2.2-3





Consider linear search again (see Exercises 2.1-3).How many elements of the input sequence need to be checked on the average,assuming that the element being searched for is equally likely to be any element int the array?How about in the worst case？What are the average-case and worst-case running times of linear search in Θ-notation?Justify your answers.   

Best Case: 1   

Worst Case: n   

Avarge Case: n+12  





### 2.2-4





How can we modify almost any algorithm to have a good best-case running time?   

要写出一个好的算法，应该把问题尽量拆分，然后根据拆分的问题组织算法。比如在排序算法中，我们可以先将其预先排成局部有序，再整体排序。





## Designing algorithms





### The divide-and-conquer approach





“分治策略”：将原问题划分成_n_个规模较小而结构与原问题相似的子问题；递归地解决这些子问题，然后再合并其结果，最终得到原问题的解。   

分治模式在每一层递归上都有3个步骤：







  * **分解（Divide）：**将原问题分解成一系列子问题；


  * **解决（Conquer）：**递归地解决各子问题。若子问题足够小，则直接求解。


  * **合并（Combine）：**将子问题的结果合并成原问题的解。





### MERGE SROT





合并排序遵循的就是分治策略，合并时使用了哨兵值。合并排序递归时使用一个辅助过程，其伪代码如下：




    
    <code>MERGE(A,p,q,r)
    1   n1 = q - p + 1
    2   n2 = r - q
    3   let L[1..n1+1] and R[1..n2+1] be new array
    4   for i = 1 to n1
    5       L[i] = A[p + i - 1]
    6   for j = 1 to n2
    7       R[j] = A[q + 1]
    8   L[n1 + 1] = $\infty$
    9   R[n2 + 1] = $\infty$
    10  i = 1
    11  j = 1
    12  for k = p to r
    13      if L[i] <= R[j]
    14          A[k] = L[i]
    15          i = i + 1
    16      else A[k] = R[j]
    17          j = j + 1
    </code>





上面辅助过程的时间复杂度为Θ(n)，因为其主要循环判断两个子数组的最小值的大小并将其放入结果数组中。  




    
    <code>MERGE-SORT(A,p,r)
    (1) if p < r
    (2)     q = $\lfloor$(p + r) / 2$\rfloor$
    (3)     MERGE-SORT(A,p,q)
    (4)     MERGE-SORT(A,q+1,r)
    (5)     MERGE(A,p,q,r)
    </code>





### Analyzing divide-and-conquer algorithms





分治算法中的递归式基于基本模式中的三个步骤（分解、解决、合并）。假设T(n)为一个规模为n的问题的运行时间。







  * 假设问题规模足够小，使得n⩽c（c是一个常量），则得到它的直接解的时间为常量，写作Θ(1)。


  * 将原问题分解成a个子问题，每一个的大小是原问题的1/b。如果分解该问题和合并该问题的时间各为D(n)和C(n)，则可以得到递归式： 

T(n)={Θ(1)aT(n/b)+D(n)+C(n)






### Analysis of merge sort





还是基于三个基本步骤来分析合并排序的运行时间，当n>1时：







  * 分解：这一步仅计算出子数组的中间位置，因此D(n)=Θ(1).


  * 解决：递归地解决两个规模为n/2的子问题，时间为2T(n/2).


  * 合并：在一个含有n个元素的子数组上，MERGE过程的运行时间为Θ(n)，则C(n)=Θ(n)。





由上所述，D(n)+C(n)=Θ(1)+Θ(n)=Θ(n)=cn，于是:


T(n)={Θ(1)2T(n/2)+cnn=1n>1


解方程组，可以得到结果为：


T(n)={Θ(1)cnlgn+cn=Θ(nlgn)n=1n>1





## Exercises 2.3





### 2.3-1





Using Figure 2.4 as a model,illustrate the operation of merge sort on the array A=⟨3,41,52,26,38,57,9,49⟩.





### 2.3-2





Rewrite the MERGE procedure so that it does not use sentinels,instead stopping once either array L or R has had all its elements copied back to A and then copying the remainder of the other array into A.




    
    <code>MERGE(A,p,q,r)
    1   n1 = q - p + 1
    2   n2 = r - q
    3   let L[1..n1] and R[1..n2] be new array
    4   for i = 1 to n1
    5       L[i] = A[p + i - 1]
    6   for j = 1 to n2
    7       R[j] = A[q + 1]
    8   i = 1
    9   j = 1
    10  for k = p to r
    11      if i <= n1 and j <= n2
    12          if L[i] <= R[j]
    13              A[k] = L[i]
    14              i = i + 1
    15          else A[k] = R[j]
    16              j = j + 1
    17      else if i > n1 and j < n2
    18          A[k] = R[j]
    19          j = j + 1
    20      else if i < n1 and j > n2
    21          A[k] = L[i]
    22          i = i + 1
    </code>





### 2.3-3





Use mathematical induction to show that when n is an exact power of 2,the solution of the recurrence

T(n)={22T(n/2)+nif n=2if n=2k,for k>1


is T(n)=nlgn.   

解答：当k=1时，有

T(2)=2=2⋅lg2


假设n=2k(k>1)时，有

T(2k)=2klg2k


又根据方程组，当n=2k+1(k>1)时，有

T(2k+1)=2T(2k)+2k+1=2⋅2klg2k+2k+1=2k+1lg2k+1


由上可知，T(n)=nlgn方程成立。





### 2.3-4





We can express insertion sort as a recursive procedure as follows.In order to sort A[1…n],we recursively sort A[1…n−1] and then insert A[n] into the sorted array A[1…n−1].Write a recurrence for the running time of this recursive version of insertion sort.  




    
    <code>INSERT(A,n)
    key = A[n]
    i = n - 1
    while i > 1 and A[i] > key
        A[i+1] = A[i]
        i = i - 1
    A[i + 1] = key
    </code>





很明显，INSERT(A,n)的运行时间为Θ(n).





### 2.3-5





Referring back to the searching problem (see Exercise 2.1-3),observe that if the sequence A is sorted,we can check the midpoint of the sequence against _v_ and eliminate half of the sequence from further consideration.The _binary search_ algorithm repeats this rocedure,halving the size of the remaining portion of the sequence each time.Write pseudocode, either iterative or recursive, for binary search.Argue that the worst-case running time of binary search is θ(lgn)  




    
    <code>BINARY-SEARCH(A,l,h,value)
    if l > h
        return -1
    mid = (l + h)/2
    if A[mid] > value
        return BINARY-SEARCH(A,l,mid-1,value)
    else A[mid] < value
        return BINARY-SEARCH(A,mid+1,h,value)
    else
        return A[mid]
    </code>





### 2.3-6





Observe that the **while** loop of lines 5-7 of the INSERTION-SORT procedure in Section 2.1 uses a linear search to scan (backward) through the sorted subarray A[1…j−1].Can we use a binary search (see Exercise 2.3-5) instead to improve the overall worst-case running time of insertion sort to θ(nlgn)?   

如果是针对链表结构，这样确实可以达到θ(nlgn)。但是针对数组的情况下，影响运行时间的是数组移位，所以这种情况下运行时间还是θ(n2).





### 2.3-7 ⋆





Describe a θ(nlgn)-time algorithm that, given a set S of n integers and another integer x, determines whether or not there exist two elements in S whose sum is exactly x.   

看到lgn，很自然地想到二分查找上去了。最终代码如下：




    
    <code>SEARCH-CHILD(S,x)
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
    </code>





使用二分法查找时先要保证数组是已排序的，于是有了第(1)步，这一步的运行时间是θ(nlgn)。`while`循环的运行时间为θ(n)，所以总时间就是θ(nlgn)。





## Problems





### 2.1 _Insertion sort on small arrays in merge sort_





Although merge sort runs in θ(nlgn) worst-case time and insertion sort runs in θ(n2) worst-case time,the constant factors in insertion sort can make it faster in practice for small problem sizes on many machines.Thus,it makes sense to **_coarsen_** the leaves of the recursion by using insertion sort within merge sort when subproblems become sufficiently small.Consider a modification to merge sort in which n/k sublists of length k are sorted using insertion sort and then merged using the standard merging mechanism,where k is a value to be determined.





_**a.**_ Show that insertion sort can sort the n/k sublists,each of length k,in Θ(nk) worst-case time.   

这个很简单，就是n/k⋅Θ(k2)=Θ(nk)。   

**_b._** Show how to merge the sublists in Θ(nlg(n/k)) worst-case time.   

合并这些子列表的的总时间就是完全使用合并排序时排序整个列表的总时间减去排序n/k个子列表的总时间，也就是

T=Θ(nlgn)−n/k⋅Θ(klgk)=Θ(nlgn)−Θ(nlgk)=Θ(nlgn/k)

  

**_c._** Given that the modified algorithm runs in Θ(nk+nlg(n/k)) worst-case time,what is the largest value of k as a function of n for which the modified algorithm has the same running time as standard merge sort,in terms of Θ-notation?   



∵Θ(nk+nlg(n/k))=Θ(nk+nlgn−nlgk)=Θ(nk+nlgn)∴当k=lgn时，有Θ(nk+nlgn/k)=Θ(nlgn)


**_d._** How should we choose k in practice?   

由c的答案可知，我们平时可以将k定位在lgn附近。





### 2-2 **_Correctness of bubblesort_**





Bubblesort is a pupular,but inefficient,sorting algoritm.It works by repeatedly swapping adjacent elements that are out of order.




    
    <code>BUBBLESORT(A)
    1   for i = 1 to A.length - 1
    2       for j = A.length downto i + 1
    3           if A[j] < A[j - 1]
    4               exchange A[j] with A[j-1]
    </code>





_**a.**_ Let A′ denote the output of BUBBLESORT(A).To prove that BUBBLESORT is correct,we need to prove that it terminates and that 

A′[1]≤A′[2]≤⋯≤A′[n]


where n=A.length.In order to show that BUBBLESORT actually sorts,what else do we need to prove?   

算法的几个准则：







  * 输入（Input）：必须有0个或多个输入值，这些值取自某个特定对象集合。


  * 输出（Output）：有1个或者多个输出值，这些输出值是与输入值有着特定关系的值。


  * 有穷性（Finiteness）：一个算法必须总是（对任意合法的输入值）在执行有穷步之后结束，且每一步都可在有穷时间内完成。


  * 确定性（Definiteness）：算法中的每一条指令必须有确切的含义，不会产生二义性。在任何条件下，算法只能有唯一的一条执行路径，即每条指令的含义都必须明确，没有二义性。


  * 可行性（Effectiveness）：即算法中描述的操作都是可以通过已经实现的基本运算执行有限次来实现的。





对比这几个准则，可以知道，要想证明BUBBLESORT是正确的，还得证明输出值中的元素都来自输入值，以及证明对于同样的输入，输出都一样。   

**The next two parts will prove inequality**   

**_b._** State precisely a loop invariant for the for loop in lines 2-4,and prove that this loop invariant holds.Your proof should use the structure of the loop invariant proof presented in this chapter.   

初始化：j=A.length，此时子数组中只有一个值，所以它是在正确的位置上的。即循环不变式的初始条件是成立的。   

保持：从最外层的for循环看，首先比较A[n]和A[n−1]，如果前者小于后者，则交换两者的值。然后再比较A[n−1]和A[n−2]，依比较情况决定是否交换值。如此循环，最终将整个子数组的最小值移动到最左端。所以这一步也是成立的。   

终止：当j=i时，循环不变式终止，此时最小值已移到最左边，所以这一步也是成立的。   

**_c._** Using the termination condition of the loop invariant proved in part(b),state a loop invariant for the for loop in lines 1-4 that will allow you prove inequality.Your proof should use the structure of the loop invariant proof presented in this chapter.   

初始化：当i=1时，此时子数组中只有一个值，所以它是排好序的。即循环不变式在第一轮迭代前是成立的。   

保持：由**_b_**的结论可知，每轮迭代后，子数组⟨A[i],…,A[n]⟩中，A[i]必定是最小的。这也意味着，每轮迭代后，子数组⟨A[1],…,A[i]⟩是有序的，所以此过程也成立。   

终止：当i=n时，循环终止，此时子数组⟨A[1],…,A[n]⟩已经是有序的，因此终止条件也成立。   

**_d._** What is the worst-case running time of bubblesort?How does it compare to the running time of insertion sort?   

运行时间由内层循环决定，此处最坏情况下运行时间为Θ(n2)。原因是

∑j=2nj=n(n+1)2−1





### 2-3 **_Correctness of Horner's rule_**





The following code fragment implements Horner's rule for evaluating a polynomial


P(x)=∑k=0nakxk=a0+x(a1+x(a2+⋯+x(an−1+xan)⋯))


given the coefficients a0,a1,…,an and a value for x:




    
    <code>1   y=0
    2   for i = n downto 0
    3       y = a_i + x * y
    </code>





_**a.**_ In terms of Θ-notation,what is the running time of this code fragment for Horner's rule?   

由代码可知，每次迭代前y的值都是确定的，所以Horner's rule的运行时间是Θ(n)。   

**_b._** Write pseudocode to implement the native polynomial-evaluation algorithm that computes each term of the polynomial from scratch.What is the running time of this algorithm?How does it compare to Horner's rule?   

Horner's rule的朴素多项式为：

P(x)=a0+a1x+⋯+anxn




    
    <code>NATIVE-POLYNOMIAL
    1   y = 0
    2   for i = 0 to n
    3       z = 1
    4       for j = 1 to i
    5           z = z * x
    6       y = y + a_i * x
    </code>





由伪代码可知，Horner's rule的朴素多项式求值的运行时间是Θ(n2)   

**_c._** Consider the following loop invariant:   

At the start of each iteration of the for loop of lines 2-3,

y=∑k=0n−(i+1)ak+i+1xk.


Interpret a summation with no terms as equaling 0.Following the structure of the loop invariant proof presented in this chapter,use this loop invariant to show that,at termination,y=∑nk=0akxk.   

起始：第一次迭代前y=∑−1k=0ak+n+1xk=0，根据题意，起始条件成立。   

保持：当i=j时，有

yj=∑k=0n−(j+1)ak+j+1xk=aj+1+aj+2x+⋯+anxn−(j+1)


当i=j−1时，有

yj−1=∑k=0n−jak+jxk=aj+aj+1x+⋯+anxn−j=aj+x⋅yj


以上可以证明循环不变式的主体成立。   

终止：终止时，有j=0，有j−1=∑nk=0akxk，所以终止条件也成立。   

**_d._** Conclude by arguing that the given code fragment correctly evaluates a polynomial characterized by the coefficients a0,a1,⋯,an.   

对于i=n来说，第一轮迭代后系数an刻划的项的级数为0，第二轮迭代后系数an刻划的项级数为1，直到第n+1轮后，系数an刻划的项级数为n。   

同理，对于i=k来说，第n−k+1轮迭代后系数ak刻划的项的级数为0，第n−k+2轮迭代后系数an刻划的项级数为1，直到第n+1轮后，系数ak刻划的项级数为k。  





### 2-4 **_Inversions_**





Let A[1..n] be an array of n distinct numbers.If i<j and A[i]>A[j],then the pair (i,j) is called an **_inversion_** of A.   

**_a._** List the five inversions of array ⟨2,3,8,6,1⟩.   

(6,1),(8,1),(3,1),(2,1),(8,6)   

**_b._** What array with elements from the set {1,2,…,n} has the most inversions? How many does it have?   

倒序的数组⟨n,n−1,…,1⟩具有最多的倒序对，其数量是

∑j=2nj=n(n+1)2−1


**_c._** What is the relationship between the running time of insertion sort and the number of inversions in the input array? Justify your answer.   

插入排序的最坏情况，就是要排序的数组具有最多的倒序对，最终决定其运行时间为Θ(n2)。   

**_d._** Given an algorithm that determines the number of inversions in any permutation on n elements in Θ(nlgn) worst-case time.(Hint:Modifiy merge sort).  




    
    <code>INVERSIONS(A,p,q,r)
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
    </code>





> 
  
> 
> Written with [StackEdit](http://benweet.github.io/stackedit/).aligned
> 
> 




