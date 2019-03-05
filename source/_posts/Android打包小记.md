---
title: Android打包小记
date: 2019-03-04 14:01:03
tags:
- android
---

1. 压缩图片来减小apk体积,压缩地址:https://tinypng.com/

2. 减少支持的SO库构架,一般都有:armeabi-v7a,armeabi,armeabi-v8a,X86, 'mips', 'mips64'不用全部选择,
然后如果SDK中需要那就没有办法(这个方法最减少apk体积)

3. 适配全面屏:
AndroidManifest.xml中application加入:
```
    android:resizeableActivity="true"
```
的属性,然后加入:

```
<meta-data
        android:name="max_aspect"
        android:value="2.1"/>
```

4. dex突破65535的限制:在build.app中加入
```
  multiDexEnabled true
```
5.移除无用的resource文件(release常见配置)
```
buildTypes {

       debug {
        .
        .
        .
        .
       }

       release {
           // 不显示Log
           buildConfigField "boolean", "LOG_DEBUG", "false"
           // 是否混淆
           minifyEnabled true
           // zipAlign优化
           zipAlignEnabled true
           // 移除无用的resource文件
           shrinkResources true
           .
           .
           .
           .

       }
   }
```

PS:删除无效代码实际并不能有效的减少APK体积
