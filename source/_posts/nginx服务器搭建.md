---
title: nginx服务器搭建
date: 2018-08-16 06:24:26
tags:
- 服务器
---
闲来无事想写一个博客,因为自己有了 [域名](www.honglingqi.cn) ,但是想着写博客的话怎么也得用[博客](blog.honglingqi.cn)这个blog二级域名吧,博客也应该用hexo来写比较好,那怎么分配这个二级域名呢,查了半天发现nginx可以很好的实现,那还用什么apache啊,赶紧的nginx装起来,但是这个真的是个大坑......

首先安装nignx
`
sudo apt-get install nginx
`
能在站点上看到如下信息

![安装成功](http://image.honglingqi.cn/blogImages/web.png)

 接着安装php

`
  sudo apt-get install php7.0`

  输入 `php -v` 出现版本信息就代表安装成功了

  为什么是7.0的版本的  因为在nginx的配置文件里php的版本就是7.0的版本,为了方便

  但是在站点下新建php文件,然后访问 你会发现它会把文件下载下来,这是因为nginx不能编译php文件,必须安装对应的插件


  php低版本貌似要自己安装  7.0的版本好像自己有了 可能镜像不同会有差异 最好还是安装下一

  `sudo apt-get install php7.0-fpm
  `

  不确定这个是否是真的需要,我也没验证

  `sudo apt-get install php7.0-cgi
  `

   然后启动php服务

   `
   service php7.0-fpm start`

   接着修改nginx的配置文件

   `sudo nano /etc/nginx/sites-available/default
   `


  ```angular2html
  #location ~ \.php$ {

          #       include snippets/fastcgi-php.conf;
          #
          #       # With php7.0-cgi alone:
          #       fastcgi_pass 127.0.0.1:9000;
          #       # With php7.0-fpm:
          #       fastcgi_pass unix:/run/php/php7.0-fpm.sock;
          #}

  ```
  替换成

  ```
  location ~ \.php$ {
                  include snippets/fastcgi-php.conf;


                  # With php7.0-cgi alone:
           #       fastcgi_pass 127.0.0.1:9000;
                  # With php7.0-fpm:
                  fastcgi_pass unix:/run/php/php7.0-fpm.sock;
           }
  ```

然后重启nginx

``
sudo service nginx restart
``

至此php也可以解析了,然后就是配置二级域名了

安装 node

```bash
sudo apt-get update

sudo apt-get install nodejs

sudo apt-get install nodejs-legacy

```

```javascript

var http = require('http');

http.createServer(function (request, response) {

    response.writeHead(200, {'Content-Type': 'text/plain'});

    response.end('Hello World\n');

}).listen(8888);

console.log('Server running at http://127.0.0.1:8888/');
```

然后访问8888端口,自然会出现Hello World

接下去就是要配置二级域名了,还是打开nginx的配置文件,拉到最后,加入如下代码

```bash


server {
            listen 80;
            server_name blog.honglingqi.cn;
            location / {
                 proxy_pass http://127.0.0.1:8888;
           }
         }


```
保存退出,然后重启就可以访问了

最后说一下php的CI框架,放在网站目录后你会发现无法访问,之前在apache上是使用rewrite模块和.hatccess文件重写路径访问
百度了下只要在配置文件中加入这个文件的路径就好了

```angular2html
include (路径).hatccess;
```
但是我自己试了一下发现一只报错,然后用了一个方法完美解决:
就是在一级域名的server下面添加如下代码:
```angular2html
 location /(CI框架的名字){
                try_files $uri $uri/ /(CI框架的名字)/index.php?$args;
        }

```
我看了下之前的配置文件 然后照着写了一个,发现真的可以用,那也挺好的

总的来说nginx的配置真的比较简单,而且建站也比较方便,强烈推荐使用
