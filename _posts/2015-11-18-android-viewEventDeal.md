---

layout: post
title: View的事件分发机制
category: 技术
tags: android
description: 有关View事件的分发机制

---

> 有关View事件的分发机制

参考书籍:参考书籍： [android开发艺术](http://bornbeauty.github.io/2015/11/06/book-list-of-2015.html#Android开发艺术探索)

参考博客: [郭霖的专栏-Android事件分发机制完全解析，带你从源码的角度彻底理解(上)](http://blog.csdn.net/guolin_blog/article/details/9097463)


# 1.View点击事件的传递规则

首先,用户触摸屏幕的时候系统必须对事件做出相应相应.而这个事件就是产生一个MotionEvent然后按照一定的规则传递给每一个View去进行相应的处理.这就是我们所谓的时间分发了.点击事件的分发主要设计一下几个主要的方法:

	//用来就行事件的分发.如果有事件传递给当前的View,那么该View一定回去调用这个方法
    //返回值受当前View的onTouchEvent和下级View的dispatchTouchEvent的影响
    //返回值表示当前的事件时候已经被处理完成
	public boolean dispatchTouchEvent(MotionEvent e)
    
    //这个方法在上面那个方法中调用,用来判断是够药拦截这个事件
    //如果当前View拦截了某个事件 那么在同一个事件序列中就不会再次被调用
    //返回值表示是否拦截当前事件
    public boolean onInterceptTouchEvent(MotionEvent e)
    
    //也是在第一个方法中去调用 用来处理拦截下来的事件
    //返回值为真表示改事件已经被处理 否则 没有处理 在同一事件序列中
    //当View无法再次接收到事件
   	public boolean onTouchEvent(MotionEvent e)
    
可以用一段伪代码来表示一下三者的关系:

	public boolean dispatchTouchEvent(MotionEvent e) {
    	boolean consume = false;
        if (onInterceptTouchEvent(e)) {
        	consume = onTouchEvent(e);
        } else {
        	cnsume = childView.dispathcTouchEvent(e);
		}
    } 
    
从上面的代码中我们基本可以总结出这样的结论:
***对于一个根ViewGroup来说,当接收到一个MotionEvent的时候,首先会调用自己的dispatchTouchEvent,如果这个ViewGroup的onInterceptTouchEvent返回ture,表示他要拦截当前的事件,接着调用onTouchEvent来处理这个事件;反之,onInterceptTouchEvent返回false,则当前事件就会继续传递给他的子元素,接着子元素的dispatchTouchEvent方法就会被调用,如此往复,直至事件被处理.***


