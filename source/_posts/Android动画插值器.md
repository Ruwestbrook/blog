---
title: Android动画插值器
date: 2018-11-13 14:30:03
tags:
- android
---
animate都能设置Interpolator来控制动画的变化率

下面图片展示了系统内置的九种插值器的对于同一个动画的影响效果

![内置插值器演示](http://image.honglingqi.cn/blogImages/1113/1542092680973.gif)


从下到下依次是:
* AccelerateInterpolator:加速播放
* DecelerateInterpolator:减速播放
* LinearInterpolator:匀速播放
* OvershootInterpolator:快速完成动画，超出再回到结束样式
* AccelerateDecelerateInterpolator:先加速再减速
* AnticipateInterpolator:先退后再加速前进
* AnticipateOvershootInterpolator:先退后再加速前进，超出终点后再回终点
* BounceInterpolator:最后阶段弹球效果
* CycleInterpolator:周期运动(构造函数有参数float cycles 表示次数)

也可以自己实现插值器:
其实Interpolator是一个接口,如果需自定义Interpolator的话,只需要实现重写Interpolator的方法就可以了


    @Override
            public float getInterpolation(float input) {
                return 0;
            }
getInterpolation函数的float input代表动画的完成进度,0代表刚开始,1表示已结束,
试着写了一个:


     ScaleAnimation animation=new ScaleAnimation(1,3,1,3,
                            1,0.5f,1,0.5f);
                    animation.setDuration(1500);
                    animation.setFillAfter(true);
                    animation.setRepeatCount(0);
                    animation.setInterpolator(new Interpolator() {
                        @Override
                        public float getInterpolation(float input) {
                            return input-1;
                        }
                    });
                    view.startAnimation(animation);
缩放动画,从一倍放大到三倍,设置了自定义的Interpolator,返回值是input-1,结果出现了奇怪的东西:

并没有像预想那样放大,而是先缩小到0再放大跟原来一样,Interpolator到底干了什么?
仔细想了很久之后发现了变化的规律:因为这是变大的操作,所以倍数应该是1+(3-1)*(input-1),从一倍到三倍,
所以在刚开始的时候input=0,其值变化规律是-1->0->1
* 那么负数怎么计算呢?

我的理解负数代表了和我们定义动画相反的顺序,在这个例子中我们需要的是变大,但是动画前段的变化是-1到0,那么就是从一倍变小到0倍
,也就是消失不见,接着从0倍到一倍,逐渐变大

为此从定义另外一个变小的动画来验证自己的猜想


    ScaleAnimation animation=new ScaleAnimation(3,1,3,1,
                            1,0.5f,1,0.5f);
                    animation.setDuration(1500);
                    animation.setFillAfter(true);
                    animation.setRepeatCount(0);
                    animation.setInterpolator(new Interpolator() {
                        @Override
                        public float getInterpolation(float input) {
                            return input-1;
                        }
                    });
                    view.startAnimation(animation);
这是变小的操作,那么结合这个Interpolator,其值的变化应该是3-(3-1)*(input-1),从五倍变小到三倍


![验证猜想](http://image.honglingqi.cn/blogImages/1113/1542099170983.gif)

需要注意的是这里并没有改变Interpolator的速率,这里只是改变了变化规律,如果需要改变速率,那么需要更加复杂的数学函数去实现(好像全忘记了)
