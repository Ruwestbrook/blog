
---
title: 带删除按钮的EditText
date: 2018-09-02 20:20:15
tags:
- android
---


大多数APP中输入框(EditText)都是有一个删除全部的按钮让用户可以直接一次性删除所输入的内容，但是原生的控件
并不支持，第三方的也很多，但是还是要自己写一遍来加深印象，顺便复习下自定义View的使用


### 1.定义

```

    public EditClearText(Context context) {
        this(context,null);
    }

    public EditClearText(Context context, AttributeSet attrs) {
        this(context, attrs,android.R.attr.editTextStyle);
    }

    public EditClearText(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    
    }

```
需要注意的是在第二个方法中如果是使用this的话，第三个参数需要传android.R.attr.editTextStyle，否则不能
被系统判定为是一个输入框（点击不能弹起键盘，没有背景）


### 2.画图

#### 测量

```


    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);

        if(!isMeasure){

            int paddingRight=getPaddingRight();

            size= (int) ((getMeasuredHeight()-getPaddingTop()-getPaddingBottom())*0.75);

            int flag=DisplayUtil.dp2Px(3);

            setPadding(getPaddingLeft(),getTop(),getPaddingRight()+size+flag,getBottom());

            drawableLeft = getMeasuredWidth() - paddingRight - size;

            drawablePadding = (getMeasuredHeight() - size) / 2;

            isMeasure=true;

        }

    }


```

1. drawable的大小是取了高度的3/4
2. 重新设置paddingRight是为了在输入文字的时候不会重叠
3. isMeasure的作用是防止重复设置paddingRight

#### 绘制

```
    @Override
    protected void onDraw(Canvas canvas) {

     if(!getText().toString().equals("")){
        canvas.save();
        /*
         * 需要注意的是当文字内容有多行是，View的高度不变，但是会增加scrollY,所以绘制删除键位置时，需要考虑scrollY的值
         */
        int drawableTop = (getMeasuredHeight() +getScrollY()- drawablePadding-size);
        canvas.translate(drawableLeft,drawableTop);

        drawable.setBounds(0,0, size,size);

        drawable.draw(canvas);

        canvas.restore();

        super.onDraw(canvas);
     }

    }

```


直接使用drawable.draw(canvas)会比转成bitmap画上去方便 需要注意的是canvas.save(),canvas.restore()
因为canvas的坐标系被移动了

特别注意

```
 int drawableTop = (getMeasuredHeight() +getScrollY()- drawablePadding-size);
```
因为在文字的多行的时候，虽然View的高度不会改变，但是scrollY会变化，如果只是按照高度绘制在中间的话，会导致图片往上移动

### 事件

```
   public boolean onTouchEvent(MotionEvent event) {

        if(event.getAction()==MotionEvent.ACTION_UP){

            if(event.getX()>=drawableLeft && event.getX()<=(drawableLeft+size)&&
                event.getY()>=drawablePadding && event.getY()<=(drawablePadding+size)){

                setText("");
                return true;

            }
        }


        return super.onTouchEvent(event);
    }
```

判断手指抬起时是否在drawable上，如果是，那么就清除文字








