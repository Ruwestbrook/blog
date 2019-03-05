---
title: Android通知使用
date: 2019-01-07 17:07:36
tags:
- android
---
又重新学习了一遍《第一行代码》,不过这次用的是kotlin来编写Android的代码,写到通知的的时候,
意外的发现书上的代码不起作用了,于是Google了下原因:
原来在Android8.0之后,Google限制了通知的使用,查看源码发现:之前的Builder(Context context)方法加上了deprecated的注解:
```
         /** @deprecated */
         @Deprecated
         public Builder(Context context) {
                    this(context, (String)null);
                }
```
查看了一下意思的话:加上这个注解说明这个方法是危险的或者有更好的方法代替

但是其实如果你指定app的编译版本在26以下时,这个方法还是有效的,但是在26以上,那么就不
能发出通知了

作为一名规范的开发者吧,还是要追随官方的脚步的,本人也不是很喜欢降低Android的编译版本来
获取一些不必要的权限或者其他的东西

随开始研究Android8.0以上的通知使用规则:
```
        public Builder(@NonNull Context context, @NonNull String channelId)
```
现在推荐的是使用这个方法来发送通知,但是很好奇的是channelId到底是个什么东西,拿来干什么?

原来从8.0开始,Google引入了通知渠道的概念,简单的来说就是指定你的APP有哪些通知类型,就比如QQ有好友发送消息的通知和好友
验证的通知,需要将通知分类,然后根据这个分类来发送通知

而且也加入了通知等级的概念,一共有六个值可以选择:
```
*. IMPORTANCE_NONE :A notification with no importance: does not show in the shade.

*. IMPORTANCE_MIN :Min notification importance: only shows in the shade, below the fold

*. IMPORTANCE_LOW :Low notification importance: shows everywhere, but is not intrusive.

*. IMPORTANCE_DEFAULT : Default notification importance: shows everywhere, makes noise, but does not visually  intrude.

*. IMPORTANCE_HIGH :Higher notification importance: shows everywhere, makes noise and peeks. May use full screen intents

*. IMPORTANCE_MAX  :Unused(没用过)


          if(Build.VERSION.SDK_INT>=Build.VERSION_CODES.O){
                    val channel: NotificationChannel = NotificationChannel("message","好友消息",NotificationManager.IMPORTANCE_DEFAULT)
                    val channel2: NotificationChannel = NotificationChannel("verification","好友验证",NotificationManager.IMPORTANCE_DEFAULT)
                    getNotificationManager().createNotificationChannel(channel)
                    getNotificationManager().createNotificationChannel(channel2)
                }
```
然后就是注册通知类型了,一定是要在8.0及以上才有这种消息管理,低版本是没有的

运行之后查看app的通知权限,可以很清楚的看到我们注册了两种消息通知,接下去就可以选择能使用通知啦





![创建通知类型](http://image.honglingqi.cn/blogImages/0107/20190107.png)
```
           private fun getNotification(title:String,progress: Int): Notification {
                val builder:NotificationCompat.Builder
                if(Build.VERSION.SDK_INT>=Build.VERSION_CODES.O){
                //这里的类型一定要填写之前注册好的类型 否则通知不会显示
                     builder= NotificationCompat.Builder(this,"message")
                }else{
                     builder= NotificationCompat.Builder(this)
                }
                val intent= Intent(this, TestActivity::class.java)
                val pi= PendingIntent.getActivity(this,0,intent,0)
                builder.setSmallIcon(R.mipmap.ic_launcher)
                builder.setLargeIcon(BitmapFactory.decodeResource(resources,R.mipmap.ic_launcher))
                builder.setContentIntent(pi)
                builder.setContentTitle(title)
                if(progress>0){
                    builder.setContentText("{$progress}%")
                    builder.setProgress(100,progress,false)
                }
                return builder.build()
            }
             getNotificationManager().notify(1,getNotification("这是一个通知",-1))
```
大功告成!
