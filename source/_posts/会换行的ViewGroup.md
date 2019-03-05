---
title: 会换行的ViewGroup
date: 2018-09-29 22:27:58
tags:
- android
---

碰到一个需求,就是在不知道有多少个标签的情况下全部显示它们,如果放不下就需要换行,但是Horizontal的linearLayout不会自动换行,所以这里只能重写ViewGroup来实现这一个需求.


首先需要确定的由于是超出宽度自动换行,那么它的高度肯定是不确定的(在一般情况下),即我们需要计算整个的高度,所以不仅要重写onLayout,还要重写omMeasure,一开始尝试继承ViewGroup,但是因为计算位置需要使用到margin值,而ViewGroup.LayoutParams没有margin值,所以这里选择继承了LinearLayout


首先需要计算高度

```
 @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        int measureWidth=MeasureSpec.getSize(widthMeasureSpec);
        int count=getChildCount();
        int width=0;
        int height=0;
        int lineHeight=0;
        for (int i = 0; i < count; i++) {
            View child=getChildAt(i);
            measureChild(child,widthMeasureSpec,heightMeasureSpec);
            LinearLayout.LayoutParams layoutParams= (LayoutParams) child.getLayoutParams();
            //获取子View的高度和宽度 需要加上Margin值
            int childWidth=child.getMeasuredWidth()+layoutParams.leftMargin+layoutParams.rightMargin;
            int childHeight=child.getMeasuredHeight()+layoutParams.topMargin+layoutParams.bottomMargin;
            if(childWidth+width>measureWidth){
                //如果当前View加上之前累加的View的宽度大于父元素的宽度,那么需要换行
                //而且下一行重新开始计算需要将width置为0,然后父View加上这一行的高度
                height+=lineHeight;
                lineHeight=childHeight;
                width=childWidth;
            }else {
                //若没有超过父View的宽度,那么需要将宽度累加,然后该行的高度取最高的子
                //View的高度
                width+=childWidth;
                lineHeight=Math.max(lineHeight,childHeight);
            }
            //特殊处理
            if(i==count-1)
                height+=lineHeight;
        }
        setMeasuredDimension(measureWidth,height);
    }
```


首先需要遍历计算子元素的高度,既然是自动换行,那么就表示当累加的宽度大于或等于父元素的宽度时,就需要在下一行重新开始,而这一行的高度取该行高度最大的子元素的值作为该行的高度,一次类推相加,最后一个View需要单独处理因为如果加上最后一个View,还没有超过父元素的宽度,那么就不会执行
```
    width+=childWidth;
    lineHeight=Math.max(lineHeight,childHeight);
```
这两行代码,如果刚好超出,那么更需要加上这个View的高度了,否则刚好将最后一个View隐藏了,最后
```
setMeasuredDimension(measureWidth,height);
```
重新设置宽高(宽度不变,但是高度会随之改变)



![没有变化](http://image.honglingqi.cn/blogImages/1001/two.png)


![高度改变](http://image.honglingqi.cn/blogImages/1001/three.png)


很明显的看得出来高度发生了变化(背景颜色),但是布局还是没有改变,所以接下去要重写onLayout布局这些子View
,首先还是要遍历子View(其实代码比较简单)

```

 int count=getChildCount();
        int left=0,top=0,height=0;
        for (int i = 0; i < count; i++) {
            View child=getChildAt(i);
            LinearLayout.LayoutParams layoutParams= (LayoutParams) child.getLayoutParams();
            int childWidth=child.getMeasuredWidth()+layoutParams.leftMargin+layoutParams.rightMargin;
            int childHeight=child.getMeasuredHeight()+layoutParams.topMargin+layoutParams.bottomMargin;
            if(left+childWidth>=getMeasuredWidth()){
                //left代表View的左坐标 每当需要换行时,那么left必定是0
                left=0;
                //换行之后改行的top需要重新计算高度,
                top+=height;
                //代表该行最高的高度需要重新
                height=childHeight;
            }else {
                //如果没有超过,还在这一行,那么这一行的高度需要改变,去最大值
                height=Math.max(height,childHeight);
            }
            //View开始布局,需要注意的是要加上leftMargin和topMargin值,因为我们是把它//们算到整个子View中去的,如果不加上,那么这些margin值会成为子View的额外
            //宽高
            child.layout(left+layoutParams.leftMargin,top+layoutParams.topMargin,left+childWidth,top+childHeight);
            //left值更新
            left+=childWidth;
        }
```

View.layout四个参数分辨是左上右下的坐标值,还是一样获取到子View的数据,然后在给他们布局,具体含义写在注释中了,至此就完成了自动换行的需求了



![自动换行](http://image.honglingqi.cn/blogImages/1001/one.png)



大半夜写的不够好, 脑子也不是很清楚 也不知道怎么写才能表述清楚,表达可能是我的软肋把,如何去把这个功能和思路描述完整.希望慢慢变好
