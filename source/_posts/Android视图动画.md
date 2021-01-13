---
title: Android视图动画
date: 2018-11-04 21:13:16
tags:
- android
---
### 1.1补间动画

共分为四种

####1.改变View的透明度 AlphaAnimation

   因为只是改变View的透明度,所以它的属性并不是很多只有fromAlpha和toAlpha
   fromAlpha:初始的alpha的值,
   toAlpha:结束时alpha的值


* 代码实现:


    AlphaAnimation alphaAnimation =new AlphaAnimation(float fromAlpha, float toAlpha)

* xml实现:


    <?xml version="1.0" encoding="utf-8"?>
    <alpha xmlns:android="http://schemas.android.com/apk/res/android"
        android:toAlpha="1.0"
        android:fromAlpha="0.1"/>

下面三个因为会改变View的位置或者大小或者形状,因此需要确定一个中心去确定如何执行动画

#####如何确定中心：

 ..XType  , ..YType :对于动画执行的XY坐标的位置的定位标准，默认值都是Animation.ABSOLUTE

..XValue和..YValue则是对于两者的实现

  有三个值可以使用:

- Animation.ABSOLUTE :坐标的绝对值,原点是View的左上角 ,X、Y的数值代表实际多少像素
- Animation.RELATIVE_TO_SELF:坐标相对于自己来说,X、Y的数值代表自己的width,height的倍数
- Animation.RELATIVE_TO_PARENT.坐标相对于父View来说,X、Y的数值代表父View的width,height的倍数

####2.scale:改变View的大小:

   - float fromX:初始X的大小
   - float toX:结束时X的大小
   - float fromY:初始Y的大小
   - float toY:结束时Y的大小

  数值均为浮点值，表示XY的数倍关系

* 代码实现:


```java

/*
ScaleAnimation默认的pivotXType和pivotYType为Absolute
*/
ScaleAnimation scaleAnimation=new ScaleAnimation(float fromX, float toX, float fromY, float toY)
ScaleAnimation scaleAnimation=new ScaleAnimation(float fromX, float toX, float fromY, float toY,float pivotX, float pivotY)
ScaleAnimation scaleAnimation=new ScaleAnimation(float fromX, float toX, float fromY, float toY,int pivotXType, float pivotXValue, int pivotYType, float pivotYValue)
  
  
```

* xml实现:


```xml
<?xml version="1.0" encoding="utf-8"?>
<!--pivotY和pivotX为数值时代表 Animation.ABSOLUTE -->
<!--为百分比时代表RELATIVE_TO_SELF -->
<!--百分比后面加一个p代表RELATIVE_TO_PARENT-->
<scale xmlns:android="http://schemas.android.com/apk/res/android"
    android:pivotY=""
    android:pivotX=""
    android:fromXScale=""
    android:fromYScale=""
    android:toXScale=""
    android:toYScale="" />
```

####3.改变View的位置(平移操作)

 fromXValue,  toXValue, fromYValue,  toYValue:XY方向上平移的距离

* 代码实现:


```java
TranslateAnimation animation=new TranslateAnimation(float fromXDelta, float toXDelta, float fromYDelta, float toYDelta)
TranslateAnimation animation=new TranslateAnimation(int fromXType, float fromXValue, int toXType, float toXValue,int fromYType, float fromYValue, int toYType, float toYValue)
```

* xml实现



```xml
<?xml version="1.0" encoding="utf-8"?>
<!--fromXDelta,fromYDelta,toXDelta,toYDelta为数值时代表-->
<!--为百分比时代表RELATIVE_TO_SELF -->
<!--百分比后面加一个p代表RELATIVE_TO_PARENT-->
<translate xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromXDelta="100"
    android:fromYDelta="100"
    android:toXDelta="100"
    android:toYDelta="100"/>
```

####4.旋转View

float fromDegrees:起始角度
float toDegrees:结束角度

* 代码实现:


```java
RotateAnimation rotateAnimation=new RotateAnimation(float fromDegrees, float toDegrees)
RotateAnimation rotateAnimation=new RotateAnimation(float fromDegrees, float toDegrees, float pivotX, float pivotY)
RotateAnimation rotateAnimation=new RotateAnimation(float fromDegrees, float toDegrees, int pivotXType, float pivotXValue,int pivotYType, float pivotYValue)
```
* xml实现:



```xml
 <?xml version="1.0" encoding="utf-8"?>
 <!--pivotY和pivotX为数值时代表 Animation.ABSOLUTE -->
 <!--为百分比时代表RELATIVE_TO_SELF -->
 <!--百分比后面加一个p代表RELATIVE_TO_PARENT-->
 <rotate xmlns:android="http://schemas.android.com/apk/res/android"
     android:pivotX=""
     android:pivotY=""
     android:fromDegrees=""
     android:toDegrees=""/>
```

###这几种动画可以同时使用,用AnimationSet

 * 代码实现:


```java
AnimationSet set=new AnimationSet(boolean shareInterpolator)//shareInterpolator代表是否动画使用同一个插值器
void addAnimation(Animation a) 来增加动画个数
```

* xml实现:


```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <rotate/>
    <scale/>
    <translate/>
    <alpha/>
</set>
```

 Animation有一些通用的方法:

* setDuration(long durationMillis)  //设置动画的时间 毫秒为单位

* setRepeatCount(int repeatCount)//设置动画的重复次数 如果设置成-1或者小于-1(Animation.INFINITE)代表无限循环，0次代表只播放一次，不重复

* setRepeatMode(int repeatMode)//设置动画重复的模式 1(Animation.RESTART)代表回放 2(Animation.REVERSE)代表倒叙回放

* setFillAfter(boolean fillAfter) //设置动画是否保存结束后的状态

* setFillBefore(boolean fillBefore)//设置动画是否保存开始时的状态

* setFillEnabled(boolean fillEnabled)// 如果为true，那么会应用setFillBefore的值否则就忽视setFillBefore的值

* setAnimationListener(AnimationListener)     //设置动画监听
其中AnimationListener是一个接口:


```java
 public static interface AnimationListener {
                //动画开始时调用
                void onAnimationStart(Animation animation);
                //动画结束时调用     
                void onAnimationEnd(Animation animation);
                //动画重复时调用
                void onAnimationRepeat(Animation animation);
            }
```


xml中通用属性:

```java
  android:duration="1000"   
  android:repeatCount="-1"
  android:repeatMode="["reverse"  "restart"]"
  android:fillBefore="["true"  "false"]"
  android:fillAfter="["true"  "false"]"
  android:fillEnabled="["true"  "false"]"
```

### 1.2 逐帧动画

顾名思义就是动画一帧一帧的播放,那么就需要每一帧的图片资源

* 代码实现:


```java
  AnimationDrawable drawable=new AnimationDrawable();
        for (int i = 0; i < length; i++) {
        Drawable drawable1=getResources().getDrawable(R.drawable.id);
         drawable.addFrame(drawable1,60);
        }
   drawable.setOneShot(false);
```

* void addFrame(Drawable frame, int duration) //添加帧数,并且设置帧数的持续时间
* void setOneShot(boolean oneShot)//true表示重复播放 false表示只播放一次

* xml实现:


```xml
<?xml version="1.0" encoding="utf-8"?>
<animation-list
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="["true"  "false"]">
    <!--每一帧都要设置一个item-->
    <item android:drawable="" android:duration=""/>
</animation-list>
```
