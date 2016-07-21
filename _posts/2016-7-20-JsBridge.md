---
layout: post
title: Android JsBridge 混合开发
author: 放纵不羁爱吃肉
---

#Android JsBridge 混合开发

这里我要和大家介绍的是安卓端和Js交互的通道。如何搭建简单的Js交互通道。首先先说下我吧，我做过两个JS交互方案，第一个是通过url截获的方式去和原生交互，以前大众点评是用这个方案做的JsBridge。第二个是通过webView的addJavascriptInterface方法去挂载一个Js方法。

但是问题来了，我们总不能每有一个Js交互的方法就加载一个addJavascriptInterface吧。这样我们的代码就会显得很臃肿，而且和前端回调的方法也非常的困难，那么我们应该怎么做呢？

这个我做过的两个方案都出奇的统一，先有Js约定交互的方法名，然后由原生对其进行增强。

```java

      @JavascriptInterface
      public void invoke(String name, String args)
              throws Exception {

       }
```

上面是我们项目中的Js回调方法，其中name就是我们需要回调的前端方法名，args是前端交互的参数，通过这些参数我们可以对前端进行增强，做一些前端做的不是很好的事情，比如分享，照相机，等等。上述代码有一点需要大家特别注意的，就是Js方法回调并不是在UI线程中，不能直接操作界面控件，切记切记。

接下来就是既然我们已经知道Js如何通知前端去进行交互，那么前端如何回调Js呢。

这里还是要返回前面带给我们的args参数，args参数中会附带一个callbackId字段，这个参数使我们回调前端Js必须的参数。

```java
public static String getJavaScipt(String callbackid,
            String args) {
    return String.format("javascript:window.__YutaAppCallback(%s,%s)", callbackid, args);
}
```

我的项目是通过这个形式去回调这段Js的。当然在高版本的Sdk中evaluateJavascript的方法去回调Js，而低版本中我们只能通过loadUrl的方式去注入。

好了如果你有什么看不懂的地方下面是我github sample的超链接。

[JsBridge](https://github.com/Leifzhang/Jsbridge)
有什么问题的话可以邮件联系我，首页最下方是我的邮箱。
