---
title: android路由
date: 2019-02-24 20:12:04
tags:
- android
---
感想:

1.找了差不多两周的工作了吧,面试的十几家,都快自闭了,也不是没人要我,但是就是不满意薪资,谢谢这家公司可以给我这个机会
去开发Android,加油吧

2.看了这个代码,我才发现,自己之前写的Android代码都是什么辣鸡,就这一个项目够我学的了

回归正题:

在之前的面试就很奇怪,有人问我Android无协议跳转有没有用过,我一脸懵逼,这到底是个什么东西,知道看到了这个项目,我才反应过来,哦,原来如此

路由的由来:Android之前的页面跳转,都是用intent来实现的,而且大家也都是这么做的,但是一旦项目庞大起来,页面越来越来
利用intent来构建跳转会变得会变得越来越难维护,比如有多个页面需要跳转到MainActivity,那么一般的形式会


        Intent intent=new Intent(context,MainActivity.class);
        startActivity(intent)
现在需求改变,统一跳转到MainActivity2页面,那么就需要在这些页面里面都要把MainActivity.class改成MainActivity2.class
,非常麻烦,而且像我这么粗心的程序员,还可能改错或者漏改

为了解决这种问题,相当于给页面之间解耦,降低页面之间的依赖,所以就出现了路由的方式:

页面统一都打上一个标签,表明需要跳转到哪一个页面,采用统一的方式进行跳转,然后传的参数就是标签,也可以带上自己的参数
这样的话,需要改变跳转的页面,只需要改变页面的标签就好了,

说了很多,下面看看实例,我采用的是[ActivityRouter](https://github.com/mzule/ActivityRouter)框架

1.引入 在app下的build.gradle添加如下

     compile 'com.github.mzule.activityrouter:activityrouter:1.2.2'
     annotationProcessor 'com.github.mzule.activityrouter:compiler:1.1.7'

2.集成框架:在AndroidManifest.xml加入如下


        <activity
            android:name="com.github.mzule.activityrouter.router.RouterActivity"
            android:theme="@android:style/Theme.NoDisplay">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="mzule" /><!--改成自己的scheme-->
            </intent-filter>
        </activity>

PS:一开始非常诧异为什么他可以做到这么跳转,直到看到这个,我想应该就是统一跳转到RouterActivity页面,然后再根据路由表(就是页面和标签的配对文件)
去找,然后在进行二次跳转并关闭这个页面
