---
title: View事件分发
date: 2019-07-26 17:44:34
tags:
---

事件的传递分为三类：

1.分发事件（dispatchTouchEvent）

2.拦截事件（onInterceptTouchEvent）:只存在于ViewGroup

3.消费事件  (onTouchEvent)



传递的对象：Activity,viewGoup,View





注意事项:

* 只有在OnTouchEvent返回true的时候，onClick方法才会被执行：因为一次完整的点击事件需要经历DOWN和UP事件，需要接受后续的事件

* setOnClickListener方法会改变OnTouchEvent的值，将其返回true
* 如果View和ViewGroup同时设置了OnClick事件，那么得到相应的会是View，因为在ViewGroup的onInterceptTouchEvent中判断了View如果有点击事件，那么该方法返回false来继续传递事件，如果要将ViewGroup的点击事件生效的话，需要重写onInterceptTouchEvent，返回值为true来拦截事件的传递
* setOnTouchListener中onTouch方法优先级在onTouchEvent之前，如果方法返回true那么会拦截事件