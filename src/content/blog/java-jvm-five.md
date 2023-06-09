---
author: Maloong
pubDatetime: 2023-03-23T12:51:00Z
title: The JVM(IIIII) -- GC垃圾回收
postSlug: java-jvm-gc
featured: false
draft: false
tags:
  - java
  - jvm
ogImage: ""
description: "垃圾收集（Garbage Collection，简称GC），最早于1960年诞生于麻省理工学院的Lisp是第一门开始使用内存动态分配和垃圾收集技术的语言。"
---

垃圾收集（Garbage Collection，简称 GC），最早于 1960 年诞生于麻省理工学院的 Lisp 是第一门开始使用内存动态分配和垃圾收集技术的语言。

垃圾收集器主要做的三件事：哪些内存需要回收、什么时候回收、怎么回收。而从垃圾收集器的诞生到现在有半个世纪的发展，现在的内存动态分配和内存回收技术已经非常成熟，一切看起来都进入了“自动化”。但在某些时候还是需要我们去监测在高并发的场景下，是否有内存溢出、泄漏、GC 时间过程等问题。所以在了解和知晓垃圾收集的相关知识对于高级程序员的成长就非常重要。

## Table of contents

## JVM 垃圾回收知识框架

垃圾收集器的核心知识项主要包括：判断对象是否存活、垃圾收集算法、各类垃圾收集器以及垃圾回收过程。如下图；

![垃圾收集器知识框架](https://s2.loli.net/2023/03/23/pRcVC579kK3uHUw.png)

### 判断对象已死

#### 引用计数器

1. 为每一个对象添加一个引用计数器，统计指向该对象的引用次数。
2. 当一个对象有相应的引用更新操作时，则对目标对象的引用计数器进行增减。
3. 一旦当某个对象的引用计数器为 0 时，则表示此对象已经死亡，可以被垃圾回收。

从实现来看，引用计数器法（Reference Counting）虽然占用了一些额外的内存空间来进行计数，但是它的实现方案简单，判断效率高，是一个不错的算法。
也有一些比较出名的引用案例，比如：微软 COM（Component Object Model） 技术、使用 ActionScript 3 的 FlashPlayer、 Python 语言等。

但是，在主流的 Java 虚拟机中并没有选用引用技术算法来管理内存，主要是因为这个简单的计数方式在处理一些相互依赖、循环引用等就会非常复杂。可能会存在不再使用但又不能回收的内存，造成内存泄漏。

#### 可达性分析法

Java、C#等主流语言的内存管理子系统，都是通过可达性分析（Reachability Analysis）算法来判定对象是否存活的。

它的算法思路是通过定义一系列称为 GC Roots 根对象作为起始节点集，从这些节点出发，穷举该集合引用到的全部对象填充到该集合中（live set）。这个过程叫做标记，只标记那些存活的对象，那么现在未被标记的对象就是可以被回收了。

GC Roots 包括：

1. 全局性引用，对方法区的静态对象、常量对象的引用。
2. 执行上下文，对 Java 方法栈帧中的局部对象引用、对 JNI handles 对象引用。
3. 已启动且未停止的 Java 线程。

两大问题：

1. 误报：已死亡对象被标记为存活，垃圾收集不到 。多占用一会内存，影响较小。
2. 漏报：引用的对象（正在使用的）没有被标记为存活，被垃圾回收了。那么直接导致的就是 JVM 奔溃。（ STW 可以确保可达性分析法的准确性，避免漏报）

### 垃圾回收算法

#### 标记-清除算法(mark-sweep)

![标记清除算法 (mark sweep)](https://s2.loli.net/2023/03/23/VMF6kBCS3Aax7ow.png)

- 标记无引用的死亡对象所占据的空闲内存，并记录到空闲列表中（ free list ）。
- 当需要创建新对象时，内存管理模块会从 free list 中寻找空闲内存，分配给新建的对象。
- 这种清理方式其实非常简单高效，但是也有一个问题内存碎片化太严重了。
- Java 虚拟机的堆中对象 ，必须是连续分布的，所以极端的情况下可能即使总剩余内存充足，但寻找连续内存分配效率低，或者严重到无法分配内存。 重启汤姆猫！
- 在 CMS 中有此类算法的使用， GC 暂停时间短，但存在算法缺陷。

#### 标记-复制算法(mark-copy)

![标记复制算法 (mark copy)](https://s2.loli.net/2023/03/23/65Bwg3Eso7FWYmP.png)

- 从图上看这回做完垃圾清理后连续的内存空间就大了。
- 这种方式是把内存区域分成两份，分别用两个指针 from 和 to 维护，并且只使用 from 指 针指向的内存区域分配内存。
- 当发生垃圾回收时，则把存活对象复制到 to 指针指向的内存区域，并交换 from 与 to 指针。
- 它的好处很明显，就是解决内存碎片化问题。但也带来了其他问题，堆空间浪费了一半。

#### 标记-压缩算法(mark-compact)

![标记压缩算法 (mark compact)](https://s2.loli.net/2023/03/23/4UMhLPGA6YC2iWq.png)

- 1974 年， Edward Lueders 提出了标记 压缩算法，标记的过程和标记清除算法一样，但在后续对象清理步骤中，先把存活对象都向内存空间一端移动，然后在清理掉其他内存空间。
- 这种算法能够解决内存碎片化问题，但压缩算法的性能开销也不小。

### 垃圾回收器

![GC堆](https://s2.loli.net/2023/03/22/JYDxsnWydhv251V.png)

#### 新生代

1. Serial

   - 算法： 标记-复制算法
   - 说明： 简单高效的单核机器，Client 模式下默认新生代收集器

2. Parallel ParNew

   - 算法：标记-复制算法
   - 说明： GC 线程并行版本，在单 CPU 场景下效果不突出。常用于 Client 模式下的 JVM

3. Parallel Scavenge
   - 算法： 标记-复制算法
   - 说明：目标在于达到可控吞吐量（吞吐量=用户代码运行时间/（用户代码运行时间+垃圾回收时间））

#### 老年代

1. Serial Old

   - 算法：标记-压缩算法
   - 说明： 性能一般，单线程版本。1.5 之前与 Parallel Scavenge 配合使用；作为 CMS 的后备预案

2. Paralled Old

   - 算法：标记-压缩算法
   - 说明： GC 多线程并行，为了替代 Serial Old 与 Parllel Scavenge 配合使用

3. CMS
   - 算法：标记-清除算法
   - 说明：对 CPU 资源敏感，停顿时间长。标记-清除算法，会产生内存碎片，可以通过参数开启碎片的合并整理。基本已被 G1 取代

#### G1

- 算法： 标记-压缩算法
- 说明：适用于多核大内存机器，GC 多线程并行执行，低停顿，高回收率
