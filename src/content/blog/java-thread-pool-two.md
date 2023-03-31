---
author: Maloong
pubDatetime: 2023-03-21T11:21:00Z
title: The ThreadPool of Java(II)
postSlug: java-thread-pool-practice
featured: true
draft: false
tags:
  - java
  - thread
ogImage: ""
description: Executors 是创建线程池的工具类，比较典型常见的四种线程池包括, newFixedThreadPool 、 newSingleThreadExecutor 、 newCachedThreadPool 、 newScheduledThreadPool。每一种都有自己特定的典型例子，可以按照每种的特 性用在不同的业务场景，也可以做为参照精细化创建线程池。但是一般大厂都不允许使用 Executors 创建线程池!这么创建的话，控制不好会出现 OOM。
---

Executors 是创建线程池的工具类，比较典型常见的四种线程池包括: newFixedThreadPool 、 newSingleThreadExecutor 、 newCachedThreadPool 、 newScheduledThreadPool。每一种都有自己特定的典型例子，可以按照每种的特 性用在不同的业务场景，也可以做为参照精细化创建线程池。
但是一般大厂都不允许使用 Executors 创建线程池!这么创建的话，控制不好会出现 OOM。

## Table of contents

## 四种线程池使用介绍

### newFixedThreadPool

```java
ExecutorService executorService = Executors.newFixedThreadPool(3);
```

![newFixedThreadPool 执行过程](https://s2.loli.net/2023/03/22/Dw1q9uZJcTbkp7L.png)

- 代码:new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>())
- 介绍:创建一个固定大小可重复使用的线程池，以 LinkedBlockingQueue 无 界阻塞队列存放等待线程。
- 风险:随着线程任务不能被执行的的无限堆积，可能会导致 OOM。

### newSingleThreadExecutor

```java
ExecutorService executorService = Executors.newSingleThreadExecutor();
```

![newSingleThreadExecutor 执行过程](https://s2.loli.net/2023/03/22/EaZI1R3VfLjKXAt.png)

- 代码:new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>())
- 介绍:只创建一个执行线程任务的线程池，如果出现意外终止则再创建一个。
- 风险:同样这也是一个无界队列存放待执行线程，无限堆积下会出现 OOM。

### newCachedThreadPool

```java
ExecutorService executorService = Executors.newCachedThreadPool();
```

![newCachedThreadPool 执行过程](https://s2.loli.net/2023/03/22/65gvcTm4IxXRN38.png)

- 代码:new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS, new SynchronousQueue<Runnable>())
- 介绍:首先 SynchronousQueue 是一个生产消费模式的阻塞任务队列，只要有 任务就需要有线程执行，线程池中的线程可以重复使用。
- 风险:如果线程任务比较耗时，又大量创建，会导致 OOM

### newScheduledThreadPool

```java
ScheduledExecutorService executorService = Executors.newScheduledThreadPool(1);
```

![newScheduledThreadPool 执行过程](https://s2.loli.net/2023/03/22/9vqlTDUKOXp7ZCf.png)

- 代码:public ScheduledThreadPoolExecutor(int corePoolSize) { super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS, new ScheduledThreadPoolExecutor.DelayedWorkQueue()); }
- 介绍:这就是一个比较有意思的线程池了，它可以延迟定时执行，有点像我们的定 时任务。同样它也是一个无限大小的线程池 Integer.MAX_VALUE。它提供的调用方法比较多，包括:scheduleAtFixedRate、scheduleWithFixedDelay，可以按需选择延迟执行方式。
- 风险:同样由于这是一组无限容量的线程池，所以依旧有 OOM 风险。

## 线程池使用场景说明

使用线程池可以降低服务器资源的投入， 让每台机器尽可能更大限度的使用 CPU。

线程池的使用会随着业务场景变化而不同，如果你的业务需要大量的使用 线程池，并非常依赖线程池，那么就不可能用 Executors 工具类中提供的方法。 因为这些线程池的创建都不够精细化，也非常容易造成 OOM 风险，而且随着业务 场景逻辑不同，会有 IO 密集型和 CPU 密集型。

最终，大家使用的线程池都是使用 new ThreadPoolExecutor() 创建的，当然也 有基于 Spring 的线程池配置 org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor。

可你想过吗，同样一个接口在有活动时候怎么办、有大促时候怎么办，可能你当 时设置的线程池是合理的，但是一到流量非常大的时候就很不适合了，所以如果 能动态调整线程池就非常有必要了。而且使用 new ThreadPoolExecutor() 方式 创建的线程池是可以通过提供的 set 方法进行动态调整的。有了这个动态调整的方法后，就可以把线程池包装起来，在配合动态调整的页面，动态更新线程池 参数，就可以非常方便的调整线程池了。

## 获取线程池监控信息

可监控内容

| 方法                  | 说明                                                                                              |
| --------------------- | ------------------------------------------------------------------------------------------------- |
| getActiveCount        | 线程池中正在执行任务的线程数量                                                                    |
| getCompletedTaskCount | 线程池已完成的任务数量，该值小于等于 taskCount                                                    |
| getCorePoolSize       | 线程池的核心线程数量                                                                              |
| getLargestPoolSize    | 线程池曾经创建过的最大线程数量。通过这个数据可以知 道线程池是否满过，也就是达到了 maximumPoolSize |
| getMaximumPoolSize    | 线程池的最大线程数量                                                                              |
| getPoolSize           | 线程池当前的线程数量                                                                              |
| getTaskCount          | 线程池已经执行的和未执行的任务总数                                                                |

#### 重写线程池方式监控

如果我们想监控一个线程池的方法执行动作，最简单的方式就是继承这个类，重 写方法，在方法中添加动作收集信息。

```java
public class ThreadPoolMonitor extends ThreadPoolExecutor {
  @Override
  public void shutdown() {
    // 统计已执行任务、正在执行任务、未执行任务数量
    super.shutdown();
  }
  @Override
  public List<Runnable> shutdownNow() {
    // 统计已执行任务、正在执行任务、未执行任务数量
    return super.shutdownNow();
  }
  @Override
  protected void beforeExecute(Thread t, Runnable r) {
    // 记录开始时间
  }
  @Override
  protected void afterExecute(Runnable r, Throwable t) {
    // 记录完成耗时
  }

}
```

#### 基于 JVMTI 方式监控

这块是监控的重点，因为我们不太可能让每一个需要监控的线程池都来重写的方 式记录，这样的改造成本太高了。

那么除了这个笨方法外，可以选择使用基于 JVMTI 的方式，进行开发监控组件。 JVMTI:JVMTI(JVM Tool Interface)位于 jpda 最底层，是 Java 虚拟机所提供的 native 编程接口。JVMTI 可以提供性能分析、debug、内存管理、线程分析等功 能。

基于 jvmti 提供的接口服务，运用 C++代码(win32-add_library)在 Agent_OnLoad 里开发监控服务，并生成 dll 文件。开发完成后在 java 代码中加入 agentpath， 这样就可以监控到我们需要的信息内容。
