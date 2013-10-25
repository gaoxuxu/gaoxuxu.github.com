---
author: gavin
comments: true
date: 2013-10-25 18:21:25
layout: post
title: 算法导论第四章——主方法以及Problems
categories:
- 算法
---

##The master method for solving recurrences

###***Theorem 4.1(Master method)***

Let $a\ge 1$ and $b>1$ be constants,let $f(n)$ be a function,and let $T(n)$ be defined on the nonnegative integers by the recurrence

$T(n)=aT(n/b)+f(n)$

where we interpret $n/b$ to mean either $\lfloor n/b\rfloor$ or $\lceil n/b\rceil$. Then $T(n)$ has the following asymptotic bounds:

1. If $f(n)=O(n^{\log_ba-\epsilon})$ for some constant $\epsilon>0$,then $T(n)=\Theta(n^{\log_ba})$.
2. If $f(n)=\Theta(n^{\log_ba})$,then $T(n)=\Theta(n^{\log_ba}\lg n)$.
3. If $f(n)=\Omega(n^{\log_ba+\epsilon})$ for some constant $\epsilon>0$, and if $af(n/b)\le cf(n)$ for some constant $c<1$ and all sufficiently large $n$,then $T(n)=\Theta(f(n))$.

##Exercises

###4.5-1

Use the master method to give tight asymptotic bounds for the following recurrences.

a. $T(n)=2T(n/4)+1=\Theta(\sqrt n)$.

b.$T(n)=2T(n/4)+\sqrt n=\Theta(\sqrt n\lg n)$

c.$T(n)=2T(n/4)+n=\Theta(n)$

d.$T(n)=2T(n/4)+n^2=\Theta(n^2)$

###4.5-2

Professor Caesar wishes to develop a matrix-multiplication algorithm that is asymptotically faster than Strassen's algorithm.His algorithm will use the divide-and-conquer method,dividing each matrix into pieces of size $n\times n$,and the divide and combine steps together will take $\Theta(n^2)$ time.He needs to determine how many subproblems his algorithm has to create in order to beat Strassen's algorithm.If his algorithm creates $a$ subproblems,then the recurrence for the running time $T(n)$ becomes $T(n)=aT(n/4)+\Theta(n^2)$.What is the largest integer value of $a$ for which Professor Caesar's algorithm would be asymptotically faster than Strassen's algorithm?  
Strassen's algorithm的运行时间为$T_1(n)=\Theta(n^{\lg7})$，要使凯撒教授的算法比Strassen算法快，只需$a=48(7^2-1)$即可。

###4.5-3

Use the master method to show that the solution to the binary-search recurrence $T(n)=T(n/2)+\Theta(1)$ is $T(n)=\Theta(\lg n)$.  
$a=1,b=2,n^{\log_ba}=n^0,f(n)=1=n^0$，所以$T(n)=\Theta(n^{\log_21}\lg n)=\Theta(\lg n)$

###4.5-4

Can the master method be applied to the recurrence $T(n)=4T(n/2)+n^2\lg n$?Why or why not?Give an asymptotic upper bound for this currence.  
不适用，因为针对第三种情况，$af(n/b)$不是多项式大于$f(n)$的。

$$
\begin{equation}\begin{split}\
T(n)&=\sum_{i=0}^{\lg n}\left(\frac{n}{2^i}\right)^2\lg \frac{n}{2^i}\\
&=\sum_{i=0}^{\lg n}\left(\frac{n}{2^i}\right)^2\lg n+\sum_{i=1}^{\lg n}\left(\frac{n}{2^i}\right)^2i\\
&=\frac{(\lg n+1)n^2\lg n}{2^{\lg n+1}-1}+\frac{\frac{1}{2}\lg n(n^2+n^2\lg n)}{2^{\lg n+1}-2}\\
&=O(n^2\lg^2n)\
\end{split}\end{equation}
$$

###4.5-5  $\star$

Consider the regularity condition $af(n/b)\le cf(n)$ for some constant $c<1$,which is part of case 3 of the master theorem.Give an example of constant $a\ge 1$ and $b>1$ and a function $f(n)$ that satisfies all the conditions in case 3 of the master theorem except the regularity condition.  

##Problems

###4-1 ***Recurrence examples***

Give asymptotic upper and lower bounds for $T(n)$ in each of the following recurrences. Assume that $T(n)$ is constant for $n\le 2$. Make your bounds as tight as possible,and justify your answers.

**a.** $T(n)=2T(n/2)+n^4=\Theta(n^4)$

**b.** $T(n)=T(7n/10)+n=\Theta(n)$

**c.** $T(n)=16T(n/4)+n^2=\Theta(n^2\lg n)$

**d.** $T(n)=7T(n/3)+n^2=\Theta(n^2)$

**e.** $T(n)=7T(n/2)+n^2=\Theta(n^{\lg 7})$

**f.** $T(n)=2T(n/4)+\sqrt n=\Theta(\sqrt n\lg n)$

**g.** $T(n)=T(n-2)+n^2=\Theta(n^4)$

###4.2 ***Parameter-passing costs***

Throughout this book,we assume that parameter passing during procedure calls takes constant time,even if an N-element array is being passed. This assumption is valid in most systems because a pointer to the array is passed,not the array itself. This problem examines the implications of three parameter-passing strategies:

1. An array is passed by pointer. Time=$\Theta(1)$.
2. An array is passed by copying. Time=$\Theta(N)$,where $N$ is the size of array.
3. An array is passed by coping only the subrange that might be accessed by the called procedure. Time=$\Theta(q-p+1)$ if the subarray $A[p..q]$ is passed.  

