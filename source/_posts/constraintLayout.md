---
title: constraintLayout
date: 2019-08-01 15:51:17
tags:
---

一年前多就开始使用constraintlayout布局了，一直知道它好用，但是也没有仔细去研究过到底好用在哪里，特别是最近闲着无事，去学习了一下constraintLayout，才发现，自己只能使用它最最最简单的东西

### 基本使用



- layout_constraintLeft_toLeftOf  //与指定View左对齐

- Layout_constraintLeft_toRightOf  //位于指定View的右边

- layout_constraintRight_toRightOf //与指定View右对齐

- layout_constraintRight_toLeftOf //位于指定View的左边

- layout_constraintTop_toTopOf  //与指定View头对齐

- layout_constraintTop_toBottomOf  //位于指定View的底边

- layout_constraintBottom_toBottomOf //与指定View底对齐

- layout_constraintBottom_toTopOf  //位于指定View的头边

- layout_constraintStart_toStartOf //与指定View初始位置对齐

- layout_constraintStart_toEndOf  //与指定View结束位置

- layout_constraintEnd_toEndOf   //与指定View结束位置对齐

- layout_constraintEnd_toStartOf//位于指定View开始位置

  

  /*

  基准线对齐，如果是TextView那么基准线为文字的基准线如果是View,那么基准线就是View的顶部

  */

- layout_constraintBaseline_toBaselineOf

  

  前面8个就不多介绍了，因为和RelativeLayout非常的相似，功能也差不多，就不多介绍了 ，start,end和left,right类似，由于我们的阅读是从左往右，所以start和left对应

   但是这里需要注意一点的是：

  1. 如果控件的宽度指定的是固定值或者wrap_content,那么在其左右两边都有约束的时候，它将会根据这两个约束条件居中显示
  2. match_parent在官方文档中是不推荐使用的，它的取代方式是0dp,0dp在此布局中的含义被替换成了match_constraint,也就是和它的约束条件的距离相等.但是match_parent任是然可以使用的，

