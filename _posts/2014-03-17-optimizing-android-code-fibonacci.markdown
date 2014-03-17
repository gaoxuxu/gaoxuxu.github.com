---
author: gavin
comments: true
date: 2014-03-17 22:43:25
layout: post
title: Android代码优化之斐波那契数列
categories:
- 算法
- Java
- Android
---

刚到手《Android Performance》,其第一章斐波那契的例子很不错。  
以下代码的结果都在我的`LG LU6200`上运行得出，CPU型号为`高通MSM8660`，`CPU`频率为`1.5GHz双核`。

###1.以递归的方式求第n项斐波那契数

代码如下：

    public static long computeRecursively(int n){
        if(n > 1)
            return computeRecursively(n-1) + computeRecursively(n-2);
        return n;
    }

用这段代码求斐波那契数列的第`30`项，结果如下：

    result:832040
    token:450milliseconds

###2.利用恒等式优化代码
斐波那契数列有如下性质，我们可以利用一下：

$$ F_0+F_1+F_2+F3+\cdots+F_n=F_{n+2}-1 $$

代码如下：

    public static long computeRecursivelyWithLoop (int n){
        if (n > 1) {
            long result = 1;
            do {
                result += computeRecursivelyWithLoop(n-2);
                n--;
            } while (n > 1);
            return result;
        }
        return n;
    }

用这段代码求斐波那契数列的第`30`项，结果如下：

    result:832040
    token:297milliseconds

###3.从递归到迭代

递归算法的弊端是众所周知的，它会浪费大量的栈空间，并造成大量的方法调用，还有可能会导致栈溢出并导致应用崩溃。迭代版本的实现可以避免：

    public static long computeIteratively (int n){
        if (n > 1) {
            long a = 0, b = 1;
            do {
                long tmp = b;
                b += a;
                a = tmp;
            } while (--n > 1);
            return b;
        }
        return n;
    }

用这段代码求斐波那契数列的第`30`项，结果如下：

    result:832040
    token:0milliseconds

好吧，这段代码简直太快了，不到`1`毫秒。我们用这段代码求斐波那契数列的第`50000`项，结果如下：

    result:-806788626697678875
    token:4milliseconds

只花`4`毫秒，不过结果溢出了。

这段代码还可以将循环次数降为原来的一半：

    public static long computeIterativelyFaster (int n){
        if (n > 1) {
            long a, b = 1;
            n--;
            a = n & 1;
            n /= 2;
            while (n-- > 0) {
                a += b;
                b += a;
            }
            return b;
        }
        return n;
    }

用这段代码求斐波那契数列的第`50000`项，结果如下：

    result:-806788626697678875
    token:3milliseconds

###4.用BigInteger

上面的代码，求第`50000`项斐波那契数时溢出了，在Java中可以用`java.math.BigInteger`类修复这个问题。下面的代码是`computeIterativelyFaster`的`BigInteger`版本：

    public static BigInteger computeIterativelyFasterUsingBigInteger (int n){
		if (n > 1) {
			BigInteger a, b = BigInteger.ONE;
			n--;
			a = BigInteger.valueOf(n & 1);
			n /= 2;
			while (n-- > 0) {
				a = a.add(b);
				b = b.add(a);
			}
			return b;
		}
		return (n == 0) ? BigInteger.ZERO : BigInteger.ONE;
	}

运行时间为`914milliseconds`。

另外，斐波那契数列的项有如下性质：

$$ F_{2n-1}=F^2_n+F^2_{n-1} $$

$$ F_{2n}=(2F_{n-1}+F_n)\cdot F_n $$

根据以上恒等式修改`computeIterativelyFasterUsingBigInteger`：

    public static BigInteger computeRecursivelyFasterUsingBigInteger (int n){
        if (n > 1) {
            // not obvious at first – wouldn’t it be great 
            // to have a better comment here?
            int m = (n / 2) + (n & 1); 
            BigInteger fM = computeRecursivelyFasterUsingBigInteger(m);
            BigInteger fM_1 = computeRecursivelyFasterUsingBigInteger(m - 1);
            if ((n & 1) == 1) {
                // F(m)^2 + F(m-1)^2
                // three BigInteger objects created
                return fM.pow(2).add(fM_1.pow(2));
            } else {
                // (2*F(m-1) + F(m)) * F(m)
                // three BigInteger objects created
                return fM_1.shiftLeft(1).add(fM).multiply(fM); 
            }
        }
        // no BigInteger object created
        return (n == 0) ? BigInteger.ZERO : BigInteger.ONE; 
    }

用这个方法求第`50000`项斐波那契数耗时为`1550milliseconds`。

上面这个方法运行太慢了，我们还可以继续优化。其实在上面的方法中，对`BigInteger`的使用有一个不正确的地方。我们知道，斐波那契数列的第`92`项在基本数据类型`long`的取值范围内，我们可以利用这项性质优化代码：

    public static BigInteger computeRecursivelyFasterUsingBigIntegerAndPrimitive(int n){
		if (n > 92) {
			int m = (n / 2) + (n & 1);
			BigInteger fM = computeRecursivelyFasterUsingBigIntegerAndPrimitive(m);
			BigInteger fM_1 = computeRecursivelyFasterUsingBigIntegerAndPrimitive(m - 1);
			if ((n & 1) == 1) {
				return fM.pow(2).add(fM_1.pow(2));
			} else {
			    // shiftLeft(1) to multiply by 2
				return fM_1.shiftLeft(1).add(fM).multiply(fM); 
			}
		}
		return BigInteger.valueOf(computeIterativelyFaster(n));
	}

这次快多了，求斐波那契第`50000`项的运行时间为`59milliseconds`，是纯递归版本的近`30`倍。

与上面的方法思想差不多，还可以使用预计算结果优化代码，即：

    	static final int PRECOMPUTED_SIZE= 512;
	static BigInteger PRECOMPUTED[] = new BigInteger[PRECOMPUTED_SIZE];
	
	static {
		PRECOMPUTED[0] = BigInteger.ZERO;
		PRECOMPUTED[1] = BigInteger.ONE;
		for (int i = 2; i < PRECOMPUTED_SIZE; i++) {
			PRECOMPUTED[i] = PRECOMPUTED[i-1].add(PRECOMPUTED[i-2]);
		}
	}
	
	public static BigInteger computeRecursivelyFasterUsingBigIntegerAndTable(int n)	{
		if (n > PRECOMPUTED_SIZE - 1) {
			int m = (n / 2) + (n & 1);
			BigInteger fM = computeRecursivelyFasterUsingBigIntegerAndTable (m);
			BigInteger fM_1 = computeRecursivelyFasterUsingBigIntegerAndTable (m - 1);
			if ((n & 1) == 1) {
				return fM.pow(2).add(fM_1.pow(2));
			} else {
				return fM_1.shiftLeft(1).add(fM).multiply(fM);
			}
		}
		return PRECOMPUTED[n];
	}

这次求第`50000`项斐波那契数的运行时间为`12milliseconds`

> Written with [StackEdit](https://stackedit.io/).