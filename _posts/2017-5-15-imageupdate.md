---
layout: post
title: 华尔街见闻安卓图片库升级之路
author: 放纵不羁爱吃肉
---

##华尔街见闻安卓图片库升级之路

很久很久以前有一家叫华尔街见闻的公司，刚开始的时候由于技术团队没有实例化，所以外包了第一个移动端版本。外包的安卓用的图片库就是众所周知的[Android-Universal-Image-Loader](https://github.com/nostra13/Android-Universal-Image-Loader)。在之后版本迭代中，因为业务需要增加了gif展示，这个时候UIL已经无法满足图片需求了，于是我们就增加了一套google推荐的图片加载框架[Glide](https://github.com/bumptech/glide)。但是由于我们是两套图片处理框架共存，所以线上版本的app经常会出现一些OOM（内存溢出）的问题，毕竟两套图片处理框架会同时开辟两个不同的内存块，但是我们能使用的最大内存是固定的，两套图片库之后内存之和超过最大内存值的情况下，自然就会出现OOM的情况。

然后英俊的我来到了我司，一来总是要搞一波大新闻的吗。最开始的时候我先抽离了一个简单的工具类，专门用来隔离这些图片加载的第三方类库，针对我们自己的项目我们只暴露了一系列的静态方法，所有的业务层代码全部都是调用这些静态方法去处理。之后我就开始体力劳动了，把项目中所有用到的图片加载的地方全部做了一次替换（真体力活无双）。这个就是当时做的第一件事，抽象化出一个组件，这个组件同时包含了一个专门的ImageView，独立的去负责处理所有图片加载的业务。


突然有一天，我们线上用户开始反馈说：“你们这个app好辣鸡啊，一天用掉我200M流量啊。” 这个时候的我是懵逼的，我根本不知道到底发生了什么情况的吗，只能用Charles去抓包看看数据流量的问题。经过一遍遍猜测之后，我们定位到了是由于我们小编上传的是高清无码的图片，然后最大的图片可能有4M左右。这样一个列表页的滑动可能就会有巨大的流量产生，OMG！WTF！所幸我们图片都是上传的七牛云（广告植入的毫无痕迹），然后我们就对我们列表图片增加图片剪切后缀，先对其进行了一波史诗鸡优化。这种后缀能帮我们把图片裁剪到特定的比例下，举个例子轮播图的图片只需要400X300就够用了一样，这样压缩下来一张图片从平均1M的原图可以压缩到30K左右。真的要谢谢用户不杀之恩啊！跪舔金主。

[原图](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5)     [裁剪规则](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5!app.list.thumbnail)


由于这个事件，我觉得我司需要有一个优秀的流量采集方案，所以我们这个时候引入了非死不可的一个叫[Stetho](https://github.com/facebook/stetho)的库。由于我们用的第三方图片加载框架都会去替换掉他的网络加载模块，而我们会把网络加载模块全部替换成OKHTTP，所以只要通过构造的时候对OKHTTP的client增加一个拦截器就能完美的做到网络请求的拦截。而且这个库还是chrome插件的形式，当然还有些非常酷的功能，比如还可以查看你app的sqlite，是不是炒鸡酷啊，下面是配图啊。

![stetho1](https://leifzhang.github.io/leifzhang.github.io/images/inspector-discovery.png)

![stetho2](https://leifzhang.github.io/leifzhang.github.io/images/inspector-network.png)

后续的版本升级迭代中，我们后续发现如果完全用后端约定好的`!app.list.thumbnail`这种后缀应用场景是在是有点过于局限，毕竟最好的方案是IOS 安卓，前端，三端用的裁剪比例是按照分辨率裁剪才是最好的。这个时候我们对这个裁剪规则由进行了一次小小的升级更新，以下这些这种图片裁剪规则可能才是三端真正需要的，按照实际业务场景去裁剪我们想要的图片尺寸大小，上面还有一种是七牛的高斯模糊规则，高斯模糊其实也没必要客户端自己做的。


[等比缩放](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/thumbnail/640)     [居中裁剪](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageView2/1/w/600)     [高斯模糊](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/thumbnail/640x480/blur/20x5)


各位看官应该也知道我司最近搞了一次后端技术重构，辣鸡安卓也积极响应了这次的号召，配合着一起做了一些升级和迭代，我司用户应该也记得去年见闻app过热的情况，这个问题其实有很多是历史原因，还有一些其实就是我们用的glide这个库其实对大的gif图片展示会有一些cpu使用率过高的问题，我们专门做过一次简单的实验，当展示一张4:3大小全屏的gif的情况下，glide的cpu使用率大概会是20%左右，这个数字已经基本是要上天了，而当我们把这个换成viewpager，然后你又设置了setOffscreenPageLimit，这个时候真的就起飞了，我们测试的结果应该是70以上，叠加的情况非常的可怕啊。我在[Glide](https://github.com/bumptech/glide)的issue中也找到的gif导致cpu过高的相似答案，[Glide gif CPU consumption](https://github.com/bumptech/glide/issues/1604)，证明这个问题的确存在的。由于以前做竞品调研啊，我特地反编译过头条和网易新闻，我发现他们有一家对gif图片是采取的一个叫[android-gif-drawable](https://github.com/koral--/android-gif-drawable)这样一个库，而另外一家的图片库选择的是[fresco](https://github.com/facebook/fresco)，真的让我细思极恐啊。


所以当时放在我面前的其实有两套方案啊，一个是glide + android-gif-drawable，另外一个是我也换成非死不可的库[fresco](https://github.com/facebook/fresco)，其实我不会告诉你我傻得两个方案都特么试了一遍。最后呢我们还是采用了第二种方案去完成这次图片库最重要的升级更新。

### 原因其实有两点啊:

#### 一、
fresco在内存回收和释放上以及图片情况非常多的情况下是优于glide的，而且android-gif-drawable，这个控件必须要加载进一张gif之后才会告诉我们这个是不是一个有效的gif，不是特别友善。

#### 二、
fresco可以支持动图的webP , fresco可以支持动图的webP , fresco可以支持动图的webP,重要的事情说三遍。

对于webP不了解的同学可以先给你们开一个传送门[webP](https://www.zhihu.com/question/27201061)。webP的图片格式可以把我们的流量大小砍掉最起码一半，同样一张120X90的图, JPG 10K左右的大小，而webP只有5K。5K啊！！！GIF转webP 1M的情况下可以压缩到只剩1/3。这就非常可怕了，其实我想说讲道理腾讯这次真的风光了，TPG的大小才是可怕，GIF竟然可以压缩的只剩1/10。跪拜腾讯大厂，这次方案比谷歌还屌，先刷个火箭，66666666666。

最后大家肯定要说了啊，你不可能要求小编去约定上传格式啊，万一前端不支持webP这么办啊，还有IOS呢等等情况。这个时候我只想说广告时间。其实七牛云是允许我们约定返回的图片格式到底是什么的，而且也是支持gif转webP的，这个过程是不约束你上传图片的格式的。比方说小编上传的是JPG或者PNG，你只要约定好七牛后缀，他就会变成你想要的webP格式。

[webP缩放](https://wpimg.wallstcn.com/d626f9ae-c787-4012-8f41-f30dce3c1bc5?imageMogr2/thumbnail/640/format/webp)


上述就是我司辣鸡安卓在开发中碰到的一些问题和瓶颈以及解决方案，并不是说技术点有多么的复杂和厉害，只是有些坑开发踩了一次也就好了，能绕过去的尽量也就绕过去了，小小短文也希望与各位共勉。
