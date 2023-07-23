---
layout: post
title:  "线程进程基本概念"
date:   2018-12-27 
header-style: text
categories: 动动脑
tags: 线程进程
---
### 一个有意思的理解方式
好奇，进程，线程，时间片，时序等抽象系统级概念。有幸拜读国外友人所书，斗胆简译之。

**进程犹如房子：**
房子乃容器也，包括地板，卧室，厨房，卫生间等等。实际上它并不会积极的有任何行为。

**线程犹如住在里面的人：**
作为主动的个体，我们人会使用房子里面的东西，卧室睡觉，客厅看电视，
聊天喝酒等等。


**单线程犹如独居：**
独居是一个很好的比喻，你，都可以，在你的房子里面为所欲为，因为所有的资源都是你的。你不需要排队上厕所，


**多线程犹如你招了一个室友：**
我们假设这个新招的室友是你老婆。早上起来的时候，你需要和你老婆抢厕所，做饭的时候需要分配谁来切菜，聊天看电视的时候，你们会商量看甄嬛传还是nba。

如果，你和你老婆非常冷静"线程安全的"，你们能够和睦的相处，并不会出现资源竞争的情况。嘿嘿，家里面多了几个孩子，那么，有趣的事情就会发生。


**假设现在在商城：**
1.商城卫生间允许一个人使用，使用前需要拿到门外的钥匙，并且关门。(互斥锁，mutex)。
2.商城卫生间允许最多10个人使用，门外放10把钥匙.(信号量，semaphore)


**此刻我比较好奇,如此类比，我们这个世界的cpu是什么呢？**
LINK: [source](http://www.qnx.com/developers/docs/6.4.1/neutrino/getting_started/s1_procs.html#Fundamentals)


### **线程基本概念**

1.调度，安全(共享数据特性，并发的情况下，原子性操作受到破坏。)，线程与内核线程间的映射关系。

	lightweight process(lwp),minest cell of application.
	
	it's consist of lwp_id, pc(current pointor of command),寄存器集合，堆栈。

2.同一个进程内的线程共享内存，代码，数据段，堆，以及进程级资源（文件，信号）

3.线程状态：runnning, ready, waitting

4.线程类型：1.io bound thread  2. cpu bound thread

### **进程相关概念**

1.Windows平台:CreateProcess ,CreateThread

2.linux平台: 任务的概念类似于单线程进程。 具有内存空间，执行实体，文件资源。
选择共享内存空间，called all the exe(thread/process) "task"。

- create new task in linux：1.fork 2.exec 3.clone
fork:类似role的管理，配合exec 执行新任务。(产生新任务)
clone(产出新线程)


解决方法：同步，加锁

### **锁的实现：**

	1.二元信号量（适用于只能被唯一一个线程访问的资源）
	2.信号(初始值位N的信号量可以被N个进程访问) ？？ 
	A(n-1) B(n-2) C(n+1) D(n+1)
	3.互斥量（Mutex）: A(1)-A(0) /B(1)-B(0)
	4.临界区：仅仅限于本进程使用
	5.read-write-lock:状态：free /shared /exclusive.
	6.condition variable：一支穿云箭，起义

多线程内部情况：

过度优化：cpu 乱序执行，解决 barrier cpu指令


### **进程创建过程:**

要想运行任何一个程序，操作系统必须首先创建一个进程。当创建新的进程之后，在主进程表中会加入一个新的条目，创建并初始化一个新的PCB。PCB中会包含进程id，父进程id，进程的入口等。
吞吐量，并发，实时性（并发，异步，缓存）

以生产者-消费者模型设计任务队列

生产者-消费者模型是人们非常熟悉的模型，比如在某个服务器程序中，当User数据被逻辑模块修改后，就产生一个更新数据库的任务（produce），投递给IO模块任务队列，IO模块从任务队列中取出任务执行sql操作（consume）

[实现](https://blog.csdn.net/luoweifu/article/details/46701167)

如果我们不显示地创建线程，那我们产的程序就是只有主线程的间线程程序。

