---
title: targetSdkVersion为28时注意事项
date: 2019-05-06 10:48:46
tags:
---
新建项目的时候targetSdhVersion指定为了28,然后在自己Android6.0上测试一直没有问题,但是用同事的小米8,Android版本为8.0的时候,却发现无论如何连接不上网络,一开始我以为是出bug了,但是重新安装过,还是会出现同样的同样的问题,也无关后台是否重启了项目,然后硬着头皮在error里面找错开始Google,然后发现了一片新天地:

* 关于无法访问网络:

我用的是okhttp请求网络,打印了下面的错误:
```
java.net.UnknownServiceException: CLEARTEXT communication to (ip地址) not permitted by network security policy
```


Android8.0开始,APP限制了http请求,推荐更加安全的https,所以不采取伊谢措施,你使用http的是无论如何都不能访问不了接口的
解决方法:
1.叫后端同学升级,改成https(但是不太现实,https证书啥的超级贵,增加成本)
2.将targetSdkVersion降低到28一下(简单方便,但是不符合开发者规范)
3.在res文件夹下新建xml文件,然后新建network_security_config.xml文件,写入一下配置:
```
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true" />
</network-security-config>
```
然后在AndroidManifest.xml中的application标签中加入
```
android:networkSecurityConfig="@xml/network_security_config"
```
然后就可以愉快的访问了