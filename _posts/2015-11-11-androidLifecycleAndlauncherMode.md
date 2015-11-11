---
layout: post
title: android生命周期和启动模式深入理解
category: 技术
tags: android
description: double eleven happy to me
---

>Activity的生命周期和启动模式的深入理解

参考书籍： [android开发艺术](http://book.douban.com/subject/26599538/)
参考博客： [楚云之南-android的task任务栈](http://www.cnblogs.com/CSU-PL/p/3794280.html)


## 一.Activity的生命周期

这个问题大家应该是比较清楚的，这个也比较简单，很好理解。首先，我们先来看一张Activity的生命周期图。
![activity生命周期](http://7xjtan.com1.z0.glb.clouddn.com/activity_lifecycle.png)

挨个看下每一个生命周期(准确的说这只是每一个生命周期开始的时候的回调函数而已)：

##### 1.onCreate()

表示activity正在被创建，这里我们主要是做一些初始化的工作。比如调用`setContentView()`方法去设置布局文件，初始化activity所需要的数据等等。
***要注意***，这个时候并不能保证activity已经完全运行起来了，我们要是想动态加载一些布局，测量view的大小等等可能不会得到想要的结果。google其实给我们提供了另外一个回调方法`onPostCreate()`,这个方法是在activity完成加载完成呈现给用户后回调的方法。看一下官方文档怎么说的

>Since: API Level 1
>Called when activity start-up is complete (after onStart() and onRestoreInstanceState(Bundle) have been called). Applications will generally not implement this method; it is intended for system classes to do final initialization after application code has run.
Derived classes must call through to the super class's implementation of this method. If they do not, an exception will be thrown.

稍微翻译一下：
onPostCreate()将会在activity加载完成后被调用(在onCreate()和onRestoreInstanceState(Bundle)方法已经被调用后)。应用一般不用复写这个方法，这个方法最初设计的目的是在应用运行后做一些初始化的工作。
派生类中复写这个方法的时候必须首先调用父类的onPostCreate()方法，不然会有异常抛出。

##### 2.onRestart()

表示activity正在重新启动。一般是activity从不可见状态转换到可见状态的时候会被调用。比如用户按下home键的时候activity被切换到后台，onPause()和onStop()被调用，然后用户又重新打开了这个应用，就会调用onRestart()方法。

##### 3.onStart()

表示activity正在被启动，这个时候activity已经可见了，但是我们还不能看到，不能直接与用户进行交互操作。可以理解为已经显示出来了，但是我们还看不到。

##### 4.onResume()

表示activity已经可见，并且已经在前台开始活动了；

##### 5.onPause()

表示activity被暂停，android开发艺探索作者认为：onPause()调用后onstop()方法就会紧接着被调用，除非快速的重新切入这个activity不然很难重现onPause()->onResume()这一过程，我认为可能在activity被部分遮盖的时候会出现这种情况，根据官方文档：

>The foreground lifetime of an activity happens between a call to onResume() until a corresponding call to onPause(). During this time the activity is in front of all other activities and interacting with the user. An activity can frequently go between the resumed and paused states -- for example when the device goes to sleep, when an activity result is delivered, when a new intent is delivered -- so the code in these methods should be fairly lightweight.

这里暂且一放，改日代码测试；

***这里需要注意的是：不要在onPause()中一些重量级的回收工作 根据官方文档所说，只有在onPause()结束后，下一个activity才会重新开始***

>Implementations of this method must be very quick because the next activity will not be resumed until this method returns.

##### 6.onStop()

表示activity即将停止，可以稍微做一些重量级的回收工作。

##### 7.onDestory()

表示activity即将被销毁，这是最后一个回调，我们可以做一些回收和资源释放的工作。





