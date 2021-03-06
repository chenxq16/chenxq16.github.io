---
layout: post
title:  "android客户端异步任务调度中心"
subtitle:   "客户端调度中心"
author: "MakerChen"
# header-img: ""
date:   2017-05-28 1:50:11
tags:
   - android
---

***

### 背景情况

在平常的客户端开发中，异步操作占有相当大的比重。譬如读取网络数据、向服务端上送埋点事件、日志本地记录等。这些异步任务有些是与页面相关的，像获取完数据后需要经过处理并展示在页面上；而有些仅仅只是通知性任务，例如埋点事件上送、日志本地记录，对用户基本无感，对程序来说也不一定需要关心他是否成功执行；还有些任务之间存在着先后的逻辑关系；还有些异步任务则组合成了一组批量任务，等这组任务全部完成后需要通知出来。显然上述这些情况，在每个页面单独去处理这些重复的、复杂的逻辑是比较费时费力的。由此产生了一个想法解决该问题。

### 职责

综上上述即时该项目的主要职责，例如：
负责调度各种异步任务；利用线程池技术减少系统资源的浪费；实现各种场景下的异步任务需求（具有先后顺序的异步任务、具有分组属性的任务等）；

### 怎么做 ？

#### 至少应该有的功能

1. 任务之间需存在优先级。

	任务中添加优先级字段，添加至有序队列中。

2. 任务完成后需按需通知调用方。

	任务默认应该是需要通知调用方的，但也可扩展为仅执行无需通知（例日志记录）。

3. 具有分组属性的任务全部完成后，统一通知。  

	需要一个优先级最高的任务送至有序队列，待其它同批次任务完成后，通知该任务执行回调给调用方。

	- 同一批次无序任务

	- 同一批次有序任务  
		简单的做法是重新排列任务的优先级，但改变了原有任务的属性。复杂的做法可扩展新的字段。但优先级本来就是用来排列顺序的，且调用方无需太过考虑该属性。此处的做法更应该算是一个妥协的方案，便于调用方的调用。后续存在实际场景可以在考虑优化。

4. 任务完成后按需在UI、子线程进行通知。

	任务的一个属性，默认是在子线程中处理。可更改该属性在UI线程中。

#### 一般异步任务结构图

![结构图](/media/taskscheduler/MessageQueue.jpg)

1. 调用方将需要执行的异步任务，添加至待执行任务队列列表。
2. 待执行任务列表维护好异步任务之间的先后顺序。
3. 有一个异步线程负责循环读取该列表中的任务，送至线程池中，并控制线程池中线程的数量。
4. 使用线程池中天生具有线程重复利用的线程池技术，减小系统开销。

参考：  
1. “天生具有顺序的队列（尴尬）”，可参考PriorityQueue，PriorityBlockingQueue。  
2. 线程池可使用Executors.newCachedThreadPool(); 无界的可缓存线程池。

#### 批次异步任务结构图

![结构图](/media/taskscheduler/BatchTask.png)

这种具有批次属性任务的处理方式相对比较粗暴。根据传入进来的批次任务，生成一个具有最高优先级的异步任务，插入到有序队列中，保证其优先执行。待具有批次属性的异步任务执行完毕后，通知该新的异步任务，完成任务。

参考：CountDownLatch，允许一个或多个线程一直等待，直到其他线程的操作执行完后再执行。

#### 类图

![类图](/media/taskscheduler/ClassImg.jpg)


项目已开源，参见：https://github.com/chenxq16/TaskScheduler
