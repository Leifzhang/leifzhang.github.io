---
layout: post
title: 2019Android面试总结
author: 放纵不羁爱吃肉
---


##自动化打点项目方案
aop其他选型考虑 如何实现参数传递以及事件唯一值

##tinker 热修复实现原理 pathClassLoader dexClassLoader区别 使用tinker碰到的具体问题情况

##项目包瘦身问题 （压缩图片 代码混淆  cpu 分包  value 删除）运行时删除R文件是否会影响项目执行  运行时把 R.id.xxx 直接替换成 地址是否影响项目执行

##jsbridge 以及项目文章详情页如何优化 （如何调用js 实现 回调方法 本地html 等）优化思路 提早初始化webview 建立webview cache类 从cache类内获取webview

##图片库选型原则以及原理 (LruDiskCache LruCache glide Picasso fresco 选择原因  我们项目有gif) 计算bitmap内存大小 btimap 8888 565 之间的差别   

##安卓路由库的自研原因以及完成度 以及项目模块化架构的封包规则

##Diffutils recyclerview 优化  通过RecycledViewPool 共享viewholder 可以完成一部分优化

##音视频播放小窗口还有播放器控件等 这个地方涉及到一个全局悬浮窗权限申请

##httpdns 以及okhttp 使用情况 为什么需要使用到httpdns 当前使用的网络请求是https吗

##推送优化 根据厂商定制推送通道

##安卓基础姿势：
1. handler 原理  activity启动流程  （activitythread  ams)  
2. activity window docorView 之间的关系   
3. view的绘制是由什么东西串联在一起的  viewTreeObserver 是基于什么机制监控的   mAttachInfo是什么
4. activity 启动流程  (加分项 asm activitythread applicationthread)
5. 进程间通信  （加分项 Android匿名共享内存（Ashmem）原理）是否进程间也能共享内存
6. recyclerview （加分项 recyclerviewpool 使用 以及recycler 缓存原理 四级缓存设置 ）

##java基础姿势：
1. 单例模式  静态内部类写法  枚举写法
2. hashmap  list set 数据结构必考题
3. java线程池创建参数规则等
4. volatile 关键字作用以及是否具有原子性
5. synchronized ReentrantLock 区别作用  cas原子操作