**a.** Consider the recursive binary search algorithm for finding a number in a sorted array (see Exercises 2.3-5). Give recurrences for the worst-case running times of binary search when arrays are passed using each of the three method above, and give good upper bounds on the solutions of the recurrences. Let $N$ be the size of the original problem and $n$ be the size of a subproblem.

1. $T(n)=O(\lg n)$
2. $T(n)=O(N\lg n)$
3. $T(n)=O(n)$

**b.** Redo part(a) for the MERGE-SORT algorithm from Section 2.3.1.

1. $T(n)=O(n\lg n)$
2. $T(n)=O(nN)$
3. $T(n)=O(n\lg n)$

###4.3 ***More recurrence example***

Give asymptotic upper and lower bounds for $T(n)$ in each of the following recurrences. Assume that $T(n)$ is constant for sufficiently small $n$. Make your bounds as tight as possible,and justify your answers.

**a.** $T(n)=4T(n/3)+n\lg n=\Theta(n^{\lg 3})$

**b.** $T(n)=3T(n/3)+n/\lg n=\Theta(n\lg\lg n)$

**c.** $T(n)=4T(n/2)+n^2\sqrt n=\Theta(n^2\sqrt n)$

**d.** $T(n)=3T(n/3-2)+n/2=\Theta(n\lg n)$

**e.** $T(n)=2T(n/2)+n/\lg n=\Theta(n\lg\lg n)$

**f.** $T(n)=T(n/2)+T(n/4)+T(n/8)+n=\Theta(n)$

**g.** $T(n)=T(n-1)+1/n=\Theta(\lg n)$

**h.** $T(n)=T(n-1)+\lg n=\Theta(n\lg n)$

**i.** $T(n)=T(n-2)+1/\lg n=\Theta(\lg\lg n)$

**j.** $T(n)=\sqrt nT(\sqrt n)+n=\Theta(n\lg\lg n)$

###4-4 ***Fibonacci numbers***

This problem develops properties of the Fibonacci numbers,which are defined by recurrence(3.22). We shall use the technique of generating functions to solve the Fibonacci recurrence. Define the ***generating function*** (or ***formal power series***) $\mathcal{F}$ as 

$$
\begin{equation}\begin{split}\
\mathcal{F}(z)&=\sum_{i=0}^\infty F_iz^i\\
&=0+z+z^2+2z^3+3z^4+5z^5+8z^6+13z^7+21z^8+\cdots\
\end{split}\end{equation}
$$

where $F_i$ is the $i$th Fibonacci number.

**a.** Show that $\mathcal{F}(z)=z+z\mathcal{F}(z)+z^2\mathcal{F}(z)$

假设$H(z)=z+z\mathcal{F}(z)+z^2\mathcal{F}(z)$，则

$$
\begin{equation}\begin{split}\
H_i(z)&=z+z^2&+z^3+2z^4+3z^5+5z^6+8z^7+13z^8+\cdots\\
&&+z^3+z^4+2z^5+3z^6+5z^7+8z^8+\cdots\\
&=\mathcal{F}(z)\
\end{split}\end{equation}
$$

**b.c.d**略

###4.5 ***Chip testing***

Professor Diogenes has $n$ supposedly identical integrated-circuit chips that in principle are capable of testing each other. The professor’s test jig accommodates two chips at a time. When the jig is loaded, each chip tests the other and reports whether it is good or bad. A good chip always reports accurately whether the other chip is good or bad, but the professor cannot trust the answer of a bad chip. Thus, the four possible outcomes of a test are as follows:

    Chip A says     Chip B says     Conclusion
    --------------------------------------------
    B is good       A is good       both are good, or both are bad
    B is good       A is bad        at least one is bad
    B is bad        A is good       at least one is bad
    B is bad        A is bad        at least one is bad
    
**a.** Show that if more than $n/2$ chips are bad, the professor cannot necessarily determine which chips are good using any strategy based on this kind of pairwise test. Assume that the bad chips can conspire to fool the professor.

这个很简单，只要有至少与好芯片数量相同的坏芯片联合起来伪装好芯片，就不可能找出好芯片。

**b.** Consider the problem of finding a single good chip from amongnchips, assuming that more than $n/2$ of the chips are good. Show that $\lfloor n/2\rfloor$ pairwise tests are sufficient to reduce the problem to one of nearly half the size.

这里可以分为三种情况：

1. 芯片A与芯片B互相报告对方是好的，假设这种情况有$k$对。
2. 芯片A与芯片B中有一方报告对方是好的，另外一方报告对方是坏的，假设这种情况有$m$对。
3. 芯片A与芯片B互相报告对方是坏的，假设这种情况有$p$对。

对第2种情况，假设芯片A报告芯片B是好的，而芯片B报告芯片A是坏的，那么芯片A就一定是坏的。于是，在第二种情况中可以排除掉$m$个坏芯片。第3种情况中，可以确定其中至少有$p$个坏芯片，可以将这种情况下的$p$对芯片全部排除。对第1种情况，芯片A与B有可能都是好的也有可能都是坏的，此时随机将其中的一个排除，也能保证在第1种情况下坏芯片与好芯片的比例不变。而第2种情况与第3种情况又排除了尽可能多的坏芯片，所以在这样的排除规则下仍旧至少维持了原比例不变（有可能好芯片的比例更高）。此时还剩下$k+m$个芯片，由于$k+m+p=\lfloor n/2\rfloor$，所以命题得证。

**c.** Show that the good chips can be identified with $\Theta(n)$ pairwise tests, assuming that more than $n/2$ of the chips are good. Give and solve the recurrence that describes the number of tests.

根据b部分的结论，有$\sum_{i=1}^{\lg n}\frac{n}{2^i}=\Theta(n)$.

###4.6 ***Monge arrays***

略

> Written with [StackEdit](http://benweet.github.io/stackedit/).