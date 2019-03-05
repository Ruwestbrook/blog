---
title: Android下拉刷新
date: 2018-10-30 21:35:38
tags:
- android
---
下拉刷新永远是一个头痛的问题,本来是有官方的组件直接实现就好了,但是发现需求是需要实现一些动画的,那也只能自己设计了

最初的想法是百度上找相似的然后稍微看懂改一下,发现最多的就是使用RecyclerView来做文章添加头部和尾部,但是实际使用下来来看,有以下缺点:

1.添加之后使用adapter并不奏效,要使用RecyclerView.getAdapter()才可以,因为这里使用了装饰者模式,所以adapter并不是RecyclerView直接的适配器

2.再给Recycler添加分割线的时候不好处理(也可能是我水平有限不知道怎么解决)

所以就换了一种方式,直接自定义RefreshView继承LinearLayout,然后再这里面添加RecyclerView,当然,我还是想的太简单了


最先思考的问题,如何判断RecyclerView在最顶部,只有其在最顶部的时候才能下拉刷新,总不能在其浏览到中时,往上滑动结果是刷新了列表

其次RefreshView内部的OnTouchEvent事件无效(黑人问号脸)


其实可以靠一个方法解决,RefreshView获取RecyclerView,然后给它添加onTouch事件,然后在里面也判断他是否头部在最前面就好了


关于RefreshView内部的OnTouchEvent事件无效的问题:应该是onInterceptTouchEvent()拦截了传递给了子控件RecyclerView,不然RecyclerView滑动就无效了(应该好好看看这里了,以为自己懂了,其实一点不懂)




问题一:滑动时冲突问题:当RefreshView在最顶部时,此时下拉刷新,但是同时RefreshView也会跟着滑动,想了很久,看了很久的代码,才发现,这个时候要把滑动事件拦截!! 也就是onTouch方法中返回true表示滑动事件已经被消费了,被自己蠢哭了
