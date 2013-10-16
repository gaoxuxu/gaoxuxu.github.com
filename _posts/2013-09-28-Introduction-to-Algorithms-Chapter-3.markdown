---
author: gavin
comments: true
date: 2013-10-16 18:08:25
layout: post
title: 算法导论第三章
categories:
- 算法
---

#Growth of Functions
对于足够大的输入规模来说，在精确表示的运行时间中，常系数和低阶项是由输入规模所决定的。当输入规模大到使只有运行时间的增长量级有关时，就是在研究算法的渐进效率，就是在研究算法的**渐近**效率。也就是说，从极限角度看，我们只关心算法运行时间如何随着输入规模的无限增长而增长。通常，对不是很小的输入规模而言，从渐近意义上说更有效的算法是最佳的选择。

##3.1 Asymptotic notation

###**Θ-notation**

对于一个给定的函数$g(n)$，我们通过$\Theta(g(n))$来表示一个函数集合：

$$\Theta(g(n))=\{f(n):there\ exist\ positive\ constants\ c _1,c _2,and\ n _0\ such\ that\ 0\le c _1g(n)\le f(n)\le c _2g(n)\ for\ all\ n\ge n _0\}.$$

因为$\Theta(g(n))$是一个集合，我们可以写成"$f(n)\in \Theta(g(n))$"来表示$f(n)$是$\Theta(g(n))$的元素。不过，通常写成"$f(n)=\Theta(g(n))$"来表示相同的意思。  
对所有的$n\ge n _0$，函数$f(n)$在一个常数因子的范围内与$g(n)$相等，我们说$g(n)$是$f(n)$的一个**渐近确界**。  
$\Theta(g(n))$的定义需要每个成员$f(n)\in \Theta(g(n))$都是**渐近非负**，也就是说当$n$足够大时$f(n)$是非负的。(**渐近正**函数是当$n$足够大时总为正值）。这就要求函数$g(n)$也是渐近非负的，否则集合$\Theta(g(n))$就是空集。因此，假定$\Theta$记号中用到的每个函数都是渐近非负的。

###**O-notation**

$\Theta$记号渐近地给出一个函数的上界和下界。当只有**渐近上界**时，使用$O$记号。对一个函数$g(n)$，用$O(g(n))$表示一个函数集合：

$$O(g(n))=\{f(n):there\ exist\ positive\ constants\ c\ and\ n _0\ such\ that\ 0\le f(n)\le cg(n)\ for\ all\ n\ge n _0\}$$

为表示一个函数$f(n)$是集合$O(g(n))$的一个元素，记$f(n)=O(g(n))$。注意$f(n)=\Theta(g(n))$隐含着$f(n)=O(g(n))$，所以有$\Theta(g(n))\subseteq O(g(n))$。  
这里比较有趣的一点是，当$c=a+|b|$并且$n _0=1$时，有$an+b=O(n^2)$。  

###$\Omega$**-notation**

$\Omega$记号给出函数的渐近下界。给定一个函数$g(n)$，用$\Omega(g(n))$表示一个函数集合

$$\Omega(g(n))=\{f(n):there\ exist\ positive\ constants\ c\ and\ n _0\ such\ that\ 0\le cg(n)\le f(n)\ for\ all\ n\ge n _0\}$$

###Theorem 3.1

For any two functions $f(n)$ and $g(n)$,we have $f(n)=\Theta(g(n))$ if and only if $f(n)=O(g(n))$ and $f(n)=\Omega(g(n))$.

###$o$**-notation**

$O$记号所提供的渐近上界可能是也可能不是渐近紧确的。界$2n^2=O(n^2)$是渐近紧确的，但$2n=O(n^2)$却不是的。我们使用$o$记号来表示非渐近紧确的上界。$o(g(n))$的形式定义为集合

$$o(g(n))=\{f(n):for\ any\ positive\ constant\ c>0,there\ exists\ a\ constant\ n _0>0\ such\ that\ 0\le f(n)<cg(n)\ for\ all\ n\ge n _0\}$$

例如，$2n=o(n^2)$，但是$2n^2\ne o(n^2)$  
在$o$表示中，当$n$趋于无穷时，有

$$\lim _{n\to \infty}\frac{f(n)}{g(n)}=0$$

同时这也是$o$记号的定义。

###$\omega$**-notation**

$\omega$记号与$\Omega$记号的关系与$o$记号与$O$记号的关系一样。使用$\omega$记号来表示非渐近紧确的下界。它的一种定义为

$$f(n)\in \omega(g(n))\ if\ and\ only\ if\ g(n)\in o(f(n))$$

$\omega(g(n))$的形式定义为集合

$$\omega(g(n))=\{f(n):for\ any\ positive\ constant\ c>0,there\ exists\ a\ constant\ n _0>0\ such\ that\ 0\le cg(n)<f(n)\ for\ all\ n\ge n _0\}$$

在$\omega$表示中，当$n$趋于无穷时，有

$$\lim _{n\to \infty}\frac{f(n)}{g(n)}=\infty$$

###函数间的比较

实数的许多关系属性可以应用于渐近比较。下面假设$f(n)$和$g(n)$是渐近正值函数。  
**传递性（Transitivity）：**  

$$\begin{array}{1}f(n)=\Theta(g(n))\ and\ g(n)=\Theta(h(n))&imply\ \ f(n)=\Theta(h(n)),\\
f(n)=O(g(n))\ and\ g(n)=O(h(n))&imply\ \ f(n)=O(h(n)),\\
f(n)=\Omega(g(n))\ and\ g(n)=\Omega(h(n))&imply\ \ f(n)=\Omega(h(n)),\\
f(n)=o(g(n))\ and\ g(n)=o(h(n))&imply\ \ f(n)=o(h(n)),\\
f(n)=\omega(g(n))\ and\ g(n)=\omega(h(n))&imply\ \ f(n)=\omega(h(n)).\end{array}$$

**自反性（Reflexivity）：**  

$$\begin{array}{1}f(n)=\Theta(f(n)),\\
f(n)=O(f(n)),\\
f(n)=\Omega(f(n)).\end{array}$$

**对称性（Symmetry）：**  

$$f(n)=\Theta(g(n))\ if\ and\ only\ if\ g(n)=\Theta(f(n))$$

**转置对称性（Transpose symmetry）：**  

$$\begin{array}{1}f(n)=O(g(n))&if\ and\ only\ if\ g(n)=\Omega(f(n)),\\
f(n)=o(g(n))&if\ and\ only\ if\ g(n)=\omega(f(n))\end{array}$$

##Exercises

###3.1-1

Let $f(n)$ and $g(n)$ be asymptoically nonnegative functions.Using the basic definition of $\Theta$-notation,prove that $\max(f(n),g(n))=\Theta(f(n) + g(n))$  
假设有$\max(f(n),g(n))=\Theta(f(n) + g(n))$  
于是根据$\Theta$符号的定义，存在正数$c _1,c _2,n _0$，使得当$n\ge n _0$时，有

$$0\le c _1(f(n)+g(n))\le \max(f(n),g(n))\le c _2(f(n)+g(n))$$

又，当$c _1=\frac{1}{2},c _2=1,n\ge n _0$时，上式成立，由此可证假设成立。

###3.1-2

Show that for any real constants $a$ and $b$,where $b>0$,

$$
(n+a)^b=\Theta(n^b)
$$

根据题意，存在正数$c _1,c _2,n _0$，使得当$n\ge n _0$时有

$$
0\le c _1n^b\le (n+a)^b\le c _2n^b
$$

假设$n _0=2|a|$，则有$(n+a)^b\ge |a|^b\ge c _1n^b$，因此可以取$c _1=\frac{1}{2}$  
对于不等式的右侧，有$(3a)^b\le c _2n^b$，可取$c _2=\left(\frac{3}{2}\right)^b$

###3.1-3

Explain why the statement,"The running time of algorithm $A$ is at least $O(n^2),$" is meaningless.  
这句话的意思可以理解为，“$A$算法的运行时间大于等于某些$O(n^2)$对应集合的函数$f(n)$”，由于函数$g(n)=0$时，有$g(n)\in O(n^2)$，因此这句话其实什么也没告诉我们。

###3.1-4

Is $2^{n+1}=O(2^n)$?Is $2^{2n}=O(2^n)$?  
由于$2^{n+1}=2\cdot 2^n$，所以很明显$2^{n+1}=O(2^n)$。  
假设存在正常数$c _1,n _0$，使得对所有的$n\ge n _0$后一个等式成立，则有  

$$
0\le 2^{2n}\le c _12^n
$$

因此$2^{2n}\notin O(2^n)$。

###3.1-5

Prove Theorem 3.1.  
定理3.1可以理解为$f(n)=\Theta(g(n))\Longleftrightarrow f(n)\in \left(O(g(n)\cap \Omega(g(n))\right)$。  
首先，由$f(n)=O(g(n))$可知，存在正数$c _1,n _1$，使得对所有的$n\ge n _1$，都有$0\le f(n)\le c _1g(n)$。  
其次，由$f(n)=\Omega(g(n))$可知，存在正数$c _2,n _2$，使得对所有的$n\ge n _2$，都有$c _2g(n)\le f(n)$。  
由此可知，存在正数$c _1,c _2,n _0=\max(n _1,n _2)$，使得对所有的$n\ge n _0$，都有$c _2g(n)\le f(n)\le c _1g(n)$，即$f(n)=\Theta(g(n))$。  
同理可证$f(n)=\Theta(g(n))\Longrightarrow f(n)\in \left(O(g(n)\cap \Omega(g(n))\right)$。

###3.1-6

Prove that the running time of an algorithm is $\Theta(g(n))$ if and only if its worst-case running time is $O(g(n))$ and its best-case running time is $\Omega(g(n))$.  
这道题意思跟3.1-5差不多。

###3.1-7

Prove that $o(g(n)) \cap \omega(g(n)) = \varnothing$.  
由$f(n)=o(g(n))$可知，对于任意的$c>0$，都存在对于$n\ge n _0$，有

$$
0\le f(n) < cg(n)
$$

同样，由$f(n)-\omega(g(n))$可知，对于任意的$c>0$，都存在对于所有的$n\ge n _0$，有

$$
cg(n)<f(n)
$$

由此可得$o(g(n)) \cap \omega(g(n)) = \varnothing$

###3.1-8

We can extend our notation to the case of two parameters ***n*** and ***m*** that can go to infinity independently at different rates. For a given function $g(n,m)$, we denote by $O(g(n,m))$ the set of functions

$$
\begin{equation}\begin{split}O(g(n,m))=\{f(n,m):&there\ exist\ positive\ constants\ c,n _0,and\ m _0 \\
&such\ that\ 0\le f(n,m)\le cg(n,m) \\
&for\ all\ n\ge n _0\ or\ m\ge m _0\}\end{split}\end{equation}
$$

Give corresponding definitions for $\Omega(g(n,m))$ and $\Theta(g(n,m))$.  

$$
\begin{equation}\begin{split}\Omega(g(n,m))=\{f(n,m):&there\ exist\ positive\ constants\ c,n _0,and\ m _0 \\
&such\ that\ cg(n,m)\le f(n,m) \\
&for\ all\ n\ge n _0\ or\ m\ge m _0\}\end{split}\end{equation}
$$

$$
\begin{equation}\begin{split}\Theta(g(n,m))=\{f(n,m):&there\ exist\ positive\ constants\ c _1,c _2,n _0,and\ m _0 \\
&such\ that\ 0\le c _1g(n,m)\le f(n,m)\le c _2g(n,m) \\
&for\ all\ n\ge n _0\ or\ m\ge m _0\}\end{split}\end{equation}
$$

##3.2 Standard notations and common functions

###Monotonicity(单调性)

A function $f(n)$ is ***monotonically increasing*** if $m\le n$ implies $f(m)\le f(n)$. Similarly,it is ***monotonically decreasing*** if $m\le n$ implies $f(m)\ge f(n)$. A function $f(n)$ is ***strictly increasing*** if $m < n$ implies $f(m) < f(n)$ and ***strictly decreasing*** if $m < n$ implies $f(m) > f(n)$.

###Floors and ceilings(上取整和下取整)

For all real $x$:

$$
x-1<\lfloor x\rfloor \le x\le \lceil x\rceil < x + 1
$$

For any integer $n$:

$$
\lfloor \frac{n}{2}\rfloor + \lceil \frac{n}{2} \rceil = n
$$

and for any real number $x\ge 0$ and integers $a,b>0$,

$$
\left \lceil \frac{\lceil x/a \rceil}{b} \right \rceil = \left \lceil \frac{x}{ab} \right \rceil
$$

$$
\left \lfloor \frac{\lfloor x/a \rfloor}{b} \right \rfloor = \left \lfloor \frac{x}{ab} \right \rfloor
$$

$$
\lceil \frac{a}{b} \rceil \le \frac{a+(b-1)}{b}
$$

$$
\lfloor \frac{a}{b} \rfloor \ge \frac{a-(b-1)}{b}
$$

The floor function $f(x) = \lfloor x\rfloor$ is monotonically increasing, as is the ceiling function $f(x)=\lceil x \rceil$.

###Modular arithmetic(取模运算)

For any integer $a$ and any positive integer $n$, the value $a \mod n$ is the ***remainder***(or ***residue***) of the quotient $a/n$:

$$
a \mod n = a-n\lfloor a/n \rfloor
$$

It follows that

$$
0\le a\mod n<n
$$

If $(a\mod n) = (b\mod n)$,we write $a\equiv b(\mod n)$ and say that $a$ is ***equivalent*** to $b$,modulo $n$.In other words,$a\equiv b(\mod n)$ if $a$ and $b$ have the same remainder when divided by $n$.Equivalently,$a\equiv b(\mod n)$ if and only if $n$ is a divisor of $b-a$. We write $a\not\equiv b(\mod n)$ if $a$ is not equivalent to $b$,modulo $n$.

###Polynomials(多项式)

Given a nonnegative integer $d$,a ***polynomial in n of degree d*** is a function $p(n)$ of the form

$$
p(n)=\sum _{i=0}^da _in^i
$$
where the constants $a _0,a _1,\dots,d _d$ are the ***coefficients*** of the polynomial and $a _d \neq 0$. A polynomial is asymptotically positive if and only if $a _d>0$. For an asymptotically positive polynimial $p(n)$ of degree $d$,we have $p(n)=\Theta(n^d)$. For any real constant $a\ge 0$,the function $n^a$ is monotonically increasing,and for any real constant $a\le 0$,the function $n^a$ is monotonically decreasing. We say that a function $f(n)$ is ***polynomially bounded*** if $f(n)=O(n^k)$ for some constant $k$.

###Exponentials(指数式)

For all real $a > 0$, $m$, and $n$, we have the following identities:

$$
\begin{equation}\begin{split}\
a^0&=1, \\
a^1&=a, \\
a^{-1}&=1/a, \\
(a^m)^n&=a^{mn}, \\
(a^m)^n&=(a^n)^m, \\
a^ma^n&=a^{m+n}\
\end{split}\end{equation}
$$

For all $n$ and $a\ge 1$, the function $a^n$ is monotonically increasing in $n$. When
convenient, we shall assume $0^0 = 1$.

We can relate the rates of growth of polynomials and exponentials by the following fact. For all real constants $a$ and $b$ such that $a > 1$,

$$
\lim _{n\to \infty}\frac{n^b}{a^n}=0,
$$

from which we can conclude that

$$
n^b=o(a^n)
$$

Thus, any exponential function with a base strictly greater than $1$ grows faster than
any polynomial function.

Using $e$ to denote $2.71828\cdots$, the base of the natural logarithm function, we have for all real $x$,

$$
e^x = 1+x+\frac{x^2}{2!}+\frac{x^3}{3!}+\cdots=\sum _{i=0}^{\infty}\frac{x^i}{i!}
$$

For all real x,we have the inequality

$$
e^x\ge 1+x
$$

where equality holds only when $x = 0$. When $|x| = 1$, we have the approximation

$$
1+x\le e^x\le 1+x+x^2
$$

When $x \to 0$, the approximation of $e^x$ by $1+x$ is quite good:

$$
e^x = 1+x+\Theta(x^2)
$$

(In this equation, the asymptotic notation is used to describe the limiting behavior as $x\to 0$ rather than as $x\to \infty$.) We have for all $x$,

$$
\lim _{n\to \infty}\left(1+\frac{x}{n}\right)^n=e^x
$$

###Logarithms(对数式)

We shall use the following notations:

$$
\begin{equation}\begin{split}\
\lg n&=\log _2n & (binar\ logarithm),\\
\ln n&=\log _en & (natural\ logarithm),\\
\lg^kn&=(\lg n)^k & (exponentiation),\\
\lg \lg n&=\lg(\lg n) & (composition).\
\end{split}\end{equation}
$$

An important notational convention we shall adopt is that *logarithm functions will apply only to the next term in the formula*, so that $\lg n + k$ will mean $(\lg n)+ k$ and not $\lg(n + k)$. If we hold $b > 1$ constant, then for $n > 0$, the function $\log _bn$ is strictly increasing.

For all real $a > 0, b > 0, c > 0$, and $n$,

$$
\begin{equation}\begin{split}\
a&=b^{\log _ba},\\
\log _c(ab)&=\log _ca + \log _cb,\\
\log _ba^n&=n\log _ba,\\
\log _ba&=\frac{\log _ca}{\log _cb},\\
\log _b(1/a)&=-\log _ba,\\
\log _ba&=\frac{1}{\log _ab},\\
a^{\log _bc}&=c^{\log _ba}\
\end{split}\end{equation}
$$

where, in each equation above, logarithm bases are not $1$.

Changing the base of a logarithm from one constant to another changes the value of the logarithm by only a constant factor, and so we shall often use the notation “$\lg n$” when we don’t care about constant factors, such as in $O$-notation. Computer scientists find $2$ to be the most natural base for logarithms because so many algorithms and data structures involve splitting a problem into two parts.

There is a simple series expansion for $\ln(1 + x)$ when $|x| < 1$:

$$
\ln(1+x) = x - \frac{x^2}{2}+\frac{x^3}{3}-\frac{x^4}{4}+\frac{x^5}{5}-\cdots
$$

We also have the following inequalities for $x > -1$:

$$
\frac{x}{1+x}\le \ln(1+x)\le x
$$

where equality holds only for $x = 0$.

We say that a function $f(n)$ is ***polylogarithmically bounded*** if $f(n) = O(\lg^k n)$ for some constant $k$. We can relate the growth of polynomials and polylogarithms
by substituting $\lg n$ for $n$ and $2^a$ for $a$ in equation

$$
\lim _{n\to \infty}\frac{n^b}{a^n}=0,
$$

yielding

$$
\lim _{n\to \infty}\frac{\lg^bn}{(2^a)^{\lg n}}=\lim _{n\to \infty}\frac{\lg^bn}{n^a}=0
$$

From this limit, we can conclude that

$$
\lg^b n=o(n^a)
$$

for any constant $a > 0$. Thus, any positive polynomial function grows faster than any polylogarithmic function.

###Factorials(阶乘)

The notation $n!$ (read “$n$ factorial”) is defined for integers $n\ge 0$ as

$$
n!=\left\{\begin{array}{2}1 & if\ n=0,\\
n\cdot(n-1)! & if\ n>0.\end{array}\right.
$$

Thus, $n!=1\cdot 2\cdot 3\cdots n$.

A weak upper bound on the factorial function is $n! \le n^n$, since each of the $n$ terms in the factorial product is at most $n$. ***Stirling’s approximation***,

$$
n!=\sqrt{2\pi n}\left(\frac{n}{e}\right)^n\left(1+\Theta\left(\frac{1}{n}\right)\right)
$$

where $e$ is the base of the natural logarithm, gives us a tighter upper bound, and a lower bound as well. As Exercise 3.2-3 asks you to prove,

$$
\begin{equation}\begin{split}\
n!&=o(n^n),\\
n!&=\omega(2^n),\\
\lg(n!)&=\Theta(n\lg n)\
\end{split}\end{equation}
$$

where Stirling’s approximation is helpful in proving above equation. The following equation also holds for all $n\ge 1$:

$$
n!=\sqrt{2\pi n}\left(\frac{n}{e}\right)^ne^{\alpha _n}
$$

where

$$
\frac{1}{12n+1}<\alpha _n<\frac{1}{12n}
$$

###Functional iteration(函数迭代)

We use the notation $f^{(i)}(n)$ to denote the function $f(n)$ iteratively applied $i$ times to an initial value of $n$. Formally, let $f(n)$ be a function over the reals. For nonnegative integers $i$, we recursively define

$$
f^{(i)}(n)=\left\{\begin{array}{2}n & if\ i=0,\\
f(f^{(i-1)}(n)) & if\ i>0.\end{array}\right.
$$

For example, if $f(n)= 2n$, then $f^{(i)}(n) = 2^i n$.

###The iterated logarithm function(迭代对数函数)

这一部分讲的是[迭代对数函数](http://zh.wikipedia.org/wiki/%E8%BF%AD%E4%BB%A3%E5%B0%8D%E6%95%B8)，而不是中文版翻译的[多重对数函数](http://zh.wikipedia.org/wiki/%E5%A4%9A%E9%87%8D%E5%AF%B9%E6%95%B0%E5%87%BD%E6%95%B0)。

We use the notation $\lg^*n$ (read “log star of n”) to denote the iterated logarithm, defined as follows. Let $\lg^{(i)}n$ be as defined above, with $f(n)=\lg n$. Because the logarithm of a nonpositive number is undefined, $\lg^{(i)}n$ is defined only if $\lg^{(i-1)}n > 0$.Be sure to distinguish $\lg^{(i)}n$ (the logarithm function applied $i$ times in succession,starting with argument $n$) from $\lg^in$ (the logarithm of $n$ raised to the $i$th power).

Then we define the iterated logarithm function as

$$
\lg^*n=\min\{i\ge 0:\lg^{(i)}n\le 1\}
$$

The iterated logarithm is a *very* slowly growing function:

$$
\begin{equation}\begin{split}\
\lg^*2&=1\\
\lg^*4&=2\\
\lg^*16&=3\\
\lg^*65536&=4\\
\lg^*(2^{65536})&=5\
\end{split}\end{equation}
$$

Since the number of atoms in the observable universe is estimated to be about $10^80$, which is much less than $2^{65536}$, we rarely encounter an input size $n$ such that $\lg^*n > 5$.

###Fibonacci numbers(斐波那契数列)

We define the ***Fibonacci numbers*** by the following recurrence:

$$
\begin{equation}\begin{split}\
F _0\ &=\ 0\\
F _1\ &=\ 1\\
F _i\ &=\ F _{i-1}\ +\ F _{i-2} & for\ i\ge 2\
\end{split}\end{equation}
$$

Thus, each Fibonacci number is the sum of the two previous ones, yielding the sequence

$$
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55,\dots
$$

Fibonacci numbers are related to the ***golden ratio*** $\phi$ and to its conjugate $\widehat{\phi}$, which are the two roots of the equation

$$
x^2 = x + 1
$$

and are given by the following formulas (see Exercise 3.2-6):

$$
\begin{equation}\begin{split}\
\phi\ &=\ \frac{1+\sqrt 5}{2}\\
&=\ 1.61803\\
\widehat{\phi}\ &=\ \frac{1-\sqrt 5}{2}\\
&=\ -0.61803\
\end{split}\end{equation}
$$

Specifically, we have

$$
F _i\ =\ \frac{\phi^i-\widehat{\phi^i}}{\sqrt 5}
$$

which we can prove by induction (Exercise 3.2-7). Since $|\widehat{\phi}| < 1$, we have

$$
\begin{equation}\begin{split}\
\frac{|\widehat{\phi^i}|}{\sqrt 5}\ &<\ \frac{1}{\sqrt 5}\\
&<\ \frac{1}{2}\
\end{split}\end{equation}
$$

which implies that

$$
F _i\ =\ \left\lfloor\frac{\phi^i}{\sqrt 5}+\frac{1}{2}\right\rfloor
$$

which is to say that the $i$th Fibonacci number $F _i$ is equal to $\phi^i/\sqrt 5$ rounded to the nearest integer. Thus, Fibonacci numbers grow exponentially.

##Exercises

###3.2-1

Show that if $f(n)$ and $g(n)$ are monotonically increasing functions,then so are the functions $f(n)+g(n)$ and $f(g(n))$,and if $f(n)$ and $g(n)$ are in addition nonnegative,then $f(n)\cdot f(n)$ is monotonically increasing.  
略。

###3.2-2

Prove equation (3.16).  

$$
\begin{eqnarray}\
\because &a\ &\ =\quad c^{\log _ca} \\
\therefore &a^{\log _bc} &\ =\quad c^{\log _ca\cdot \log _bc}\\
&&\ =\quad c^{\frac{\log _ba}{\log _bc}\cdot \log _bc}\\
&&\ =\quad c^{\log _ba}\
\end{eqnarray}
$$

###3.2-3

Prove equation(3.19).Also prove that $n!=\omega(2^n)$ and $n!=o(n^n)$.  
首先引入斯特林近似方程

$$
n!=\sqrt{2\pi n}\left(\frac{n}{e}\right)^n\left(1+\Theta\left(\frac{1}{n}\right)\right)
$$

所以

$$
\begin{equation}\begin{split}\
\lg(n!)\quad &=\quad \lg(\sqrt{2\pi n}\left(\frac{n}{e}\right)^n\left(1+\Theta\left(\frac{1}{n}\right)\right))\\
&=\quad \frac{1}{2}\lg(2\pi n)+n\lg n-n\lg e\
\end{split}\end{equation}
$$

根据$\omega$-notation的定义:

$$
\lim _{n\to \infty}\frac{f(n)}{g(n)}=\infty
$$

由于

$$
\begin{equation}\begin{split}\
\lim _{n\to \infty}\frac{n!}{2^n}\quad &=\quad \lim _{n\to \infty}\left(\sqrt{2\pi n}\left(\frac{n}{2e}\right)^n\right)\\
&=\quad \infty\
\end{split}\end{equation}
$$

因此$n!=\omega{2^n}$  
同样的方法可以证明$n!=o(n^n)$

###3.2-4 $\star$

Is the function $\lceil \lg n\rceil !$ polynomially bounded? Is the function $\lceil \lg \lg n \rceil !$ polynomially bounded?

对于$\lceil \lg n\rceil !$，由于：

$$
\lceil \lg n\rceil !\ge (\lg n)!\approx\sqrt{2\pi \lg n}\left(\frac{\lg n}{e}\right)^{\lg n}
$$

又，对于常量$k$，有$n^k=(2^k)^{\lg n}$，且当$n\ge 2^{e2^k}$时，有$\frac{\lg n}{e}\ge 2^k$，此时$\lceil \lg n\rceil !>n^k$，因此它没有多项式界。

对于$f(n) = \lceil\lg\lg n\rceil!$，我们需要证明$f(n) = O(n^k)$，由于

$$
n! <= (\frac{n}{e})^{n+1}
$$

假设$v=\lceil\lg\lg n\rceil\le\lg\lg n + 1$，则

$$
\begin{equation}\begin{split}\
f(n)&=v!\le(\frac{v}{e})^{n+1}\\
&\le\left(\frac{\lg\lg n+1}{e}\right)^{\lg\lg n+2}\\
&\le\left(\frac{\lg\lg n}{2}\right)^{\lg\lg n+2}\\
&\le(\lg\lg n)^{\lg\lg n}&(because\quad x^2\le 2^{x+2})\
\end{split}\end{equation}
$$

现在只需证明$\left(\lg\lg n\right)^{\lg\lg n}=O(n^k)$，设存在正常量$c,n _0$，使得对所有的$n\ge n _0$，都有

$$
\begin{equation}\begin{split}\
(\lg\lg n)^{\lg\lg n} &\le cn^k\\
\iff\quad\lg^{(2)}n\cdot\lg^{(3)}n&\le k\lg cn\
\end{split}\end{equation}
$$

由于$\lg^{(2)}n\cdot\lg^{(3)}n\le(\lg^{(2)}n)^2<c _1(\lg n)^a$，即

$$
\left(\lg\lg n\right)^{\lg\lg n}<n^{ac _1}
$$

于是可以证明$\lceil\lg\lg n\rceil!$是有多项式界的。

###3.2-5

Which is asymptotically larger:$\lg(\lg^*n)$ or $\lg^*(\lg n)$?  
令$n=2^m$，则$\lg(\lg^*n)=\lg(1+\lg^*m)$并且$\lg^*(\lg n)=\lg^*m$。所以后者要比前者增长的快。

###3.2-6

Show that the golden ratio $\phi$ and its conjugate $\widehat{\phi}$ both satisfy the equation $x^2=x+1$.  
$(x-\frac{1}{2})^2-\frac{5}{4}=0$，带入即可证明。

###3.2-7

Prove by induction that the $i$th Fibonacci number satisfies the equality

$$
F _i=\frac{\phi^i-{\widehat{\phi}}^i}{\sqrt 5}
$$

where $\phi$ is the golden ratio and $\widehat{\phi}$ is its conjugate.  

$$
\begin{equation}\begin{split}\
F _0&=\frac{1-1}{\sqrt 5} = 0\\
F _1&=\frac{\phi^i-\widehat{\phi^i}}{\sqrt 5} = 1\\
F _i&=F _{i-1}+F _{n-2}=\frac{\phi^{i-1}-\widehat{\phi}^{i-1}+\phi^{i-2}-\widehat{\phi}^{i-2}}{\sqrt 5}\\
&=\frac{\phi^{i-2}(1+\phi)-\widehat{\phi}^{i-2}(1+\widehat{\phi})}{\sqrt 5}\\
&=\frac{\phi^i-\widehat{\phi}^i}{\sqrt 5}\
\end{split}\end{equation}
$$

###3.2-8

Show that $k\ln k=\Theta(n)$ implies $k=\Theta(n/\ln n)$.  
由$k\ln k=\Theta(n)\Rightarrow n=\Theta(k\ln k)\Rightarrow\ln n=\Theta(\ln(\ln k))=\Theta(\ln k)$  
所以$n/\ln n = \Theta(\frac{k\ln k}{\ln k})=\Theta(k)$  
即$k=\Theta(n/\ln n)$

##Problems

###3.1 *Asymptotic behavior of polynomials*

Let

$$
p(n)=\sum _{i=0}^da _in^i
$$

where $a _d>0$, be a degree-$d$ polynomial in $n$,and let $k$ be a constant.Use the definitions of the asymptotic notaions to prove the following properties.

**a.** If $k\ge d$,then $p(n)=O(n^k)$

$p(n)=O(n^d)=O(n^k)$

**b.** If $k\le d$,then $p(n)=\Omega(n^k)$

$$p(n)=\Omega(n^k)\Rightarrow c _1n^k\le p(n)\le c _2n^d$$

$$n\ge \sqrt[d-k]{\frac{c _1}{c _2}}$$

**c.** If $k=d$, then $p(n)=\Theta(n^k)$

a与b的交集

**d.** If $k>d$, then $p(n)=o(n^k)$

很容易证明$\lim _{n\to \infty}\frac{p(n)}{n^k}=0$

**e.** If $k<d$, then $p(n)=\omega(n^k)$

同样很容易证明$\lim _{n\to \infty}\frac{p(n)}{n^k}=\infty$
> Written with [StackEdit](http://benweet.github.io/stackedit/).