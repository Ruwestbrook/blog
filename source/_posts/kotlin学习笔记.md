---
title: kotlin学习笔记
date: 2018-12-28 15:50:21
tags:
- kotlin
---
### ?:
Boolean和Boolean?的区别:
var x:Boolean  x的取值可以为:true,false
var x:Boolean? x的取值可以为:true,false,null
所以不能用Boolean?来判断if条件,如果要使用,那么可以用:

        var x:Boolean?= null
        if(x==true){
    
        }
来判断

 ### ?和!!的区别:
?:

    kotlin:a?.foo()
    java: if(a!=null){
       a.foo();
      }

!!:

       kotlin:a!!.foo()
       java: if(a!=null){
          a.foo();
        }else{
          throw new KotlinNullPointException();
        }

?不会抛出空指针异常而!!会,

?会使该类型多一个null的值,而!!不会

### kotlin中接口使用(kotlin中不能直接按照使用类的方式来使用接口)

interface A{
 fun a()
}

要实现有两种方式

1. 继承实现

        class B :A{
            fun a(){
            }
        }
val test=B()
2. 匿名内部类

        val test=object:A{
            fun a(){
            }
        }
必须使用object,不能用其他代替,否则报错

### until和..的区别

for(i in 0..10){}
for(i in 0 until 10){}
两者区别在于 前者 [0,10] 后者[0,10)


### kotlin中的类

#### 多个构造函数


```kotlin
//第一种写法  使用super关键字
class A : View {
    constructor(context: Context?):super(context)
    constructor(context: Context?, attrs: AttributeSet?):super(context,attrs)
    constructor(context: Context?, attrs: AttributeSet?, defStyleAttr: Int):super(context,attrs,defStyleAttr)
}

//第二种写法  
class A(context: Context?, attrs: AttributeSet?, defStyleAttr: Int) :
    LinearLayout(context, attrs, defStyleAttr) {
    constructor(context: Context?):super(context,null,0)
    constructor(context: Context?, attrs: AttributeSet?):this(context,attrs,0)
}
```
第一种写法更加简洁明了

#### 类初始化方法
class A{
  init {
       //初始化代码放在此处进行
    }
}

#### vararg

vararg 表示可变参数

##