---
author: gavin
comments: true
date: 2013-04-17 22:34:41
layout: post
slug: go-%e6%96%90%e6%b3%a2%e9%82%a3%e5%a5%91%e6%95%b0%e5%88%97
title: Go——斐波那契数列
wordpress_id: 150
categories:
- Go
tags:
- fibonacci
- Go
---

由于Go语言支持闭包，所以实现的斐波那契数列算法非常简洁：

        package main
    
        // fibonacci is a function that returns
        // a function that returns an int.
        func fibonacci() func() int {
    	a, b := 0, 1
    	return func() int {
    	    a, b = b, a+b
    	    return a
    	}
        }
    
        func main() {
    	f := fibonacci()
    	for i := 0; i < 10; i++ {
                println(f())
            }
        }

下面附上Java版，这里维护了一个数组：
    
        package algs;
    
        public class Fibonacci {
    	private static long[] FN = new long[100];
    	
    	public static void main(String[] args) {
    	    for(int i = 0; i < 10; i++)	{
    		System.out.println(F(i));
    	    }	
    	}
    	
    	private static long F(int N){
                if(N == 0) return 0;
    	    if(N == 1) {
    	    	FN[N] = 1;
    		return 1;
    	    }
    	    FN[N] = FN[N-1] + FN[N-2];
    	    return FN[N];
    	}
        }
