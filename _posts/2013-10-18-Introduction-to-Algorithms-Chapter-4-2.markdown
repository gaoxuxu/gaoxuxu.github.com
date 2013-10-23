---
author: gavin
comments: true
date: 2013-10-23 18:30:25
layout: post
title: 算法导论第四章——代换法和递归树
categories:
- 算法
---

##4.3 The substitution method(代换法) for solving recurrences

用代换法解递归式有两个步骤：

1. 猜测解的形式
2. 用数学归纳法找出使解真正有效的常数。

代换法可以用来确定一个递归式的上界或下界，可以直接扩展边界条件，使递归假设对很小的$n$也成立。

并不存在通用的方法来猜测递归式的正确解。这种猜测需要经验，有时甚至是创造性的。有一种方法是先证明递归式的较松的上下界，然后再缩小不确定性空间。

有时或许能够猜出递归式解的渐近界，但却会在归纳证明时出现一些问题。通常，问题出在归纳假设不够强，无法证明其准确的界。遇到这种情况，可以去掉一个低阶项来修改所猜的界，以使证明顺利进行。

在运用渐近表示时很容易出错。例如，对合并排序运行时间的上界，由假设$T(n)\le cn$并证明

$$
T(n)\le 2(c\lfloor n/2\rfloor) + n\le cn+n=O(n)\quad\quad \Leftarrow wrong!!
$$

错误在于没有证明归纳假设的准确形式，即$T(n)\le cn$。

有时，对一个递归式做一些简单的代数变换，就会使之变成熟悉的形式。例如，考虑

$$
T(n)=2T(\lfloor\sqrt n\rfloor) + \lg n
$$

将其简化的方法是改变变量。设$m=\lg n$，得到

$$
T(2^m)=2T(2^{m/2})+m
$$

设$S(m)=T(2^m)$，得到新的递归式

$$
S(m)=2S(m/2)+m
$$

得到$S(m)=O(m\lg m)$，最终有$T(n)=T(2^m)=S(m)=O(m\lg m)=O(\lg n\lg\lg n)$

##Exercises

###4.3-1

Show that the solution of $T(n)=T(n-1)+n$ is $O(n^2)$。  
可以看出

$$
T(n)=1+2+\cdots +n=\sum _{i=1}^ni=\frac{n(n+1)}{2}
$$

假设$c$为常数，对所有的$n\ge n _0$，都有$T(n)\le cn^2+bn$

$$
T(n)=T(n-1)+n\le c(n-1)^2+(b+1)n-b=cn^2+(b+1-2c)n+c-b
$$

当$b=c\ge\frac{1}{2}$时，有

$$
T(n)\le cn^2+bn
$$

###4.3-2

Show that the solution of $T(n)=T(\lceil n/2\rceil)+1$ is $O(\lg n)$  
假设有$T(n)=c\lg{(n-b)}$

$$
\begin{equation}\begin{split}\
T(n)&=T(\lceil n/2\rceil)+1\\
&=c\lg{\lceil n/2 -b\rceil}+1\\
&\le c\lg(\frac{n-2b+2}{2}) - 1\\
&=c\lg(n-2b+2)\\
&\le c\lg(n-b)\quad(if\ b\ge 2)\
\end{split}\end{equation}
$$

###4.3-3

We saw that the solution of $T(n)=2T(\lfloor n/2\rfloor)+n$ is $O(n\lg n)$.Show that the solution of this recurrence is also $\Omega(n\lg n)$. Conclude that the solution is $\Theta(n\lg n)$.  
假设$T(n)=cn\lg n$

$$
\begin{equation}\begin{split}\
T(n)&=2T(\lfloor n/2\rfloor)+2T(\lceil n/2\rceil)+n-2T(\lceil n/2\rceil)\\
&=4T(n/2)+n-2T(\lceil n/2\rceil)\\
&\ge 2cn\lg(n/2)+n-c(n+1)\lg(\frac{n+1}{2})\\
&=2cn\lg n-2cn+n-cn\lg(n+1)-c\lg(n+1)+cn+c\\
&\ge cn\lg n+c\lg{\left(\frac{n^n}{(n+1)^{n+1}}\right)}+(1-c)n+c\\
&\ge cn\lg n\quad\quad(if\ n\ge \frac{1}{1-\frac{1}{c}},c>1)\\
\Rightarrow T(n)=\Theta(n\lg n)\
\end{split}\end{equation}
$$

###4.3-4

Show that by making a different inductive hypothesis, we can overcome the difficulty with the boundary condition $T(1)=1$ for recurrence(4.19) without adjusting the boundary conditions for the inductive proof.  
假设$T(n)=cn\lg n+bn,b\ge 1$

###4.3-5

