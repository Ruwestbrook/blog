---
title: Recycler的稍微进阶使用(自认为)
date: 2018-08-19 08:07:21
tags:
- android
---

Android在加载数据是常用RecyclerView来展示列表数据,非常的方便,使用也非常的简单,但是碰到一些其他的问题是,有些时候会让我束手无策,在这里要总结一下:

## 1·上拉加载更多

 列表展示数据通常是多页的数据,遇到翻页的情况就不知道在什么时候去加载比较方便,通常是在最后一个item显示出来的时候在去加载新的数据是最合适的,所以怎么判断RecyclerView滑到最底部:

 首先要给RecyclerView添加滑动监听事件,上拉加载数据需要满足两个条件:1.RecyclerView停止滑动,2.最后一个item显示出来


 ```

mRecyclerView.addOnScrollListener(new RecyclerView.OnScrollListener() {
                                    @Override
                                    public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
                                        switch (newState){
                                            case RecyclerView.SCROLL_STATE_IDLE: // The RecyclerView is not currently scrolling.      
                                                break;
                                            case RecyclerView.SCROLL_STATE_DRAGGING: // The RecyclerView is currently being dragged by outside input such as user touch input.                                             
                                                break;
                                            case RecyclerView.SCROLL_STATE_SETTLING: // The RecyclerView is currently animating to a final position while not under outside control.                                              
                                        }
                                    }

                                    @Override
                                    public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
                                        super.onScrolled(recyclerView, dx, dy);
                                    }
                                });
 ```


 添加滑动事件,重写onScrollStateChanged方法, recyclerView表示当前的RecyclerView,newState表示其滑动状态:有以下三种:

 ```
    SCROLL_STATE_IDLE :The RecyclerView is not currently scrolling. 没有在滑动    数值:0
  SCROLL_STATE_DRAGGING  :The RecyclerView is currently being dragged by outside input such as user touch input 当前在滑动,且是外部因素  数值:1
   SCROLL_STATE_SETTLING :The RecyclerView is currently animating to a final position while not under outside control  惯性滑动 外部没有动作  数值:2
 ```

 判断底部:

 ```
       LinearLayoutManager layoutManager = (LinearLayoutManager) recyclerView.getLayoutManager();
        //屏幕中最后一个可见子项的position
        int lastVisibleItemPosition = layoutManager.findLastVisibleItemPosition();
        //当前屏幕所看到的子项个数
        int visibleItemCount = layoutManager.getChildCount();
        //当前RecyclerView的所有子项个数
        int totalItemCount = layoutManager.getItemCount();
        return visibleItemCount > 0 && (lastVisibleItemPosition == totalItemCount - 1);

 ```

一开始使用这个方法时想到GridLayoutManager,也可以使用吗,但是点进去之后发现GridLayoutManager时继承自LinearLayoutManager,所以是可以使用的



2.添加分割线


一个最简单的recyclerView
```

 recyclerView=findViewById(R.id.list);
        recyclerView.setLayoutManager(new LinearLayoutManager(this));
        recyclerView.setAdapter(new RecyclerView.Adapter() {
            @NonNull
            @Override
            public RecyclerView.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
                return new RecyclerView.ViewHolder(LayoutInflater.from(RecyclerViewActivity.this).
                        inflate( R.layout.item,recyclerView,false)) {};
            }

            @SuppressLint("SetTextI18n")
            @Override
            public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int position) {
                TextView textView=holder.itemView.findViewById(R.id.text);
                textView.setText("第"+(position+1)+"个item");
            }

            @Override
            public int getItemCount() {
                return 20;
            }
        });
```


之后添加默认的分割线  

```
  recyclerView.addItemDecoration(new DividerItemDecoration(this,DividerItemDecoration.VERTICAL));

```



改变布局方式,可以发现它只是在底部添加了分割线,左右两边并没有,那接下来就是要自己实现了


最简单的方法当然是研究DividerItemDecoration的源码啦,Android studio点击进去之后 会发现实际上是继承了RecyclerView.ItemDecoration这个类并实现了方法,下面就拿出最重要的几个方法来看看到底是怎么实现的


```

 @Override
    public void onDraw(Canvas c, RecyclerView parent, RecyclerView.State state) {
        if (parent.getLayoutManager() == null || mDivider == null) {
            return;
        }
        if (mOrientation == VERTICAL) {
            drawVertical(c, parent);
        } else {
            drawHorizontal(c, parent);
        }
    }
```

这个方法是最基本的方法,可以看到在里面有两个方法 如果布局是垂直(vertical)的话 那么就使用drawVertical方法,水平布局对应另一个


接下去看drawVertical方法

```
private void drawVertical(Canvas canvas, RecyclerView parent) {
        canvas.save();
        final int left;
        final int right;
        //noinspection AndroidLintNewApi - NewApi lint fails to handle overrides.
        if (parent.getClipToPadding()) {
            left = parent.getPaddingLeft();
            right = parent.getWidth() - parent.getPaddingRight();
            canvas.clipRect(left, parent.getPaddingTop(), right,
                    parent.getHeight() - parent.getPaddingBottom());
        } else {
            left = 0;
            right = parent.getWidth();
        }

        final int childCount = parent.getChildCount();
        for (int i = 0; i < childCount; i++) {
            final View child = parent.getChildAt(i);
            parent.getDecoratedBoundsWithMargins(child, mBounds);
            final int bottom = mBounds.bottom + Math.round(child.getTranslationY());
            final int top = bottom - mDivider.getIntrinsicHeight();
            mDivider.setBounds(left, top, right, bottom);
            mDivider.draw(canvas);
        }
        canvas.restore();
    }
```


canvas.save()和canvas.restore()是画布的相关,这里不再复述

它首先用parent.getClipToPadding()判断recyclerVoew是否设置了padding值 如果有的话,分割线的left和right需要调整,当不存在padd的之后 可以看到 right的值就是recyclerView的宽度,所以在recyclerView使用GrildLayoutMamanger时
设置vertical的分割线,分割线是一条的原因
