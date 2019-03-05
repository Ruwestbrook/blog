---
title: recyclerView分割线的源码阅读
date: 2018-09-02 20:20:15
tags:
- android
---
 recyclerview分割线是比较常用的功能吧,自己也想尝试一下如何添加,就想着去学习一下,看了网上的博客自己照着写了一下,但是发现还是阅读源码的方式稍微好一点把,因为Android
 自己实现了分割线的方法, DividerItemDecoration继承自 RecyclerView.ItemDecoration,,一步一步下去发现一些很好玩的东西,所以想着写一篇博客记录一下

 1.recyclerview能添加多个ItemDecoration

 ```angular2html
 public void addItemDecoration(ItemDecoration decor) {
        addItemDecoration(decor, -1);
    }

 public void addItemDecoration(ItemDecoration decor, int index) {
           if (mLayout != null) {
               mLayout.assertNotInLayoutOrScroll("Cannot add item decoration during a scroll  or"
                       + " layout");
           }
           if (mItemDecorations.isEmpty()) {
               setWillNotDraw(false);
           }
           if (index < 0) {
               mItemDecorations.add(decor);
           } else {
               mItemDecorations.add(index, decor);
           }
           markItemDecorInsetsDirty();
           requestLayout();
       }
 ```

所以如果需要实现GridLayoutManager网格布局的时候只需要添加两个DividerItemDecoration,其布局不相同就可以了

2.在DividerItemDecoration中看到一个方法:parent.getDecoratedBoundsWithMargins(child, mBounds),mBounds是一个新的Rect(),在此方式中,调用的又是   getDecoratedBoundsWithMarginsInt(view, outBounds)
列出getDecoratedBoundsWithMarginsInt的方法

```angular2html
static void getDecoratedBoundsWithMarginsInt(View view, Rect outBounds) {
        final LayoutParams lp = (LayoutParams) view.getLayoutParams();
        final Rect insets = lp.mDecorInsets;
        outBounds.set(view.getLeft() - insets.left - lp.leftMargin,
                view.getTop() - insets.top - lp.topMargin,
                view.getRight() + insets.right + lp.rightMargin,
                view.getBottom() + insets.bottom + lp.bottomMargin);
    }
```

可以看到的是该方法中把传入的mBounds设置top,left,right,bottom的值,关注到的是mDecorInsets这个值,接下去看getLayoutParams中有关这个值的数据:



```angular2html
  final Rect mDecorInsets = new Rect();
        boolean mInsetsDirty = true;
        // Flag is set to true if the view is bound while it is detached from RV.
```

搜索mDecorInsets这个值,发现在getItemDecorInsetsForChild方法中赋值:

```angular2html

 Rect getItemDecorInsetsForChild(View child) {
        final LayoutParams lp = (LayoutParams) child.getLayoutParams();
        if (!lp.mInsetsDirty) {
            return lp.mDecorInsets;
        }

        if (mState.isPreLayout() && (lp.isItemChanged() || lp.isViewInvalid())) {
            // changed/invalid items should not be updated until they are rebound.
            return lp.mDecorInsets;
        }
        final Rect insets = lp.mDecorInsets;
        insets.set(0, 0, 0, 0);
        final int decorCount = mItemDecorations.size();
        for (int i = 0; i < decorCount; i++) {
            mTempRect.set(0, 0, 0, 0);
            mItemDecorations.get(i).getItemOffsets(mTempRect, child, this, mState);
            insets.left += mTempRect.left;
            insets.top += mTempRect.top;
            insets.right += mTempRect.right;
            insets.bottom += mTempRect.bottom;
        }
        lp.mInsetsDirty = false;
        return insets;
    }
```

mInsetsDirty就是一个flag来判断是否完成这个方法,这个方法就是在添加ItemDecoration的值到mDecorInsets中,可以看到调用了getItemOffsets这个方法,getItemOffsets就是我们在重写分割线的时候
必须要重写的一个方法,
