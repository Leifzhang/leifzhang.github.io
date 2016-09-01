---
layout: post
title: Android 根据不同平台启动不同的推送机制
author: 放纵不羁爱吃肉
---

#Android 根据不同平台启动不同的推送机制

由于第三方推送在不同平台手机到达率不同的问题，所以正常情况下一个成熟的app的推送机制应该是有好几种的，比方说小米和华为以及魅族等特殊平台下可以考虑用官方提供的推送，这样到达率会比友盟等第三方推送的到达率高很多。
这个时候我们需要设计一套adapter机制让我们的app可以轻松的切换不同平台app的推送问题。正常情况下，一个推送系统需要有一个初始化操作，一个设置第三方账号关联操作，一个开关，一个settag操作。我们只需要暴露四个方法就可以完成这个简单的操作了。

```java
public interface IPushAdapter {
    void init(Context context);

    void setTags(String[] tags);

    void toggle(boolean isEnable);

    void setAlias(String... alias);

    void saveRegId();
}
```

上面就是我项目中的adapter代码，第一个方法是初始化，第二个方法是设置tag，第三个方法是开关，第四个是设置外联账号，最后一个是保存token，只是方便后续调试用的。
每次我们新增一个推送平台只需要实现这个接口adapter，然后再去做一次设备厂商判断实例化就好了。这个地方最好可以接入一个工厂模式去实例化对应的adapter，这样可以方便后续维护。
