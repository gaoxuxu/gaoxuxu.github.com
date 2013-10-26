---
author: gavin
comments: true
date: 2013-10-26 20:21:25
layout: post
title: 算法导论第四章——Strassen算法
categories:
- 算法
---

##4.2 Strassen's algorithm for matrix multiplication

假设$A(a_{ij})$和$B_{ij}$都是$n\times n$矩阵，令$C=A\cdot B$，定义$C$的项为$c_{ij}$

$$
c_{ij}=\sum_{k=1}^na_{ik}\cdot b_{kj}
$$

首先，使用通用矩阵乘法算法：

    SQUARE-MATRIX-MULTIPLY(A,B)
    1   n = A.rows
    2   let C be a new n × n matrix
    3   for i = 1 to n
    4       for j = 1 to n
    5           c_i_j = 0
    6           for k = 1 to n
    7               c_i_j = c_i_j + a_i_k * b_k_j
    8   return C

SQUARE-MATRIX-MULTIPLY的运行时间很明显是$\Theta(n^3)$

###A simple divide-and-conquer algorithm

要使用分治法解决这个问题，首先假定$n=2^m$（也可以通过填0的方法使它成为$2^m\times 2^m矩阵$），m是正整数。

将$A,B,C$拆分成4个$\frac{n}{2}\times \frac{n}{2}$矩阵

$$
\begin{equation}\
A=\left(\begin{matrix}A_{11} & A_{12} \\A_{21} & A_{22}\end{matrix}\right),\
B=\left(\begin{matrix}B_{11} & B_{12} \\B_{21} & B_{22}\end{matrix}\right),\
C=\left(\begin{matrix}C_{11} & C_{12} \\C_{21} & C_{22}\end{matrix}\right). \label{}(4.9)\
\end{equation}
$$

因此，可以将等式$C=A\cdot B$改写为

$$
\begin{equation}\
\left(\begin{matrix}C_{11} & C_{12} \\C_{21} & C_{22}\end{matrix}\right)=\left(\begin{matrix}A_{11} & A_{12} \\A_{21} & A_{22}\end{matrix}\right)\cdot \left(\begin{matrix}B_{11} & B_{12} \\B_{21} & B_{22}\end{matrix}\right)\
\end{equation}
$$

上面的等式符合下面的四个等式

$$
\begin{equation}\begin{split}\
C_{11}\quad&=\quad A_{11}\cdot B_{11}+A_{12}\cdot B_{21}\\
C_{12}\quad&=\quad A_{11}\cdot B_{12}+A_{12}\cdot B_{22}\\
C_{21}\quad&=\quad A_{21}\cdot B_{11}+A_{22}\cdot B_{21}\\
C_{12}\quad&=\quad A_{21}\cdot B_{12}+A_{22}\cdot B_{22}\
\end{split}\end{equation}
$$

Ok,分解、解决以及合并步骤都已了解，就可以写出分治法版本算法的伪代码了

    SQUARE-MATRIX-MULTIPLY-RECURSIVE(A,B)
    1   n = A.rows
    2   let C be a new n × n matrix
    3   if n==1
    4       c11 = a11 * b11
    5   else partition A,B,and C as in equations(4.9)
    6       C11 = SQUARE-MATRIX-MULTIPLY-RECURSIVE(A11,B11)
                + SQUARE-MATRIX-MULTIPLY-RECURSIVE(A12,B21)
    7       C12 = SQUARE-MATRIX-MULTIPLY-RECURSIVE(A11,B12)
                + SQUARE-MATRIX-MULTIPLY-RECURSIVE(A12,B22)
    8       C21 = SQUARE-MATRIX-MULTIPLY-RECURSIVE(A21,B11)
                + SQUARE-MATRIX-MULTIPLY-RECURSIVE(A22,B21)
    9       C22 = SQUARE-MATRIX-MULTIPLY-RECURSIVE(A21,B12)
                + SQUARE-MATRIX-MULTIPLY-RECURSIVE(A22,B22)
    10  return C

要注意的是，第5行分解的过程，如果是要创建新的矩阵，则需要12个新的$n/2 \times n/2$矩阵，且复制过程的花费是$\Theta(n^2)$。为节省开销，可以使用角标换算的方法，这样的花费是$\Theta(1)$。第6-9行涉及8个$n/2\times n/2$矩阵的运算，因此这部分的花费是$8T(n/2)$。另外，第6-9行的加法运算的花费也是$\Theta(n^2)$。

