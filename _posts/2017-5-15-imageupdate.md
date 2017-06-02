---
layout: post
title: 华尔街见闻安卓图片库迭代过程
author: 放纵不羁爱吃肉
---

&ensp;&ensp;很久很久以前有一家叫华尔街见闻的公司，他们聘请了英俊的我来开发他们的App。来到了我司之后，自然是要搞一波大新闻的吗。

## App经常OOM，OOM了咋办？   

&ensp;&ensp;我刚到我司的时候，线上版本的经常会出现一些OOM（内存溢出）的问题，因为当时我司是两套图片处理框架同时使用，[Glide](https://github.com/bumptech/glide)和[Android-Universal-Image-Loader](https://github.com/nostra13/Android-Universal-Image-Loader)，两套图片处理框架会同时开辟两个不同的内存块，但是我们能使用的最大内存是固定的，当图片库内存之和超过最大内存值时，自然就会出现OOM的情况。这种情况下我们必须要确保app内只有`一套`图片库，同时对开发人员暴露的是一个`统一`的入口方便后续业务的更新以及迭代。

## App消耗流量巨大怎么办？

&ensp;&ensp;有一天我们线上用户开始反馈说：“你们这个app好辣鸡啊，一天用掉我200M流量啊。”然而情况是这样的，我们小编上传的是高清无码的图片，假设一张原图是1M，只要稍微一滑动列表页肯定会有巨大的流量消耗产生，OMG！WTF！所幸我们图片都是上传的七牛云（广告植入的毫无痕迹），只要我们对列表图片增加图片剪切后缀，就可以进行了一波史诗鸡优化了，文末有图片剪裁的比较以及使用方法。同时真的要谢谢用户不杀之恩啊！

&ensp;&ensp;后续的版本升级迭代中，我们后续发现特定的七牛后缀是没有办法解决所有业务场景,还容易出现一些第三方的图片无法展示问题，所以类似`!app.list.thumbnail`这种后缀就被我们用另外一种方式替换。请求图片的大小应该根据不同的场景请求不同的图片尺寸。这个时候我们对这个裁剪规则由进行了一次小小的升级更新，按照实际业务场景去裁剪我们想要的图片尺寸大小，同时我们还发现七牛的高斯模糊规则。

&ensp;&ensp;App内的流量监控,我们现在用的是非死不可的[Stetho](https://github.com/facebook/stetho)。首先引入方案十分简单，同时这个库还是chrome插件的形式数据流量，同时也可以对我们的api做一次监控。当然还有些非常酷的功能，比如还可以查看你app的sqlite，sp，是不是炒鸡酷啊，下面是配图啊。

![stetho1](https://leifzhang.github.io/leifzhang.github.io/images/inspector-discovery.png)

![stetho2](https://leifzhang.github.io/leifzhang.github.io/images/inspector-network.png)


## 手机热的要变note7？！  


&ensp;&ensp;我司用户应该也记得去年见闻app过热的情况，这个问题其中有一部分是历史代码债务原因，另外一个原因就是我们用的[Glide](https://github.com/bumptech/glide)对gif展示会导致cpu使用率过高。我们专门做过一次简单的实验，当展示一张4:3大小全屏的gif的情况下，glide的cpu使用率大概会是20%左右，而当我们把这个换成viewpager，然后你又设置了setOffscreenPageLimit，我们测试的结果应该是70以上，这个时候我已经上天了就差和太阳肩并肩。最近我在[Glide](https://github.com/bumptech/glide)的issue中也找到的gif导致cpu过高的相似答案，
[Glide gif CPU consumption](https://github.com/bumptech/glide/issues/1604)，证明不是只有我们碰到过这个问题。

&ensp;&ensp;上述问题我尝试过两套方案啊，第一个是[Glide](https://github.com/bumptech/glide) + [android-gif-drawable](https://github.com/koral--/android-gif-drawable)，另外一个是我也换成非死不可的库[fresco](https://github.com/facebook/fresco)。对比结果大概是这样的:

### 选择非死不可的原因其实有两点啊:

#### 一、fresco在内存回收和释放上以及图片情况非常多的情况下是优于glide的，而且android-gif-drawable，这个控件必须要加载一张图片之后才会告诉我们这个是不是一个gif，万一不是我们就必须对图片进行降级操作，加载成普通的一张图片。

#### 二、fresco支持webP格式的图片，同时fresco可以支持动图的webP，fresco可以支持动图的webP，fresco可以支持动图的webP，重要的事情说三遍。对于webP不了解的同学可以先给你们开一个传送门[webP](https://www.zhihu.com/question/27201061)。

&ensp;&ensp;同样的分辨率情况下,webP的图片格式要比PNG，JPG的图片要小。同样清晰度的一张JPG有46.1K，而webP只有35.1K，而PNG则要有200K。客户端对图片的要求其实只有两点清晰，流量小，同样清晰度的情况下webP肯定要优于别的图片格式。GIF转webP 1M的情况下可以压缩到只剩1/3。另外腾讯这次真的厉害了，他们的[TPG](http://www.yxdown.com/news/201705/351469.html)，GIF竟然可以压缩的只剩1/10。跪拜腾讯大厂，这次方案比谷歌还屌，先刷个火箭，66666666666。

&ensp;&ensp;最后大家肯定要说了啊，你不可能要求小编去约定上传格式啊，万一前端不支持webP这么办啊，还有IOS呢等等情况。这个时候我只想说广告时间。其实七牛云是允许我们约定返回的图片格式到底是什么的，而且也是支持gif转webP的，这个过程是不约束你上传图片的格式的。比方说小编上传的是JPG或者PNG，你只要约定好七牛后缀，他就会变成你想要的webP格式。

## 最后我会把图片和数据对大家做一个简单的介绍和展示

#### 数据展示

|名称|大小|链接|图片|
|-------|---------|-------------|--------|
|原图 |104K |https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5  | ![原图](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5)|
|原图PNG  |465K |https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/format/png |![PNG](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/format/png) |
|裁剪规则 |46.1K  |https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5!app.adr.banner.l |![裁剪规则](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5!app.adr.banner.l)|
|等比缩放 |49.7K  |https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/thumbnail/640 |![等比缩放](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/thumbnail/640) |
|居中裁剪 |53.3K  |https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageView2/1/w/600 |![居中裁剪](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageView2/1/w/600)|
|高斯模糊 | 13.3K|  https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/thumbnail/640x480/blur/20x5 |![高斯模糊](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/thumbnail/640x480/blur/20x5)
|webP格式切换 |35.1K  |https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/thumbnail/640/format/webp |![webP缩放](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/thumbnail/640/format/webp)



&ensp;&ensp;上述就是我司辣鸡安卓在开发中碰到的一些问题和瓶颈以及解决方案，并不是说技术点有多么的复杂和厉害，只是有些坑开发踩了一次也就好了，能绕过去的尽量也就绕过去了，小小短文也希望与各位共勉。
