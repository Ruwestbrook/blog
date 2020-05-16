---
title: ijkplayer使用
date: 2019-09-16 20:39:09
tags:
---

有两种方式导入ijkplayer，

第一种：也是最简单的第一种，直接用Gradle导入

~~~groovy
 		# required, enough for most devices.
    implementation'tv.danmaku.ijk.media:ijkplayer-java:0.8.8'
    implementation 'tv.danmaku.ijk.media:ijkplayer-armv7a:0.8.8'

    # Other ABIs: optional
    implementation 'tv.danmaku.ijk.media:ijkplayer-armv5:0.8.8'
    implementation 'tv.danmaku.ijk.media:ijkplayer-arm64:0.8.8'
    implementation 'tv.danmaku.ijk.media:ijkplayer-x86:0.8.8'
    implementation 'tv.danmaku.ijk.media:ijkplayer-x86_64:0.8.8'

    # ExoPlayer as IMediaPlayer: optional, experimental
    compile 'tv.danmaku.ijk.media:ijkplayer-exo:0.8.8'
~~~



第二种非常复杂，需要下载编译，想要的可以去网上找找教程



