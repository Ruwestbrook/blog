---
title: IOS第三方库的之CocoaPods管理
date: 2018-06-22 10:25:22
tags:
- ios
---
在IOS开发中,也需要导入很多第三方的类库,就比如AFNetworking,用户网络请求,所以自己尝试着去实践了下,真的好多坑啊

## 1.安装
如果机器版本太久就需要更新一下ruby
```
//更新源操作
gem sources --remove https://rubygems.org/
gem sources -a https://ruby.taobao.org/
gen sources -l

//安装 cocoapods
sudo gem install cocoapods

//将Github上的开源库都托管都安装Podspec索引安装到到本地,
pod setup

```
到此就安装完毕了,如果中途有下载过慢的情况,可以搜索网络替换一下源

## 2.使用前
例如需要使用AFNetworking这个类库,可以搜索一下是否存在
```angular2html
pod search AFNetworking //可以不区分大小写
```
如果是下面这这种情况:
![搜索成功](http://image.honglingqi.cn/blogImages/0622/success.png)
那么说明可以使用了

如果出现了一下错误:
<p style="color:red">[!] Unable to find a pod with name, author, summary, or description matching `AFNetworking`</p>

执行下面的操作
```angular2html
rm ~/Library/Caches/CocoaPods/search_index.json
```
然后就可以了

## 导入
用xcode新建一个项目,然后在终端中进入这个文件夹(你也可以手动)
```angular2html
touch podfile //生成文件
open podfile
```
然后填入一些你需要使用的类库信息,这里我使用了AFNetworking来做测试,进入到其github的仓库,里面有需要添加的依赖:
```angular2html
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.0'

target 'TargetName' do
pod 'AFNetworking', '~> 3.0'
end
```
其中,吧TargetName 换成你自己的工程名字,比如我新建的工程叫做test 那么这里就填写test

接下去进行安装

``pod install
``

![安装成功](http://image.honglingqi.cn/blogImages/0622/install.png)

## 使用

之前的我都成功了很多次了,唯独最后一步使用我一直不能成功,很气,最后自己看博客看书,才找到门道:
在执行完
```pod install```
之后项目的根目录会多出来三个文件:test.xcworkspace,pods,podfile.lock
然后之后打开test.xcworkspace这个文件,而不是test.xcodeproj ,很奇怪这到底怎么实现的,最后发现自己太笨了:
1.打开Xcode 选择 open another project
1.然后找到项目,按照下图打开
![打开项目](http://image.honglingqi.cn/blogImages/0622/open.png)
1.接下去就可以使用啦,再头部导入:#import "AFNetWorking.h",但是在导入的时候没有提示,
不知道为什么,一开始我还以为错了,结果是能build 成功的,照着例子请求了下网址,是能有数据的

```angular2html
-(void)get
{
    //1.创建会话管理者
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];

    //2.封装参数
    NSDictionary *dict = @{
                           @"channel":@"0",
                           @"pwd":@"1314",
                           @"type":@"JSON"
                           };
    //3.发送GET请求
    /*
     第一个参数:请求路径(NSString)+ 不需要加参数
     第二个参数:发送给服务器的参数数据
     第三个参数:progress 进度回调
     第四个参数:success  成功之后的回调(此处的成功或者是失败指的是整个请求)
     task:请求任务
     responseObject:注意!!!响应体信息--->(json--->oc))
     task.response: 响应头信息
     第五个参数:failure 失败之后的回调
     */
    [manager GET:@"请求的url" parameters:dict progress:nil success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        NSLog(@"success--%@--%@",[responseObject class],responseObject);

    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {

        NSLog(@"failure--%@",error);
    }];
}
```
### 总结
 Object-C 真的是让人头大的东西,刚学IOS开发,还有很多不懂的地方啊,学习吧