Show that $\Theta(nlgn)$ is the solution to the “exact” recurrence (4.3) for merge sort.  
可以使用[Akra-Bazzi method](http://en.wikipedia.org/wiki/Akra-Bazzi _method)：

$$
T(x)=g(x)+\sum _{i=1}^ka _iT(b _ix+h _i(x))\quad\quad for\ x\ge x _0
$$

当$\sum _{i=1}^ka _ib _i^p=1$时，有

$$
T(x)\in \Theta\left(x^p\left(1+\int _i^x\frac{g(u)}{u^{p+1}}du\right)\right)
$$

将[Akra-Bazzi method](http://en.wikipedia.org/wiki/Akra-Bazzi _method)应用到本题时，可得$p=1,|g(u)|\in n$，所以

$$
\begin{equation}\begin{split}\
T(n)&\in \Theta\left(n\left(1+\int _i^n\frac{u}{u^2}du\right)\right)\\
&\in\Theta(n(1+\ln n))\\
&\in \Theta(n\lg n)\
\end{split}\end{equation}
$$

###4.3-6

Show that the solution to $T(n)=2T(\lfloor n/2\rfloor+17)+n$ is $O(nlgn)$.  
解法同4.3-5，使用Akra-Bazzi method.

###4.3-7

Using the master method in Section 4.5, you can show that the solution to the recurrence $T(n)=4T(n/3)+n$ is $T(n)=\Theta(n^{\log _3^4})$. Show that a substitution proof with the assumption $T(n)\le cn^{log _3^4}$ fails. Then show how to subtract off a lower-order term to make a substitution proof work.  


###4.3-8

Using the master method in Section 4.5, you can show that the solution to the recurrence $T(n)=4T(n/2)+n^2$ is $T(n)=\Theta(n^2)$. Show that a substitution proof with the assumption $T(n)\le cn^2$ fails. Then show how to subtract off a lower-order term to make a substitution proof work.

###4.3-9
Solve the recurrence $T(n)=3T(\sqrt n)+\log n$ by making a change of variables. Your solution should be asymptotically tight. Do not worry about whether values are integral.  
令$n=2^m$，

$$
\begin{equation}\begin{split}\
T(n)&=3T(\sqrt n)+\lg n\\
\Rightarrow\quad T(2^m)&=3T(2^{m/2})+m\\
S(m)&=T(2^m)=3S(m/2)+m\\
&=\Theta(m^{\lg 3})\\
\Rightarrow\quad T(n)&=\Theta(\lg 3\lg n)\
\end{split}\end{equation}
$$

##The recursion-tree method for solving recurrences

递归树方法能帮我们直接得到好的猜测。在递归树中，每一个节点都代表递归函数调用集合中一个子问题的代价。将树中每一层内的代价相加得到一个每层代价的集合，再将每层的代价相加得到所有层次的总代价。递归树很适合用于递归式表示分治算法运行时间的情况。

递归树最适合用来产生好的猜测，然后用代换法验证。使用递归树产生好的猜测时，可以容忍小量的“不良量”。如果画递归树时非常仔细，那么就可以直接用递归树作为递归式解的证明。

##Exercises

###4.4-1

Use a recursion tree to determine a good asymptotic upper bound on the recurrence $T(n)=3T(\lfloor n/2\rfloor)+n$.Use the substitution method to verify your answer.  
由于$(\frac{1}{2})^{\lg n}n=1$，所以递归树的深度为$\lg n+1$.总代价为

$$
\begin{equation}\begin{split}\
T(n)&\le cn+\frac{3}{2}cn+\cdots+\left(\frac{3}{2}\right)^{\lg n-1}cn+\Theta(n^{\lg 3})\\
&=\sum _{i=0}^{\lg n-1}\left(\frac{3}{2}\right)^icn+\Theta(n^{\lg 3})\\
&=\frac{\left(\frac{3}{2}\right)^{\lg n}-1}{(3/2)-1}cn+\Theta(n^{\lg 3})\\
&=2c\left(3^{\lg n}-2^{\lg n}\right)+\Theta(n^{\lg 3})=O(n^{\lg 3})\
\end{split}\end{equation}
$$

###4.4-2

Use a recursion tree to determine a good asymptotic upper bound on the recurrence $T(n)=T(n/2)+n^2$.Use the substitution method to verify your answer.  
与上题相同，树深为$\lg n+1$，总代价为

$$
\begin{equation}\begin{split}\
T(n)&=cn^2+\frac{1}{2}cn^2+\cdots+\left(\frac{1}{2}\right)^{\lg n-1}cn^2+\Theta(1)\\
&=\sum _{i=0}^{\lg n-1}\left(\frac{1}{2}\right)^icn^2+\Theta(1)\\
&<2cn^2+\Theta(1)=O(n^2)\
\end{split}\end{equation}
$$

###4.4-3

Use a recursion tree to determine a good asymptotic upper bound on the recurrence $T(n)=4T(n/2+2)+n$.Use the substitution method to verify your answer.  
树深为$\lg n+1$，总代价为

$$
\begin{equation}\begin{split}\
T(n)&=cn+4c(\frac{n}{2}+2)+\cdots+4^ic\left(\frac{n}{2^i}+\frac{2^{i-1}-1}{2^{i-2}}+2\right)+\cdots+4^{\lg n-1}c\left(\frac{n}{2^{\lg n-1}}+\frac{2^{\lg n-2}-1}{2^{\lg n-3}}+2\right)+\Theta(n^2)\\
&=\sum _{i=0}^{\lg n-1}2^icn+\sum _{i=0}^{\lg n-1}4^{i+1}c-\sum _{i=0}^{\lg n-1}2^{i+2}c+\Theta(n^2)\\
&=2cn(n-1)+\frac{4}{3}(n^2-1)-4(n-1)+\Theta(n^2)\\
&\le O(n^2)\
\end{split}\end{equation}
$$

###4.4-4

Use a recursion tree to determine a good aysmptotic upper bound on the recurrence $T(n)=2T(n-1)+1$.Use the substitution method to verify your answer.  
树深为$n$，总代价为

$$
T(n)=\sum _{i=0}^{n-1}2^ic=c(2^n-1)=O(2^n)
$$

###4.4-5

Use a recursion tree to determine a good asymptotic upper bound on the recurrence $T(n)=T(n-1)+T(n/2)+n$.Use the substitution method to verify your answer.   

           4
         /   \
        3     2
       / \   / \
      2   1 1   1
     / \
    1   1

从递归树可以看出

$$
\begin{equation}\begin{split}\
T(n)&=\sum _{i=1}^nT(\frac{i}{2})+\sum _{i=1}^nci\\
&\le nT(\frac{n}{2})+\frac{n(n-1)}{2}c\\
&\le 2nT(\frac{n}{2})\
\end{split}\end{equation}
$$

令$n=2^m,S(m)=T(n)=2^{m+1}S(m-1)$，有

$\lg{(S(m))}=m+1+\lg(S(m-1))$，令$\lg(S(m))=L(m)$，则

$L(m)=L(m-1)+m+1=\frac{m(m+3)}{2}\le m^2$

$\Rightarrow S(m)=2^{L(m)}\le 2^{m^2}$

$\Rightarrow T(n)\le 2^{\lg^2 n}$

###4.4-6

Argue that the solution to the recurrence $T(n)=T(n/3)+T(2n/3)+cn$,where $c$ is a constant,is $\Omega(n\lg n)$ by appealing to a recursion tree.  
树的最短深度为$\log _3n+1$，而且最短路径每一层的和都是$cn$，所以

$$
T(n)>=(\log _3n+1)cn=\Omega(n\lg n)
$$

###4.4-7

Draw the recursion tree for $T(n)=4T(\lfloor n/2\rfloor)+cn$,where $c$ is a constant,and provide a tight asymptotic bound on its solution.Verify your bound by the substitution method.  
$T(n)=\Theta(n^2)$

###4.4-8

Use a recursion tree to give an asymptotically tight solution to the recurrence $T(n)=T(n-a)+T(a)+cn$,where $a\ge 1$ and $c>0$ are constants.  
$T(n)=\frac{n}{a}T(a)+c\frac{n}{a}\frac{n+1}{2}=cn+\frac{c}{2a}(n^2+n)=\Theta(n^2)$

###4.4-9

Use a recursion tree to give an asymptotically tight solution to the recurrence $T(n)=T(\alpha n)+T((1-\alpha)n)+cn$,where $\alpha$ is a constant in the range $0<\alpha<1$ and $c>0$ is also a constant.  
下界好证，参考4.4-6，$T(n)=\Omega(n\lg n)$，假设$T(n)=O(n\lg n)$

$$
\begin{equation}\begin{split}\
T(n)&=T(\alpha n)+T((1-\alpha)n) + cn\\
&\le d\alpha n \lg(\alpha n) + d(1-\alpha)n\lg((1-\alpha)n) + cn\\
&=d\alpha n\lg\alpha+d\alpha n\lg n+d(1-\alpha)n\lg(1-\alpha)+d(1-\alpha)n\lg n+cn\\
&=dn\lg n+dn(\alpha\lg\alpha+(1-\alpha)\lg(1-\alpha))+cn\\
&\le dn\lg n\quad\quad\left(for\ \  0<d\le\frac{-c}{\alpha\lg\alpha+(1-\alpha)\lg(1-\alpha)}\right)\
\end{split}\end{equation}
$$

> Written with [StackEdit](http://benweet.github.io/stackedit/).