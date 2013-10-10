---
author: gavin
comments: true
date: 2013-02-27 16:05:08
layout: post
slug: android%e4%b8%adview%e7%9a%84touch%e4%ba%8b%e4%bb%b6%e4%bc%a0%e9%80%92%e9%a1%ba%e5%ba%8f
title: Android中view的Touch事件传递顺序
wordpress_id: 12
categories:
- Android
- 源码分析
tags:
- Android
- onTouch事件
- view
---

先看一下ViewGroup的dispatchTouchEvent的关键源码： 
    
    public boolean dispatchTouchEvent(MotionEvent ev) {
        ...
        if (action == MotionEvent.ACTION_DOWN) {
    	...
           	if (disallowIntercept || !onInterceptTouchEvent(ev)) {
                final View[] children = mChildren;
                final int count = mChildrenCount;
                for (int i = count - 1; i >= 0; i--) {
                    final View child = children[i];
                    if ((child.mViewFlags & VISIBILITY_MASK) == VISIBLE
                            || child.getAnimation() != null) {
                        ...
                        if (child.dispatchTouchEvent(ev))  {
                            // Event handled, we have a target now.
                            mMotionTarget = child;
                            return true;
                        }
                    }
                }
            }
        }
        ...
        final View target = mMotionTarget;
        if (target == null) {
            ...
            return super.dispatchTouchEvent(ev);
        }
        if (!disallowIntercept && onInterceptTouchEvent(ev)) {
            ...
            if (!target.dispatchTouchEvent(ev)) {
                // target didn't handle ACTION_CANCEL. not much we can do
                // but they should have.
            }
            // clear the target
            mMotionTarget = null;
            // Don't dispatch this event to our own view, because we already
            // saw it when intercepting; we just want to give the following
            // event to the normal onTouchEvent().
            return true;
        }
        ...
        return target.dispatchTouchEvent(ev);
    }

根据以上代码可知，如果onInterceptTouchEvent方法返回true，会直接调用当前ViewGroup的onTouch方法，否则则会依次调用其内包含的子控件的dispatchTouchEvent方法。我写了一个测试用例，ViewGroup的onInterceptTouchEvent方法与所有View的onTouch方法都打了Log： 
    
    <?xml version="1.0" encoding="utf-8"?>
    <org.gavin.test.view.MyScrollView xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
    
        <org.gavin.test.view.MyLinearLayout
            android:layout_width="fill_parent"
    	android:layout_height="fill_parent"
    	android:orientation="vertical">
    	<org.gavin.test.view.MyImageView
    	    android:layout_width="wrap_content"
    	    android:layout_height="wrap_content"
    	    android:src="@drawable/ic_launcher">
            </org.gavin.test.view.MyImageView>
        </org.gavin.test.view.MyLinearLayout>
    
    </org.gavin.test.view.MyScrollView>

Log信息： 

	I/MyScrollView(26001): onInterceptTouchEvent:MotionEvent{...}  
	I/MyLinearLayout(26001): onInterceptTouchEvent:MotionEvent{...}  
	I/MyImageView(26001): onTouchEvent:MotionEvent{...}  
	I/MyLinearLayout(26001): onTouchEvent:MotionEvent{...}  
	I/MyScrollView(26001): onTouchEvent:MotionEvent{...}

如果MyLinearLayout的onInterceptTouchEvent方法返回true： 

	11-04 14:05:08.389: I/MyScrollView(26297): onInterceptTouchEvent:MotionEvent{...}  
	11-04 14:05:08.389: I/MyLinearLayout(26297): onInterceptTouchEvent:MotionEvent{...}  
	11-04 14:05:08.397: I/MyLinearLayout(26297): onTouchEvent:MotionEvent{...}  
	11-04 14:05:08.397: I/MyScrollView(26297): onTouchEvent:MotionEvent{...}

OK，再看这段代码： 

    if (child.dispatchTouchEvent(ev))  {
        // Event handled, we have a target now.
        mMotionTarget = child;
        return true;
    }

从这段代码可以看出，如果某个子控件的dispatchTouchEvent方法返回true，则中断以后的消息传递。并在下一个非ACTION_DOWN事件直接调用此子控件的dispatchTouchEvent方法。一个View的dispatchTouchEvent返回true，主要可能是onTouch方法返回true。如果我们把MyImageView的onTouchEvent返回值改为true： 

	I/MyScrollView(26393): onInterceptTouchEvent:MotionEvent{...}  
	I/MyLinearLayout(26393): onInterceptTouchEvent:MotionEvent{...}  
	I/MyImageView(26393): onTouchEvent:MotionEvent{...}
