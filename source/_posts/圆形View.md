---
title: 圆形View
date: 2018-11-29 16:14:10
tags:
- android
---

圆形的图片在APP中是非常的常见的,之前就学习过了怎么处理自定义的View来实现圆形的图片,过了好久又忘记了
,所以来写一篇文章记录一下:

使用BitmapShader来设置圆形或者圆角的图片:
在官方的定义是:Shader used to draw a bitmap as a texture BitmapShader  使用特定的图片来作为纹理来使用
在Paint.setShader来使用

       构造函数: public BitmapShader(@NonNull Bitmap bitmap, TileMode tileX, TileMode tileY)

bitmap:需要变化的图片
tileX:在X轴上的变化方式
tileY:在Y轴上的变化方式
变化方式有三个值可以选择:

1. CLAMP 拉伸:横向的最后一个横行像素，不断的重复，纵项的那一列像素，不断的重复；
1. REPEAT 重复:就是横向、纵向不断重复这个bitmap
1. MIRROR 镜像:横向不断翻转重复，纵向不断翻转重复；

为了防止图片过大或者过小,可以通过给BitmapShader设置Matrix来控制缩放

     Matrix mMatrix = new Matrix();
     mMatrix.setScale(float sx, float sy);

分别是X轴和Y轴的缩放倍数 然后用shader.setLocalMatrix(Matrix matrix);来实现
然后再Paint中设置setShader(Shader shader)
最后用canvas.drawCircle()来画一个圆并使用设置了BitmapShader的Paint来绘制就可以完成圆形的ImageView了
也可以使用canvas.drawRoundRect()来绘制圆角的矩形
