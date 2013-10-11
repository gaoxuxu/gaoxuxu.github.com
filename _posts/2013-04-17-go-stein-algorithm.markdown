---
author: gavin
comments: true
date: 2013-04-17 23:28:07
layout: post
slug: go-stein%e7%ae%97%e6%b3%95%e6%b1%82%e6%9c%80%e5%a4%a7%e5%85%ac%e7%ba%a6%e6%95%b0
title: Go——Stein算法求最大公约数
wordpress_id: 152
categories:
- Go
tags:
- gcd
- Go
- stein
---
 
        package main
    
        func gcd(p, q int) int {
    	if p < q {
    	    t := p
    	    p = q
    	    q = t
    	}
    	if q == 0 {
    	    return p
    	}
    	if p&1 == 0 && q&1 == 0 {
    	    return gcd(p>>1, q>>1) << 1
    	}
    	if p&1 == 0 {
    	    return gcd(p>>1, q)
    	}
    	if q&1 == 0 {
    	    return gcd(p, q>>1)
    	}
    	return gcd((p+q)>>1, (p-q)>>1)
        }
    
        func main() {
    	p, q := 3, 11
    	println(gcd(p, q))
        }
