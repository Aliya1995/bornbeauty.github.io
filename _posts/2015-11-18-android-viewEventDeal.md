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

***对于一个根ViewGroup来说,当接收到一个MotionEvent的时候:***

- 调用dispatchTouchEvent方法
	- 调用onInterceptTouchEvent方法
		- 返回值为true,则表示拦截当前事件,调用onTouchEvent来处理这个事件 
		- 返回值为false,则当前事件将会被传递给childView,childView继续调用dispatchTouchEvent方法

如此往复,直至事件被处理.

***当一个View需要处理一个事件的时候***

- 如果该View设置了onTouchListener,则会调用onTouch方法
	- 如果onTouch方法返回false,则去调用onTouchEvent
		- 如果设置了onClickListener,那么在onTouchEvent方法中将会调用onClick方法
	- 反之,onTouchEvent则不会被调用

当一个事件产生的时候,它的传递过程遵循这样的过程:Activity->Windows->View;事件总是先传递给Activity,Activity在传递给Windows,Windows在传递给View;如果View将事件处理了,则该事件相应就结束了.否则,事件将一级一级的继续返回,最终会传递给Activity的onTouchEvent处理.

	@Override
    public boolean onTouchEvent(MotionEvent event) {
        Toast.makeText(MainActivity.this, event.getAction() + "我是Activity", Toast.LENGTH_SHORT).show();
        return super.onTouchEvent(event);
    }

当你没有给任何控件设置相应事件的时候(也就是都会返回false),那么你就会看到Activity的onTouchEvent被调用了.

在[android开发艺术](http://bornbeauty.github.io/2015/11/06/book-list-of-2015.html#Android开发艺术探索)这本书中提到了几个结论:

>1.同一个事件序列是指手指接触屏幕那一刻起,到手指离开屏幕那一刻结束,在这个过程中所有产生的事件都属于这一个事件序列.包括一个ACTION_DOWN,一个ACTION_UP和n个ACTION_MOVE;

>2.某一个View一旦决定拦截事件,那么这一事件序列都只能由它来处理.

这个结论认真想了一下,似乎有点问题;假如这个View我设置了onTouchListener,但是我怡然返回false,这个事件序列仍然会传递给父View,当然了,这个View也只能接收到一个ACTION_DOWN事件,ACTION_UP和ACTION_MOVE不会接收到.假如这个View同时还设置了onClickListener,onTouchEvent返回false的时候事件会交给onTouchEvent处理这个事件,不会在交给父View处理了.这个问题还是需要结合源码来看一下;

>3.某个View一旦开始处理一个事件,如果它不消耗ACTION_DOWN事件,那么同一时间序列也不会交给他来处理了.
>4.如果View不消耗ACTION_DWON以外的事件,那么这个点击事件就会消失,不会在交还给父View处理.最后会交回activity处理.
>5.ViewGroup默认不拦截任何事件,他的onInterceptTouch方法默认返回false;View没有onInterceptTouch方法,收到事件他的onTouchEvent事件就会被调用.


