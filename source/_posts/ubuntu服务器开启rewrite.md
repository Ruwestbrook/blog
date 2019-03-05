---
title: ubuntu服务器开启rewrite
date: 2018-07-05 00:49:00
tags: 
- 服务器
---
自己部署了一个小项目到自己的服务器上,准备写几个接口写一个app练练手,用了php的CI框架,但是传上去发现不能用?
一直提示我路径不存在?传了N多次还是这样,开始思考到底哪里出问题了,下载了一个新的CI框架部署在本地,写了与i个测试界面
也是进不去,对比了一下,发现少了一个 .htaccess 文件,原来是它搞的鬼,但是看了看服务器上的项目根目录是有的啊
一脸好奇,有问题找百度,发现是rewrite模块没打开(但是百度的很多方法都是没用的,你们抄来抄去有意思吗?)

终端输入:sudo a2enmod
接着输入: rewrite

然后编辑配置文件,修改www目录下的权限
cd /etc/apache2
sudo vim apache2.conf


``
<Directory /var/www/>
​    Options Indexes FollowSymlinks
​    AllowOverride None
​    Require all granted
</Directory>

``

把 AllowOverride None 修改为 AllowOverride All

重启apache2  sudo /etc/init.d/apache2 restart

完成
