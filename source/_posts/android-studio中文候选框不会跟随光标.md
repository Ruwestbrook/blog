---
title: android studio中文候选框不会跟随光标
date: 2018-12-12 16:22:04
tags:
- android studio
---
(百度了IDEA不会跟随和Android studio不会跟随总结出来的方法)
1.找到Android studio的安装位置,将其jre文件复制一份出来,这时会有jre - 副本(用来防治万一出现问题可以复原)和jre两个文件夹
2.找到Java安装位置,把其中jre文件夹(比如我电脑上的是jre1.8.0_191)复制到Android studio安装位置中jre的文件夹中,选择覆盖相同的文件
3.复制java安装目录中的jdk文件夹(比如我电脑上的是jdk1.8.0_121)\lib下的tool到Android studio安装位置中jre\lib中,重启as即可
4.字体会发生改变,我用了consolas 18的字体,看起来比较舒服

取消双击shift搜索全局(来自:[知乎](https://www.zhihu.com/question/266147247/answer/368247772)):
1.在窗口点击Ctrl+shift+A复合键，如下图，搜索registry,选择action里面的一项
2.找到ide.suppress.double.click.handler选项，打钩 即可
