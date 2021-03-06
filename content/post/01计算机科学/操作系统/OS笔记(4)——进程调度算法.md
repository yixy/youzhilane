# OS笔记(4)——进程调度算法 #

对于单处理机多道程序设计系统来说，只有一个CPU，如果同时出现两个进程/线程处于就绪状态，那么此时需要选择下一个要执行的进程。在操作系统中，完成这项工作的调度程序所使用的算法被称为调度算法。实际上，调度算法的应用随处可见，进程调度算法同样适用于线程。

一般而言，公平（等价进程获得公平的CPU份额）和平衡（保持系统所有部分都忙碌）是调度算法所需要追求的。但是，对于不同特点的系统，还会有一些特定的目标。

* 批处理系统：吞吐量高、周转时间小（作业从开始到完成的平均时间）。
* 交互式系统：响应时间小。
* 实时系统：满足截止时间。

## 1. 先来先服务 ##

维持一个队列，先到来的进程最先被调度。一个运行中的进程如果进入阻塞，则被重新插入队尾。

这是一个非抢占式调度算法。

## 2. 最短剩余时间优先 ##

当运行时间可以预先知道时，优先调度运行时间最短的进程。

这是一个非抢占式调度算法。他的抢占式版本对算法进行了一些改造：新来的进程比当前运行进程的剩余执行时间更少时，当前进程阻塞，新进程被调度。

## 3. 轮转调度 ##

维持一个队列，队列中每个进程按顺序获得一个时间片，运行进程在该时间片内运行。若进程在时间片结束时还在运行，则剥夺其CPU分配给下一个进程。通常，时间片的设置是20ms-50ms。

这是一个抢占式调度算法。

## 4. 优先级调度 ##

给每个进程分配优先级，保证高优先级的进程先被调度。注意，这里的优先级可以是静态也可以是动态调整的。为了防止饥饿进程的出现，通常会给运行中的进程分配一定的时间片，用完时间片后，可以调低该进程的优先级。

优先级调度算法通常是一个抢占式调度算法。

## 5. 彩票调度 ##

给每个进程分配一个“彩票”，一旦需要作出一项调度策略时，系统随机抽出一张彩票，拥有该彩票的进程获得一定时间片的调度资源。注意，可以通过给更重要的进程分配额外数量的彩票，来实现优先级策略。

彩票调度算法通常是一个抢占式调度算法。
