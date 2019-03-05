---
title: Android属性动画
date: 2018-11-14 10:11:14
tags:
- android
---

Android动画一共分为两种,视图动画和属性动画,之前但是视图动画有局限性,他只能对View进行移动、缩放、旋转和淡入淡出操作,移动之后也只是
改变了View的视图位置,并没有影响到它的属性,

平移例子就很好的说明了这一点:View移动到了另一个位置,但是点击事件还是在原来的地方才能响应


基于这些缺点,Android在Android3.0中推出了属性动画

## ValueAnimator

在我的理解上来说,这可能并不算是一个真正意义上的动画,他只是随着随着时间的进行来改变他的值,然后用这个值去改变某一些属性参数,
虽然他有一个继承了setTarget()的方法,但是好像用不到?
还是接上面的例子,我们想要实现的是不管View移动到哪里,他总是能响应点击事件,而不是在最初的位置才能响应事件

首先创建ValueAnimator实例:


    ValueAnimator valueAnimator=ValueAnimator.ofInt(200,300);
    valueAnimator.setDuration(1500);
这是最简单的构造ValueAnimator之一,代表一段数值的变化,从200-300,用时1500毫秒,变化的类型永远是int类型的
参数代表的是变化区间,是一个可变参数,至少需要一个参数


      valueAnimator.setTarget(view);
      valueAnimator.start();
然后你可能会这样用,但是运行了之后好像并不可以,因为ValueAnimator只是改变自己的值,并不会去控制其他的属性,所以我们在这个ValueAnimator
设置一个监听器,监听它的值的变化:


    valueAnimator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
                        @Override
                        public void onAnimationUpdate(ValueAnimator animation) {
                            int value=(int)animation.getAnimatedValue();
                            Log.d(TAG, "onAnimationUpdate: "+value);
                        }
                    });
可以看到值一直在发生变化,拿到了这个之后,我们就可以开始模拟之前的平移动画了:用layout()来改变view的位置


        //在onAnimationUpdate()添加layout()方法
        final int x=view.getLeft();(这之前就获取到的)
        view.layout(x+value,view.getTop(),x+value+view.getWidth(),view.getBottom());
 这样也实现了View的移动,并且也实现了平移操作


 除了ValueAnimator.ofInt()来构造ValueAnimator之外,还可以使用ValueAnimator.ofFloat();ValueAnimator.ofArgb(),是比较常用的,
 我们也可以使用 ValueAnimator.ofObject()来自定义ValueAnimator返回的类型,其实在onAnimationUpdate中animation.getAnimatedValue()
 返回的就是object类型的变量,这个时候我们就要自定义一个返回类型了 通过实现TypeEvaluator<T>接口来自定义返回类型

    public class CharEvaluator implements TypeEvaluator<Character> {
                @Override
                public Character evaluate(float fraction, Character startValue, Character endValue) {
                    return (char) (endValue-(1-fraction)*(endValue-startValue));
                }
上面就是一个自定义的TypeEvaluator返回的是char类型的字符float fraction代表了动画完成的进度,相当于插值器中的float input

使用CharEvaluate

    ValueAnimator animator=ObjectAnimator.ofObject(new CharEvaluator(), 'A', 'Z');


## ObjectAnimator

ValueAnimator并不能直接的改变UI,而是在监听函数中动态的去设置相应的值,UI状态才能发生改变,但是只是改变View的某一个特定的值的话,采用ObjectAnimator无疑是更好的选择
ObjectAnimator继承自ValueAnimator,使用的方式也比ValueAnimator更加的简单:

构造函数:

        ObjectAnimator animator=ObjectAnimator ofInt(Object target, String propertyName, int... values)
        target:需要设置动画的对象
        propertyName:需要改变的属性名称
        values:改变的值

ObjectAnimator的具体使用方法为:寻找target的setPropertyName()方法,然后将values设置进去
例如我想把一个View的透明度从0变到1,使用ObjectAnimator:

        ObjectAnimator animator=ObjectAnimator ofInt(view,"alpha", 0,1);

然后再设置其他的属性就可以执行了

只要设置的propertyName可以在target中找到setPropertyName()的方法的话,那么ObjectAnimator动画就可以执行

        /**
        动态的改变mImageView的top值,相当于改变其高度
        **/
        ObjectAnimator animator=ObjectAnimator.ofInt(mImageView,"top",500,200,500,400,900);



*tips:如果当values只有一个值的时候,会调用getPropertyName()来获取起始值,values作为结束值


## AnimatorSet

当有多个动画需要同时使用的时候,AnimatorSet无疑是一个很好的选择:


            ObjectAnimator animator1=ObjectAnimator ofInt(view,"alpha", 0,1);
           ObjectAnimator animator2=ObjectAnimator.ofInt(mImageView,"top",500,200,500,400,900);
           AnimatorSet animatorSet = new AnimatorSet();
           //两个动画同时播放:
           animatorSet.playTogether(animator1,animator2);
           //依次播放
           set.playSequentially(animator1,animator2);
*tips:
1. 每个动画可以单独设置target或者duration等属性,同时AnimatorSet也可以设置这些属性,如果AnimatorSet设置了之后单个动画的属性的就会失效
1. 依次播放的动画会在这一个动画结束后才开始下一个,如果是无限播放,那么下一个将轮不到播放

##  AnimatorSet.Builder

AnimatorSet不能灵活控制单个动画的执行顺序 只能一起或者一次执行
 AnimatorSet.Builder 随意可以使用  setStartDelay针对单个动画  after之前播放 with 一起播放 before在之后播放
 使用AnimatorSet.play()可以获取到AnimatorSet.Builder实例

                   AnimatorSet set=new AnimatorSet();
                 AnimatorSet.Builder builder=set.play(animator1).with(animator2).after(animator3);
                animator1.setStartDelay(1500);
                animator2.setStartDelay(1500);
                set.setStartDelay(1500);
                set.play(animator1).with(animator2);
               set.setDuration(1500);


AnimatorSet用xml表示

        <set xmlns:android="http://schemas.android.com/apk/res/android"
            android:ordering="together">
            <objectAnimator
                android:valueFrom="0"
                android:valueTo="1"
                android:propertyName="alpha"/>
            <objectAnimator
                android:valueFrom="0"
                android:valueTo="200"
                android:propertyName="top"/>
        </set>
        android:orderin=sequentially || together 顺序播放或者一起播放
