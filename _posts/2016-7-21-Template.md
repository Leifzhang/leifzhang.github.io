---
layout: post
title: Android 混合开发之前端模板
author: 放纵不羁爱吃肉
---

今天和大家介绍下如何提高webView加载速度。

正常情况下我们通过加载一段url的方式加载一个H5页面，时间都是相对比较慢的，因为我们要加载前端Js，Html样式等等，还有时候会有耗时的GA操作。这样就经常会导致用户反馈页面加载慢等等。而且安卓端很难对图片等资源进行缓存。

这里我就要介绍下我们项目的解决方案了，我们可以通过拍黄片经常使用的方式去对其进行优化。通过加载前端模板，然后通过Js调用的方式去把前端需要的数据加载给前端。

[https://github.com/JeasonWong/NewsTemplate](https://github.com/JeasonWong/NewsTemplate)

这个是我同事的前端模板样式的sample，其中在Js加载这一段我对其进行了一定的优化，我采取的是预加载的方式，让webView先去加载本地HTML，然后通过addJavascriptInterface的方式去通知安卓端去加载数据，当安卓请求完数据之后再调用前端方法就可以了。

下面就不多说了，大家自己看sample吧，有什么问题的话可以邮件联系我，首页最下方是我的邮箱。
