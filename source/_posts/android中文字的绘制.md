---
title: android中文字的绘制
date: 2018-12-12 14:11:15
tags:
- android
---
paint中对于绘制文字的函数


    //对齐方式 Paint.Align.LEFT:从左边像右边画  Paint.Align.CENTER:从中间像两侧话  Paint.Align.RIGHT:从右像左画 开始位置就是画图的起始位置
    paint.setTextAlign(Paint.Align.RIGHT);
    //是否有删除线
    paint.setStrikeThruText(true);
    //是否有下划线
    paint.setUnderlineText(true);
    //是否粗体
    paint.setFakeBoldText(true);
    //水平倾斜角度  负数向右倾斜 正数向左
    paint.setTextSkewX(-0.25f);
    //设置水平拉伸
    paint.setTextScaleX(2);
    //设置字体样式
    paint.setTypeface(Typeface typeface)

canvas中绘制文字:

    void drawText(String text, float x, float y,Paint paint)


需要注意的是画图的起始位置X是文字的(对齐方式left:文字的左边 center:文字中间 right:文字右边 ),y是文字的底部

![不同的对齐方式对绘制的影响](http://image.honglingqi.cn/blogImages/1212/20181212151507.jpg)

    void drawText( String text, int start, int end, float x, float y,Paint paint)
    void drawText( CharSequence text, int start, int end, float x, float y,Paint paint)
和上面的基本相同,只是加了需要绘制的字符串起始位置和结束位置
start:起始绘制文字的索引
end:完成绘制的索引
如果start=0,end=text.length() 就和第一个方法没有区别

    void drawText(char[] text, int index, int count, float x, float y,Paint paint)
index:起始绘制文字的索引
count:需要绘制的长度
传入的是char数组

    void drawPosText(String text, float[] pos,Paint paint)
    void drawPosText(char[] text, float[] pos,Paint paint)
这两个方法可以单独绘制每一个文字的位置

float[] pos:表示坐标的数组,格式为{x1,y1,x2,y2......}

    void drawTextOnPath( String text,  Path path, float hOffset,float vOffset,  Paint paint)
    void drawTextOnPath( char[] text,  Path path, float hOffset,float vOffset,  Paint paint)
这两个方法可以沿着路径绘制文字
hOffset:The distance along the path to add to the text's starting position(要添加到文本起始位置的路径的距离);
vOffset:he distance above(-) or below(+) the path to position the text(他在（-）或低于（+）的路径上进行距离以定位文本);
简单的理解就是hOffset表示在正常情况下绘制X轴方向的偏移量,vOffset表示Y轴的偏移量
