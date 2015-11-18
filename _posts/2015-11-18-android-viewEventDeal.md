---

layout: post
title: View的事件分发机制
category: 技术
tags: android
description: 有关View事件的分发机制

---

> 有关View事件的分发机制

# 1.View点击事件的传递规则

首先,用户触摸屏幕的时候系统必须对事件做出相应相应.而这个事件就是产生一个MotionEvent然后按照一定的规则传递给每一个View去进行相应的处理.这就是我们所谓的时间分发了.点击事件的分发主要设计一下几个主要的方法:

	//用来就行事件的分发.如果有事件传递给当前的View,那么该View一定回去调用这个方法
    //返回值受当前View的onTouchEvent和下级View的dispatchTouchEvent的影响
    //返回值表示当前的事件时候已经被处理完成
	public boolean dispatchTouchEvent(MotionEvent e)
    
    //这个方法在上面那个方法中调用,用来判断是够药拦截这个事件
    //如果当前View拦截了某个事件 那么在同一个事件序列中就不会再次被调用
    //返回值表示是否拦截当前事件
    public boolean onInterTouchEvent(MotionEvent e)
    
    //也是在第一个方法中去调用 用来处理拦截下来的事件
    //返回值为真表示改事件已经被处理 否则 没有处理 在同一事件序列中
    //当View无法再次接收到事件
   	public boolean onTouchEvent(MotionEvent e)
    
    
    