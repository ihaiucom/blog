---
title: TSF4G
date: 2018-09-21 13:40:15
tags:
  - Server
---

https://github.com/johnnyluo586/TSF4G.git

# Tbus简介

Tbus是tsf4g(Tencent ServiceFramework for Game,腾讯游戏服务框架)中的基础组件之一，主要的目的是为上层业务提供统一的线程或进程间通信接口，屏蔽本地进程间通信以及远程进程通信的细节，让开发人员可以集中精力关注业务逻辑，是tsf4g重要组成部分。

# Tbus原理

 Tbus基于共享内存构建无锁双通循环消息队列，发送的双方通过专用的读写队列完成数据收发，实现本地进程或者远程进程间通信。通信双方使用的两个队列称之为tbus通道(channel)，每一组通讯的双方就需要有一个tbus通道。

 进程A发送消息后，消息被存储到Host A的发送队列中，部署于Host A的tbusd发现队列中存在消息，则从队列中把消息取出，通过tcp发送到Host B上。Host B上的tbusd接收到消息后，把消息写入本地的接收队列，以供进程B读取。

 为了能完成通信，Tbus还有以下几个特点：
 通信双方具备全局唯一的tbus通信地址，该地址是一个点分十进制的字符串，与IP地址类型，总长度为32bit，分为4段，每段bit位数可以自定义，总长度不超过32bit。例如：128.1.100.1,5.0.200.1。

 Tbus通道以及消息是存储在共享内存中，必须要使用相应的工具提前创建，进程才能绑定与使用。而当业务进程异常退出后，由于消息是存储在共享内存中，只要不主动清理共享内存、重启服务器或损坏共享内存，通道的消息就不会丢失。
 