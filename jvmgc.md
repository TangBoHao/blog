---
title: JVM中的GC
date: 2018-06-04 01:02:18
tags:
---
##GC概念
Garbage Collection 垃圾收集
GC的对象是堆空间和永久区


<!--more-->







##GC算法

####引用计数法

通过引用计算来回收垃圾。对于一个对象A，只要有任何一个对象引用了A，则A的引用计数器加一，当引用失效时，引用计数器减一。只要对象A的引用计数器的值为0，则对象A就不可能再被使用。

注：没有使用这种算法

存在的问题：
 - 引用和去引用伴随加法和减法，影响性能。
 - 很难处理垃圾对象的循环引用

####标记清除法

标记-清除算法时现代垃圾回收算法的思想基础。将垃圾回收分为两个阶段：标记阶段和清除阶段。一种实现：在标记阶段，首先通过根节点，标记所有从根节点开始的可达对象。因此，未被标记的对象就是未被引用的垃圾对象。然后，在清除阶段，清除所有未被标记的对象。

####标记压缩法
标记-压缩法适合用于存活对象较多的场合，如老年代。它在标记-清除法的基础上做了一些优化。和标记清除法一样，它也是先从根节点开始，对所有可达对象做一次标记，但之后，它并不简单的清理未标记的对象，而是将所有的存活对象压缩到内存的一端，之后，清理边界外的所有的空间。


####复制算法

复制算法是一种相对高效的回收方法。不适用于存活对象较多的场合，如老年代。用于新生代。将所有的内存空间分为两块，每次只使用其中一块，在垃圾回收的时候，将正在使用的内存中存活对象复制dao未使用的内存块中，之后，清除正在使用的内存块中的所有对象，交换两个内存的角色，完成垃圾回收。

问题：空间浪费 整合标记清理思想 减少复制空间的大小 （将老年代和大对象放到另外的空间。


##分代思想
根据对象的存活周期进行分类，短命的对象归为新生代，长命的对象归为老年代。
根据不同代的特点，选取合适的垃圾收集算法：
 - 少量对象存活，适合复制算法
 - 大量对象存活，适合标记清理或者标记压缩










##可触及性
所有的算法，需要能够识别一个垃圾对象，因此需要给出一个可触及性的定义。
可触及的：从根节点可以触及这个对象
可复活的：一旦所有的引用都被释放，就是可复活的对象，因为在finalize()中可能复活该对象。
不可触及的：在finalize()后，可能会进入不可触及的状态。不可触及的对象不可复活。可以回收。

注：对象的finalize()方法只会调用一次。且优先级低，何时被调用不确定，所以不推荐使用。可以使用try-catch-finally来替代他。

根的概念：

 - 栈中引用的对象
 - 方法区中静态成员或者常量引用的对象（全局对象）
 - JNI方法栈中引用的对象


###Stop-The_world
 - Java中一种全局暂停的现象
 - 全局停顿，所有Java代码停止，native代码可以执行，但是不能和JVM交互
 - 多半是由于GC引起的
  - Dump线程
  - 死锁检查
  - 堆Dump

 GC产生全局停顿的原因：垃圾不断产生
 应对：当GC线程工作的时候 其他所有的线程都停下来


##GC的参数

####堆的回顾
eden	s0	s1	tenured
新生代	复制区	老年代


####串行收集器
 - 最古老，最稳定
 - 效率高
 - 可能产生较长时间的停顿
 - -XX+UseSerialGC
  - 新生代、老年代使用串行回收
  - 新生代复制算法
  - 老年代标记-压缩

 注：GC线程执行时，应用程序线程暂停


####并行收集器

1.ParNew收集器

 - -XX:UseParNewGC
  - 新生代并行
  - 老年代串行

 - Serial收集器新生代的并行版本
 -复制算法
 -多线程，需要多核支持
 - -XX:ParallelGCThreads 限制线程数量

2.Parallel收集器

 - 类似ParNew
 - 新生代复制算法
 - 老年代 标记压缩
 - 更加关注吞吐量
 - -XX:+UseParallelGC
   使用Parallel收集器+老年代串行
 - -XX:UseParallelOldGC
   使用parallel收集器+并行老年代


####CMS收集器
 - Concurrent Mark Sweep 并发标记清除  //与用户线程一起执行
 - 标记-清除算法
 - 并发阶段会降低吞吐量
 - 老年代收集器（新生代主要用ParNew）
 - -XX:+UseConcMarkSweepGC


运行过程比较复杂，着重实现了标记的过程，可分为
 - 初始标记
  - 根可以直接关联到的对象
  - 速度块

 - 并发标记（和用户线程一起
  - 主要标记过程，标记全部对象

 - 重新标记
  - 由于并发标记时，用户线程依然运行，因此在正式清理前，再做修正

 - 并发清除（和用户线程一起
  -基于标记结果，清理对象

特点：

 - 尽可能的降低停顿
 - 会影响系统整体吞吐量和性能 //在GC阶段，会分出一部分cpu去做GC
 - 清理不彻底  //因为在清理阶段，用户线程还在运行，会产生新的垃圾，无法清理
 - 因为和用户线程一起运行，不能再空间快满的时候再清理 //若预留空间不够，可以使用串行收集器作为后备


