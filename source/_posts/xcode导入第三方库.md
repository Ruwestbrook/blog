---
title: xcode导入第三方库
date: 2018-08-21 16:10:21
tags:
- ios
---
我真的觉得有些程序员真的太懒了(虽然我也很懒),关键是懒就算了,还不动脑子,一模一样的文章从别人那里Ctrl+C然后Ctrl+V有意思吗?自己也不去试试又没有用就直接当作自己的博客了,也是醉了.然后不排除是我自己用不来这些方法的原因
但是我自己研究出来更好的了,XCODE导入第三方库,就比如导入AFNetworking

1.点击xcode左上方file->选择add Files to "项目名称"

![图片一](http://image.honglingqi.cn/blogImages/1808/xcode/one.jpg)

2.选择AFNetworking路径,选中需要导入的两个文件夹

![图片一](http://image.honglingqi.cn/blogImages/1808/xcode/two.png)

3.导入成功,接下去就可以引用了

![图片一](http://image.honglingqi.cn/blogImages/1808/xcode/three.png)

可能是我自己不会用或者太笨的问题,网上的方法找这种方法:

```angular2html
1、点击项目名；

2、选择TARGETS，选中对应的target；

3、选中Build Phrases选项卡；

4、选中Link Binary With Libraries；

5、展开该条目，点击左下角的加号；

6、在列表中选择需要的Framework，即可以导入当前可用的Framework。
```
我找不到,所以只能用这种笨办法手动导入了

貌似用CocoaPods管理也挺方便的,有兴趣的可以去试试,不用手动导入了
