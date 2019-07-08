---
title: Java面试
date: 2019-06-15 10:16:15
tags:
---

public class Main {

    public static void main(String[] args) {
        System.out.println("Hello World!");
        int a=1;
        test(a);
        //==比较的是2个对象的地址，而equals比较的是2个对象的内容。
        System.out.println("a2="+a); //输出1
        Integer b1=110,b2=110,f1=400,f2=400;
        System.out.println(b1==b2);//true
        System.out.println(f1==f2);//false
    }
    private static void test(int a){
        a++;
        System.out.println("a1="+a); //输出2
    }
}



==比较的是2个对象的地址，而equals比较的是2个对象的内容。


wait和sleep的区别