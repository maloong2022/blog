---
author: Maloong
pubDatetime: 2023-03-20T22:58:00Z
title: The State of Java Thread
postSlug: java-thread-state
featured: false
draft: false
tags:
  - java
  - thread
ogImage: ""
description: Java 线程状态和状态的转化
---

Java 线程状态和状态的转化

## Table of contents

## Thread 状态关系

Java 的线程状态描述在枚举类 java.lang.Thread.State 中，共包括如下五种状态：

```java
public enum State { NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED; }
```

这五种状态描述了一个线程的生命周期，线程的状态是通过下图的方式进行流转的：

![](https://s2.loli.net/2023/03/21/e3zWIP26HKx7vOb.png)

- New ：新创建的一个线程，处于等待状态。
- Runnable ：可运行状态，并不是已经运行，具体的线程调度各操作系统决定。在
  Runnable 中包含了 Ready 、 Running 两个状态，当线程调用了 start() 方法
  后，线程则处于就绪 Ready 状态，等待操作系统分配 CPU 时间片，分配后则进
  入 Running 运行状态。此外当调用 yield() 方法后，只是 谦让 的允许当前线程让
  出 CPU ，但具体让不让不一定，由操作系统决定。如果让了，那么当前线程则会处
  于 Ready 状态继续竞争 CPU ，直至执行。
- Timed_waiting ：指定时间内让出 CPU 资源， 此时线程不会被执行，也不会被
  系统调度，直到等待时间到期后才会被执行。下列方法都可以触发：
  Thread.sleep 、 Object.wait 、 Thread.join 、
  LockSupport.parkNanos 、 LockSupport.parkUntil 。
- Wating ：可被唤醒的等待状态，此时线程不会被执行也不会被系统调度。此状态
  可以通过 synchronized 获得锁，调用 wait 方法进入等待状态。最后通过
  notify 、 notifyall 唤醒。下列方法都可以触发： Object.wait 、
  Thread .join 、 LockSupport.park 。
- Blocked ：当发生锁竞争状态下，没有获得锁的线程会处于挂起状态。例如
  synchronized 锁，先获得的先执行，没有获得的进入阻塞状态。
- Terminated ：这个是终止状态，从 New 到 Terminated 是不可逆的。一般是程
  序流程正常结束或者发生了异常。

## Thread 方法使用

一般情况下 Thread 中最常用的方法就是 start 启动，除此之外一些其他方法可能在平常的开发中用的不多，但这些方法在一些框架中却经常出现。

### yield

yield 方法让出 CPU，但不一定，一定让出！。这种可能会用在一些同时启动的线程中，按照优先级保证重要线程的执行，也可以是其他一些特殊的业务场景（例如这个线程内容很耗时，又不那么重要，可以放在后面）。

### wait & notifyall

wait 和 notify/nofityall，是一对方法，有一个等待，就会有一个叫醒，否则程序就夯在那不动了。

### join

join 是两个线程的合并吗？不是的！
join 是让线程进入 wait ，当线程执行完毕后，会在 JVM 源码中找到，它执行完毕后，其实执行 notify，也就是 等待 和 叫醒 操作。

```java
public class Test {

  public static void main(String[]args) throws InterruptedException{
    Thread thread = new Thread(()->{
      System.out.println("Thread before");
      try {
        Thread.sleep(3000);
      }catch(Exception e){
        e.printStackTrace();
      }
      System.out.println("Thread after");
      });
      thread.start();
      System.out.println("Main begin!");

      thread.join();
      System.out.println("Main end!");
  }

}

```

```bash
Main begin!
Thread before
Thread after
Main end!
```

首先 join() 是一个 synchronized 方法， 里面调用了 wait()，这个过程的目的是让持有这个同步锁的线程进入等待，那么谁持有了这个同步锁呢？答案是主线程，因为主线程调用了 threadA.join()方法，相当于在 threadA.join()代码这块写了一个同步代码块，谁去执行了这段代码呢，是主线程，所以主线程被 wait()了。然后在子线程 threadA 执行完毕之后，JVM 会调用 lock.notify_all(thread);唤醒持有 threadA 这个对象锁的线程，也就是主线程，会继续执行。

- 这部分验证的主要体现就是加了 thread.join() 后，会影响到输出结果。如
  果不加， main end 会优先 thread after 提前打印出来。
- join() 是一个 synchronized 方法 ，里面调用了 wait() 方法，让持有当前同步锁
  的线程进入等待状态，也就是主线程。当子线程执行完毕后，我们从源码中可以看
  到 JVM 调用了 lock.notify_all(thread) 所以唤醒了主线程继续执行。
