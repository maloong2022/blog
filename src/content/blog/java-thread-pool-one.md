---
author: Maloong
pubDatetime: 2023-03-21T02:00:00Z
title: The ThreadPool of Java(I)
postSlug: java-thread-pool
featured: true
draft: false
tags:
  - java
  - thread
ogImage: ""
description: Java自带线程池的原理和源码分析。
---

## Table of contents

### ThreadPoolExecutor

线程池的核心目的就是资源的利用，避免重复创建线程带来的资源消耗。因此引入一个池化技术的思想，避免重复创建、销毁带来的性能开销。

#### 手写一个线程池

![](https://s2.loli.net/2023/03/22/nWERD4AeihHyfOp.png)

这个手写线程池的实现也非常简单，只会体现出核心流程，包括：

1. 有 n 个一直在运行的线程，相当于我们创建线程池时允许的线程池大小。
2. 把线程提交给线程池运行。
3. 如果运行线程池已满，则把线程放入队列中。
4. 最后当有空闲时，则获取队列中线程进行运行。

```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.Executor;
import java.util.concurrent.atomic.AtomicInteger;

public class ThreadPoolTrader implements Executor {

    private final AtomicInteger ctl = new AtomicInteger(0);
    private volatile int corePoolSize;
    private volatile int maximumPoolSize;

    private final BlockingQueue<Runnable> workQueue;

    public ThreadPoolTrader(int corePoolSize, int maximumPoolSize, BlockingQueue<Runnable> workQueue) {
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
    }

    @Override
    public void execute(Runnable command) {
        int c = ctl.get();
        if (c < corePoolSize) {
            if (!addWorker(command)) {
                reject();
            }
            return;
        }
        if (!workQueue.offer(command)) {
            if (!addWorker(command)) {
                reject();
            }
        }
    }

    private boolean addWorker(Runnable firstTask) {
        if (ctl.get() >= maximumPoolSize) return false;

        Worker worker = new Worker(firstTask);
        worker.thread.start();
        ctl.incrementAndGet();
        return true;
    }

    private final class Worker implements Runnable {
        final Thread thread;
        Runnable firstTask;

        public Worker(Runnable firstTask) {
            this.thread = new Thread(this);
            this.firstTask = firstTask;
        }

        @Override
        public void run() {
            Runnable task = firstTask;
            try {
                while (task != null || (task = getTask()) != null) {
                    task.run();
                    if (ctl.get() > maximumPoolSize) {
                        break;
                    }
                    task = null;
                }
            } finally {
                ctl.decrementAndGet();
            }
        }

        private Runnable getTask() {
            for (; ; ) {
                try {
                    System.out.println("WorkQueue.size:" + workQueue.size());
                    return workQueue.take();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }


    }

    private void reject() {
        throw new RuntimeException("Error! ctl.count:" + ctl.get() + " WorkerQueue.size:" + workQueue.size());
    }

    public static void main(String[] args) {
        ThreadPoolTrader threadPoolTrader = new ThreadPoolTrader(2, 2, new ArrayBlockingQueue<>(10));
        for (int i = 0; i < 10; i++) {
            int finalI = i;
            threadPoolTrader.execute(() -> {
                try {
                    Thread.sleep(1500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("任务编号：" + finalI);
            });
        }
    }
}

```

主要功能逻辑包括：

- ctl ，用于记录线程池中线程数量。
- corePoolSize 、 maximumPoolSize ，用于限制线程池容量。
- workQueue ，线程池队列，也就是那些还不能被及时运行的线程，会被装入到这
  个队列中。
- execute ，用于提交线程，这个是通用的接口方法。在这个方法里主要实现的就
  是，当前提交的线程是加入到 worker 、队列还是放弃。
- addWorker ，主要是类 Worker 的具体操作，创建并执行线程。这里还包括了
  getTask() 方法，也就是从队列中不断的获取未被执行的线程。

这个代码有很多问题没有考虑，如线程池状态呢，不可能一直奔跑呀！？、线程池的锁呢，不会有并发问题吗？、线程池拒绝后的策略呢？，这些问题都没有在主流程解决，也正因为没有这些流程，所以上面的代码才更容易理解。

### 线程池源码分析

#### 类图

![](https://s2.loli.net/2023/03/22/xRQhEOTFGqXgHo1.png)

以围绕核心类 ThreadPoolExecutor 的实现展开的类之间实现和继承关系。

- 接口 Executor 、 ExecutorService ，定义线程池的基本方法。尤其是
  execute(Runnable command) 提交线程池方法。
- 抽象类 AbstractExecutorService ，实现了基本通用的接口方法。
- ThreadPoolExecutor ，是整个线程池最核心的工具类方法，所有的其他类和
  接口，为围绕这个类来提供各自的功能。
- Worker ，是任务类，也就是最终执行的线程的方法。
- RejectedExecutionHandler ，是拒绝策略接口，有四个实现类
  AbortPolicy( 抛异常方式拒绝 、 DiscardPolicy( 直接丢弃 、
  DiscardOldestPolicy( 丢弃存活时间最长的任务 、
  CallerRunsPolicy( 谁提交谁执行 。
- Executors ，是用于创建我们常用的不同策略的线程池
  newFixedThreadPool 、 newCachedThreadPool 、
  newScheduledThreadPool 、 newSingleThreadExecutor 。

#### 线程状态

![](https://s2.loli.net/2023/03/22/k7vzDa9SIyolciF.png)

```java
    private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
    private static final int COUNT_BITS = Integer.SIZE - 3;
    private static final int CAPACITY   = (1 << COUNT_BITS) - 1;

    // runState is stored in the high-order bits
    private static final int RUNNING    = -1 << COUNT_BITS;
    private static final int SHUTDOWN   =  0 << COUNT_BITS;
    private static final int STOP       =  1 << COUNT_BITS;
    private static final int TIDYING    =  2 << COUNT_BITS;
    private static final int TERMINATED =  3 << COUNT_BITS;
```

在 ThreadPoolExecutor 线程池实现类中，使用 AtomicInteger 类型的 ctl 记录线程池状态和线程池数量。在一个类型上记录多个值，它采用的分割数据区域，高 3 位记录状态，低 29 位存储线程数量，默认 RUNNING 状态，线程数为 0 个。

#### 线程池状态

![线程池状态流转](https://s2.loli.net/2023/03/22/K7VXzs9iapIZtbU.png)

线程池中的状态流转关系，包括如下状态：

- RUNNING ：运行状态，接受新的任务并且处理队列中的任务。
- SHUTDOWN ：关闭状态 调用了 shutdown 方法 。不接受新任务， 但是要处理队列
  中的任务。
- STOP ：停止状态 调用了 shutdownNow 方法 。不接受新任务，也不处理队列中的
  任务，并且要中断正在处理的任务。
- TIDYING ：所有的任务都已终止了 workerCount 为 0 ，线程池进入该状态后会调
  terminated() 方法进入 TERMINATED 状态。
- TERMINATED ：终止状态 terminated() 方法调用结束后的状态。

#### 提交线程（execute）

![提交线程流程图](https://s2.loli.net/2023/03/22/kW5XpsvxiVzdgBu.png)

```java
public void execute(Runnable command) {
        if (command == null)
            throw new NullPointerException();
        /*
         * Proceed in 3 steps:
         *
         * 1. If fewer than corePoolSize threads are running, try to
         * start a new thread with the given command as its first
         * task.  The call to addWorker atomically checks runState and
         * workerCount, and so prevents false alarms that would add
         * threads when it shouldn't, by returning false.
         *
         * 2. If a task can be successfully queued, then we still need
         * to double-check whether we should have added a thread
         * (because existing ones died since last checking) or that
         * the pool shut down since entry into this method. So we
         * recheck state and if necessary roll back the enqueuing if
         * stopped, or start a new thread if there are none.
         *
         * 3. If we cannot queue task, then we try to add a new
         * thread.  If it fails, we know we are shut down or saturated
         * and so reject the task.
         */
        int c = ctl.get();
        if (workerCountOf(c) < corePoolSize) {
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            if (! isRunning(recheck) && remove(command))
                reject(command);
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        else if (!addWorker(command, false))
            reject(command);
}
```

阅读这部分源码的时候，可以参考我们自己实现的线程池。其实最终的目的都是一样的，就是这段被提交的线程，启动执行、加入队列、决策策略，这三种方式。

- ctl.get() get()，取的是记录线程状态和线程个数的值，最终需要使用方法
  workerCountOf()workerCountOf()，来获取当前线程数量。 `workerCountOf 执行的是 c &
  CAPACITY 运算
- 根据当前线程池中线程数量，与核心线程数 corePoolSize 做对比，小于则进
  行添加线程到任务执行队列。
- 如果说此时线程数已满，那么则需要判断线程池是否为运行状态
  isRunning(c) 。如果是运行状态则把不能被执行的线程放入线程队列中。
- 放入线程队列以后，还需要重新判断线程是否运行以及移除操 作，如果非运行且移
  除，则进行拒绝策略。否则判断线程数量为 0 后添加新线程。
- 最后就是再次尝试添加任务执行，此时方法 addWorker 的第二个入参是 false
  最终会影响添加执行任务数量判断。如果添加失败则进行拒绝策略。

#### 添加执行任务(addWorker)

![添加执行任务逻辑流程](https://s2.loli.net/2023/03/22/whcMtX52E8x4olu.png)

1. 增加线程数量

```java
private boolean addWorker(Runnable firstTask, boolean core) {
        retry:
        for (;;) {
            int c = ctl.get();
            int rs = runStateOf(c);

            // Check if queue empty only if necessary.
            if (rs >= SHUTDOWN &&
                ! (rs == SHUTDOWN &&
                   firstTask == null &&
                   ! workQueue.isEmpty()))
                return false;

            for (;;) {
                int wc = workerCountOf(c);
                if (wc >= CAPACITY ||
                    wc >= (core ? corePoolSize : maximumPoolSize))
                    return false;
                if (compareAndIncrementWorkerCount(c))
                    break retry;
                c = ctl.get();  // Re-read ctl
                if (runStateOf(c) != rs)
                    continue retry;
                // else CAS failed due to workerCount change; retry inner loop
            }
        }
```

2. 创建启动线程

```java
 boolean workerStarted = false;
        boolean workerAdded = false;
        Worker w = null;
        try {
            w = new Worker(firstTask);
            final Thread t = w.thread;
            if (t != null) {
                final ReentrantLock mainLock = this.mainLock;
                mainLock.lock();
                try {
                    // Recheck while holding lock.
                    // Back out on ThreadFactory failure or if
                    // shut down before lock acquired.
                    int rs = runStateOf(ctl.get());

                    if (rs < SHUTDOWN ||
                        (rs == SHUTDOWN && firstTask == null)) {
                        if (t.isAlive()) // precheck that t is startable
                            throw new IllegalThreadStateException();
                        workers.add(w);
                        int s = workers.size();
                        if (s > largestPoolSize)
                            largestPoolSize = s;
                        workerAdded = true;
                    }
                } finally {
                    mainLock.unlock();
                }
                if (workerAdded) {
                    t.start();
                    workerStarted = true;
                }
            }
        } finally {
            if (! workerStarted)
                addWorkerFailed(w);
        }
        return workerStarted;
    }
```

添加执行任务的流程可以分为两块看，上面代码部分是用于记录线程数量、下面代码部分是在独占锁里创建执行线程并启动。这部分代码在不看锁、CAS 等操作，那么就和我们最开始手写的线程池基本一样了

- if (rs >= SHUTDOWN &&! (rs == SHUTDOWN &&firstTask = ==
  null &&! workQueue.isEmpty())) isEmpty()))，判断当前线程池状态，是否为
  SHUTDOWN 、 STOP 、 TIDYING 、 TERMINATED 中的一个。并且当前状态为
  SHUTDOWN 、且传入的任务为 null ，同时队列不为空。那么就返回 false 。
- compareAndIncrementWorkerCount CAS 操作，增加线程数量，成功就会
  跳出标记的循环体。
- runStateOf(c) != rs ，最后是线程池状态判断，决定是否循环。
- 在线程池数量记录成功后，则需要进入加锁环节，创建执行线程，并记录状态。在
  最后如果判断没有启动成功，则需要执行 addWorkerFailed 方法，剔除到线程方
  法等操作。

#### 执行线程(runWorker)

```java
final void runWorker(Worker w) {
        Thread wt = Thread.currentThread();
        Runnable task = w.firstTask;
        w.firstTask = null;
        w.unlock(); // allow interrupts
        boolean completedAbruptly = true;
        try {
            while (task != null || (task = getTask()) != null) {
                w.lock();
                // If pool is stopping, ensure thread is interrupted;
                // if not, ensure thread is not interrupted.  This
                // requires a recheck in second case to deal with
                // shutdownNow race while clearing interrupt
                if ((runStateAtLeast(ctl.get(), STOP) ||
                     (Thread.interrupted() &&
                      runStateAtLeast(ctl.get(), STOP))) &&
                    !wt.isInterrupted())
                    wt.interrupt();
                try {
                    beforeExecute(wt, task);
                    Throwable thrown = null;
                    try {
                        task.run();
                    } catch (RuntimeException x) {
                        thrown = x; throw x;
                    } catch (Error x) {
                        thrown = x; throw x;
                    } catch (Throwable x) {
                        thrown = x; throw new Error(x);
                    } finally {
                        afterExecute(task, thrown);
                    }
                } finally {
                    task = null;
                    w.completedTasks++;
                    w.unlock();
                }
            }
            completedAbruptly = false;
        } finally {
            processWorkerExit(w, completedAbruptly);
        }
    }
```

最核心的点就是 task.run() 让线程跑起来。额外再附带一些其他流程如下；

- beforeExecute 、 afterExecute ，线程执行的前后做一些统计信息。
- 另外这里的锁操作是 Worker 继承 AQS 自己实现的不可 重入的独占锁。

#### 队列获取任务(getTask)

```java
private Runnable getTask() {
        boolean timedOut = false; // Did the last poll() time out?

        for (;;) {
            int c = ctl.get();
            int rs = runStateOf(c);

            // Check if queue empty only if necessary.
            if (rs >= SHUTDOWN && (rs >= STOP || workQueue.isEmpty())) {
                decrementWorkerCount();
                return null;
            }

            int wc = workerCountOf(c);

            // Are workers subject to culling?
            boolean timed = allowCoreThreadTimeOut || wc > corePoolSize;

            if ((wc > maximumPoolSize || (timed && timedOut))
                && (wc > 1 || workQueue.isEmpty())) {
                if (compareAndDecrementWorkerCount(c))
                    return null;
                continue;
            }

            try {
                Runnable r = timed ?
                    workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS) :
                    workQueue.take();
                if (r != null)
                    return r;
                timedOut = true;
            } catch (InterruptedException retry) {
                timedOut = false;
            }
        }
    }
```

- getTask 方法从阻塞队列中获取等待被执行的任务，也就是一条条往出拿线程方
  法。
- if (rs >= SHUTDOWN ... ...，判断线程是否关闭。
- wc = workerCountOf(c) wc > corePoolSize ，如果工作线程数超过核
  心线程数量 corePoolSize 并且 workQueue 不为空，则增加工作线程。但如
  果超时未获取到线程，则会把大于 corePoolSize 的线程销毁掉。
- timed ，是 allowCoreThreadTimeOut 得来的。最终 timed 为 true 时，
  则通过阻塞队列的 poll 方法进行超时控制。
- 如果在 keepAliveTime 时间内没有获取到任务，则返回 null 。如果为 false
  则阻塞。
