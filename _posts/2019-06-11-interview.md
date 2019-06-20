---
layout: post
title: 2019Android面试总结
author: 放纵不羁爱吃肉
---

今年到现在大大小小的接触了一些企业，比如美团，头条，小红书，虎扑，B站，英语流利说,趣头条, 今天抽空总结下面试题目，也算是对今年面试的一次总结。

## 关于我们app项目的一些问题梳理

1. 自动化打点项目方案 aop其他选型考虑 如何实现参数传递以及事件唯一值  


2. tinker 热修复实现原理 pathClassLoader dexClassLoader区别 使用tinker碰到的具体问题情况 以及热修复过程是怎么样的 tinker使用中有碰到什么问题吗  


3. 项目包瘦身问题 （压缩图片 代码混淆  cpu 分包  value 删除）运行时删除R文件是否会影响项目执行  运行时把 R.id.xxx 直接替换成 地址是否影响项目执行  


4. jsbridge 以及项目文章详情页如何优化 （如何调用js 实现 回调方法 本地html 等）优化思路 提早初始化webview 建立webview cache类 从cache类内获取webview  


5. 图片库选型原则以及原理 (LruDiskCache LruCache glide Picasso fresco 选择原因  我们项目有gif)     


6. 安卓路由库的自研原因以及完成度 以及项目模块化架构的封包规则  


7. Diffutils recyclerview 优化  通过RecycledViewPool 共享viewholder 可以完成一部分优化  


8. 音视频播放小窗口还有播放器控件等 这个地方涉及到一个全局悬浮窗权限申请  


9. httpdns  为什么需要使用到httpdns 当前使用的网络请求是https吗   


10. okhttp 使用情况 okhttp的拦截器机制 以及okhttp的职责链设计模式  


11. 推送优化 根据厂商定制推送通道  


12. Rxjava 使用情况以及是否使用过自己封装RxJava  rxjava 背压问题


## 安卓基础姿势：  


1. handler 原理  activity启动流程  （activitythread  ams)  


2. activity window docorView 之间的关系  


3. view的绘制是由什么东西串联在一起的  viewTreeObserver 是基于什么机制监控的   mAttachInfo是什么  


4. activity 启动流程  (加分项 asm activitythread applicationthread)  


5. 进程间通信  （加分项 Android匿名共享内存（Ashmem）原理）是否进程间也能共享内存  


6. recyclerview （加分项 recyclerviewpool 使用 以及recycler 缓存原理 四级缓存设置 ）  


7. 计算bitmap内存大小 btimap 8888 565 之间的差别 以及内存占用情况  分析下webp png  jpg 加载进来之后内存是否一样  

## java基础姿势:  

1. 单例模式  静态内部类写法  枚举写法  


2. hashmap实现原理 list有几种 分别实现规则 set的有几种 分别是用什么实现的 数据结构必考题  


3. java线程池创建参数规则等  以及参数代表的意义


4. volatile 关键字作用以及是否具有原子性  


5. synchronized ReentrantLock 区别作用  cas原子操作  


6. java 注解的使用 （我个人看法  注解单独使用是没有意义的 需要配合aop或者别的进行二次开发才是有价值的）


## 网络层姿势:  

1. 三次握手 四次挥手  

2. https 如何实现  

3. 如何优化一个网络请求  优化方向 dns 传输数据  缓存 etag  


## 算法题汇总:  

1. 合并两个有序链表  


2. 输出链表倒数第K位  


3. 堆排序简述（没答出来）


4. 输出数组和为N的数


最后分享一个复习网站https://legacy.gitbook.com/book/lrh1993/android_interview_guide
