---
title: androidt图片保存的坑
date: 2018-07-05 00:24:56
tags:
- android
---
很久很久都没写过这一方面的东西了真的太生疏了,都忘记保存图片到本地了,所以写一篇记一下自己的愚昧

首次用最简单的方法BitmapFactory

<span style="color:red;">Unable to decode stream: java.io.FileNotFoundException</span>

报错?很不能理解这是为什么啊,思来想去了很久没找到原因,百度下了,结果发现权限没加,太蠢了,遂加上权限

``
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>``

还是不行,在仔细想想,Android6.0以上权限是需要用户授权的,又加上授权
``
  if(checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE)!=PackageManager.PERMISSION_GRANTED){
​            ActivityCompat.requestPermissions(this,new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE},1);
​        }
``
(具体怎么加就不说了)

在行了吧,还是不行,到底怎么回事呢,到了这一步,还是提示之前的错误,那就不是权限的问题了,是文件真的
找不到了,看了看路径,我是放在
``
Environment.getExternalStorageDirectory().getAbsolutePath() + File.separator + "pics"+ File.separator;
``

难道是这个文件夹不存在? 可能是加了几行代码判断了下
``
​     File file=new File(DISK_URL);
​            if(!file.exists())
​                file.mkdir();
``
可以了? 就这样?有点蒙,算是解决了吧,突然意识到Android并没有这么省心,每一个"/"代表了一层文件夹路径
,如果文件夹不存在的话,就要主动去创建(?有没有什么更好的办法?可以找找),那是不是和权限的关系不大?

接着试了吧权限删除

两个字 不行..

还是要加权限啊,不然不让你保存

嗯就这样

2019-03-04 PS:写Android还是会忘记权限的事情,最简单网络访问,还是会把网络权限给忘了添加,更别说权限需要申请了