因此，SQUARE-MATRIX-MULTIPLY-RECURSIVE的运行时间是

$$
T(n)\quad=\quad\left\{\begin{array}{2}\Theta(1)&if\ n=1\\
8T(n/2)+\Theta(n^2)&if\ n>1\end{array}\right.
$$

由此，$T(n)=\Theta(n^3)$。好像并不比通用算法快到哪去。

###Strassen's method

Strassen的方法不太明显，有四步：

1. 将矩阵$A,B,C$分解成$n/2\times n/2$的子矩阵。使用角标换算的话，这一步的消耗是$\Theta(1)$。
2. 创建10个$n/2\times n/2$矩阵$S_1,S_2,\dots,S_{10}$，其中的每一个都是第一步中两个矩阵的和或者差。创建这10个矩阵的花费是$\Theta(n^2)$。
3. 使用第一步和第二步中创建的子矩阵，递归计算出7个矩阵$P_1,P_2,\dots,P_7$。每一个矩阵$P_i$都是$n/2\times n/2$的。
4. 通过加减不同的$P_i$矩阵组合，计算出需要的矩阵$C$的子矩阵$C_{11},C_{12},C_{21},C_{22}$，这一步计算的花费也是$\Theta(n^2)$

因此，Strassen算法的运行时间是

$$
T(n)\quad=\quad\left\{\begin{array}{2}\Theta(1)&if\ n=1\\
7T(n/2)+\Theta(n^2)&if\ n>1\end{array}\right.
$$

即$T(n)=\Theta(n^{\lg 7})$

下面详细介绍其过程。在第2步中，创建下面10个矩阵：

$$
\begin{equation}\begin{split}\
&S_1\quad&=\quad B_{12}\ -\ B_{22}\ ,\\
&S_2\quad&=\quad A_{11}\ +\ A_{12}\ ,\\
&S_3\quad&=\quad A_{21}\ +\ A_{22}\ ,\\
&S_4\quad&=\quad B_{21}\ -\ B_{11}\ ,\\
&S_5\quad&=\quad A_{11}\ +\ A_{22}\ ,\\
&S_6\quad&=\quad B_{11}\ +\ B_{22}\ ,\\
&S_7\quad&=\quad A_{12}\ -\ A_{22}\ ,\\
&S_8\quad&=\quad B_{21}\ +\ B_{22}\ ,\\
&S_9\quad&=\quad A_{11}\ -\ A_{21}\ ,\\
&S_{10}\quad&=\quad B_{11}\ +\ B_{12}\ .\\\
\end{split}\end{equation}
$$

第三步中，递归计算出7个矩阵

$$
\begin{equation}\begin{split}\
P_1\quad&=\quad A_{11}\ \cdot\ S_1\ ,\\
P_2\quad&=\quad S_2\ \cdot\ B_{22}\ ,\\
P_3\quad&=\quad S_3\ \cdot\ B_{11}\ ,\\
P_4\quad&=\quad A_{22}\ \cdot\ S_4\ ,\\
P_5\quad&=\quad S_5\ \cdot\ S_6\ ,\\
P_6\quad&=\quad S_7\ \cdot\ S_8\ ,\\
P_7\quad&=\quad S_9\ \cdot\ S_{10}\ .\\\
\end{split}\end{equation}
$$

第4步有如下等式：

$$
\begin{equation}\begin{split}\
C_{11} &= P_5+P_4-P_2+P_6,\\
C_{12} &= P_1 + P_2,\\
C_{21} &= P_3+P_4,\\
C_{22} &= P_5+P_1-P_3-P_7\
\end{split}\end{equation}
$$

##Exercises

###4.2-1

Use Strassen's algorithm to compute the matrix product

$$
\left(\begin{matrix}1&3\\7&5\end{matrix}\right)\left(\begin{matrix}6&8\\4&2\end{matrix}\right).
$$

Show your work.  
先写出Strassen算法的伪代码

    STRASSEN-ALGORITHM-MATRIX-MULTIPLY(A,B)
    1   n = A.rows
    2   let C be a new n × n matrix
    3   if n==1
    4       c11 = a11 * b11
    5   else partition A,B,and C as in equations(4.9)
    6       create martix S1...S10
    7       P1 = STRASSEN-ALGORITHM-MATRIX-MULTIPLY(A11,S1)
    8       P2 = STRASSEN-ALGORITHM-MATRIX-MULTIPLY(S2,B22)
    9       P3 = STRASSEN-ALGORITHM-MATRIX-MULTIPLY(S3,B11)
    10      P4 = STRASSEN-ALGORITHM-MATRIX-MULTIPLY(A22,S4)
    11      P5 = STRASSEN-ALGORITHM-MATRIX-MULTIPLY(S5,S6)
    12      P6 = STRASSEN-ALGORITHM-MATRIX-MULTIPLY(S7,S8)
    13      P7 = STRASSEN-ALGORITHM-MATRIX-MULTIPLY(S9,S10)
    14      C11 = P5+P4-P2+P6
    15      C12 = P1+P2
    16      C21 = P3+P4
    17      C22 = P5+P1-P3-P7
    18  return C

java版[代码](https://github.com/MartinThoma/matrix-multiplication/blob/master/Java/MatrixMultiplication.java)
    
    public class StrassenE {

        public static double[][] strassenR(double[][] A, double[][] B) {
            int n = A.length;

            if (n == 1) {
        	    double[][] C = new double[1][1];
			    C[0][0]=A[0][0]*B[0][0];
			    return C;
            } else {
                // initializing the new sub-matrices
                int newSize = n / 2;
                double[][] a11 = new double[newSize][newSize];
                double[][] a12 = new double[newSize][newSize];
                double[][] a21 = new double[newSize][newSize];
                double[][] a22 = new double[newSize][newSize];

                double[][] b11 = new double[newSize][newSize];
                double[][] b12 = new double[newSize][newSize];
                double[][] b21 = new double[newSize][newSize];
                double[][] b22 = new double[newSize][newSize];

                double[][] aResult = new double[newSize][newSize];
                double[][] bResult = new double[newSize][newSize];

                // dividing the matrices in 4 sub-matrices:
                for (int i = 0; i < newSize; i++) {
                    for (int j = 0; j < newSize; j++) {
                        a11[i][j] = A[i][j]; // top left
                        a12[i][j] = A[i][j + newSize]; // top right
                        a21[i][j] = A[i + newSize][j]; // bottom left
                        a22[i][j] = A[i + newSize][j + newSize]; // bottom right

                        b11[i][j] = B[i][j]; // top left
                        b12[i][j] = B[i][j + newSize]; // top right
                        b21[i][j] = B[i + newSize][j]; // bottom left
                        b22[i][j] = B[i + newSize][j + newSize]; // bottom right
                    }
                }

                // Calculating p1 to p7:
                aResult = add(a11, a22);
                bResult = add(b11, b22);
                double[][] p1 = strassenR(aResult, bResult);
                // p1 = (a11+a22) * (b11+b22)

                aResult = add(a21, a22); // a21 + a22
                double[][] p2 = strassenR(aResult, b11); // p2 = (a21+a22) * (b11)

                bResult = subtract(b12, b22); // b12 - b22
                double[][] p3 = strassenR(a11, bResult);
                // p3 = (a11) * (b12 - b22)

                bResult = subtract(b21, b11); // b21 - b11
                double[][] p4 = strassenR(a22, bResult);
                // p4 = (a22) * (b21 - b11)

                aResult = add(a11, a12); // a11 + a12
                double[][] p5 = strassenR(aResult, b22);
                // p5 = (a11+a12) * (b22)

                aResult = subtract(a21, a11); // a21 - a11
                bResult = add(b11, b12); // b11 + b12
                double[][] p6 = strassenR(aResult, bResult);
                // p6 = (a21-a11) * (b11+b12)

                aResult = subtract(a12, a22); // a12 - a22
                bResult = add(b21, b22); // b21 + b22
                double[][] p7 = strassenR(aResult, bResult);
                // p7 = (a12-a22) * (b21+b22)

                // calculating c21, c21, c11 e c22:
                double[][] c12 = add(p3, p5); // c12 = p3 + p5
                double[][] c21 = add(p2, p4); // c21 = p2 + p4

                aResult = add(p1, p4); // p1 + p4
                bResult = add(aResult, p7); // p1 + p4 + p7
                double[][] c11 = subtract(bResult, p5);
                // c11 = p1 + p4 - p5 + p7

                aResult = add(p1, p3); // p1 + p3
                bResult = add(aResult, p6); // p1 + p3 + p6
                double[][] c22 = subtract(bResult, p2);
                // c22 = p1 + p3 - p2 + p6

                // Grouping the results obtained in a single matrix:
                double[][] C = new double[n][n];
                for (int i = 0; i < newSize; i++) {
                    for (int j = 0; j < newSize; j++) {
                        C[i][j] = c11[i][j];
                        C[i][j + newSize] = c12[i][j];
                        C[i + newSize][j] = c21[i][j];
                        C[i + newSize][j + newSize] = c22[i][j];
                    }
                }
                return C;
            }
	    }

	    private static double[][] add(double[][] A, double[][] B) {
    		return add(A, B, 1);
    	}
	
	    private static double[][] add(double[][] A, double[][] B, int sign){
    		int n = A.length;
	    	double[][] C = new double[n][n];
		    for (int i = 0; i < n; i++) {
			    for (int j = 0; j < n; j++) {
				    C[i][j] = A[i][j] + sign*B[i][j];
			    }
		    }
		    return C;
	    }

	    private static double[][] subtract(double[][] A, double[][] B) {
		    return add(A, B, -1);
	    }
    }

###4.2-2

Write pseudocode for Strassen's algorithm.
见上一题。

###4.2-3

How would you modify Strassen's algorithm to multiply $n\times n$ matrices in which $n$ is not an exact power of 2? Show that the resulting algorithm runs in time $\Theta(n^{\lg 7})$.

不够2的次方的部分补零即可，运行时间仍旧为$\Theta(n^{\lg7})$
    
###4.2-4

What is the largest $k$ such that if you can multiply 3$\times$3 matrices using $k$ multiplications (not assuming commutativity of multiplication), then you can multiply $n\times n$ matrices in time $o(n^{\lg 7})$? What would the running time of this algorithm be?

运行时间为$T(n)=kT(n/3)+\Theta(n^2)$，由题意以及主定理，要$T(n)=\Theta(n^{\log_3k})\le\Theta(n^{\lg 7})$，有$\log_3k\le\lg 7$，$k\le 3^{\lg 7}\approx 21.84$，即$k=21$

###4.2-5

V. Pan has discovered a way of multiplying 68$\times$68 matrices using 132,464 multiplications, a way of multiplying 70$\times$70 matrices using 143,640 multiplications, and a way of multiplying 72$\times$72 matrices using 155,424 multiplications. Which method yields the best asymptotic running time when used in a divide-and-conquer matrix-multiplication algorithm? How does it compare to Strassen’s algorithm?

针对$68\times68$矩阵的情况，运行时间为$T(n)=132646T(n/68)+\Theta(n^2)=\Theta(n^{\log_{68}132646})\approx n^{2.7954538844}$

针对$70\times70$矩阵的情况，运行时间为$T(n)=143640T(n/70)+\Theta(n^2)=\Theta(n^{\log_{70}143640})\approx n^{2.7951226897}$

针对$72\times72$矩阵的情况，运行时间为$T(n)=155424T(n/72)+\Theta(n^2)=\Theta(n^{\log_{72}155424})\approx n^{2.7951473911}$

毫无疑问，这三种途径都比Strassen的算法更快（$n^{\lg 7}\approx n^{2.8073549221}$）

###4.2-6

How quickly can you multiply a $kn\times n$ matrix by an $n\times kn$ matrix, using Strassen’s algorithm as a subroutine? Answer the same question with the order of the input matrices reversed.

假设$A$是$kn\times n$矩阵，$B$是$n\times kn$矩阵。则$A\cdot B$的时间代价为$T(n)=\Theta(k^2n^{\lg 7})$；$B\cdot A$的时间代价为$T(n)=\Theta(kn^{\lg 7})$。

###4.2-7

Show how to multiply the complex numbers $a+bi$ and $c+di$ using only three multiplications of real numbers. The algorithm should take $a, b, c$, and $d$ as input and produce the real component $ac-bd$ and the imaginary component $ad+bc$ separately.

只要计算$(a+b)\cdot(c+d)$、$ac$和$bd$就行，结果的虚数部分为$ad+bc=(a+b)(c+d)-ac-bd$。

> Written with [StackEdit](http://benweet.github.io/stackedit/).
