---
author: Maloong
pubDatetime: 2023-03-22T16:45:00Z
title: The JVM(IIII) -- JVM故障处理工具
postSlug: java-jvm-error-tools
featured: false
draft: false
tags:
  - java
  - jvm
ogImage: ""
description: 虽然实际的业务使用中很少通过指令去监控 JVM 而是有一整套的非入侵全链路监控，在监控服务里与之方法调用时的 JVM 一并监控，可以让研发人员更快速的排查问题。但这些工具的实现依然是需要这些基础，在有了基础的知识掌握后，可以更好多使用工具。
---

虽然实际的业务使用中很少通过指令去监控 JVM 而是有一整套的非入侵全链路监控，在监控服务里与之方法调用时的 JVM 一并监控，可以让研发人员更快速的排查问题。但这些工具的实现依然是需要这些基础，在有了基础的知识掌握后，可以更好多使用工具。

![JVM故障处理工具](https://s2.loli.net/2023/03/22/b3nl6aMIxAwEWDk.png)

## Table of contents

## jps 虚拟机进程状况

jps（JVM Process Status Tool），它的功能与 ps 命令类似，可以列出正在运行的虚拟机进程，并显示虚拟机执行主类（Main Class，main()函数所在的类）名称以及这些进程的本地虚拟机唯一 ID（Local Virtual Machine Identifier,LVMID），类似于 ps -ef | grep java 的功能。

### 命令格式

jps [ options ] [ hostid ]

- options ：选项、参数，不同的参数可以输出需要的信息
- hostid ：远程查看

#### 选项列表

| 选项 | 描述                                             |
| ---- | ------------------------------------------------ |
| -q   | 只输出进程 ID ，忽略主类信息                     |
| -l   | 输出主类全名，或者执行 JAR 包则输出路径          |
| -m   | 输出虚拟机进程启动时传递给主类 main() 函数的参数 |
| -v   | 输出虚拟机进程启动时的 JVM 参数                  |

### jps -lv 127.0.0.1，输出远程机器信息

jps 链接远程输出 JVM 信息，需要注册 RMI，否则会报错 RMI Registry not available at 127.0.0.1。
注册 RMI 开启 jstatd 在你的 C:\Program Files\Java\jdk1.8.0_161\bin 目录下添加名称为 jstatd.all.policy 的文件。
jstatd.all.policy 文件内容如下：

```bash
grant codebase "file:${java.home}/../lib/tools.jar" { permission java.security.AllPermission; };
```

添加好配置文件后，在 bin 目录下注册添加的 jstatd.all.policy 文件

```bash
C:\Program Files\Java\jdk1.8.0_161\bin>jstatd -J-Djava.security.policy=jstatd.all.policy
```

顺利的话现在就可以查看原创机器 JVM 信息了。

## jcmd 虚拟机诊断命令

jcmd，是从 jdk1.7 开始新发布的 JVM 相关信息诊断工具，可以用它来导出堆和线程信息、查看 Java 进程、执行 GC、还可以进行采样分析（jmc 工具的飞行记录器）。注意其使用条件是只能在被诊断的 JVM 同台 sever 上，并且具有相同的用户和组(user and group).

### 命令格式

jcmd <pid | main class> <command ...|PerfCounter.print|-f file>

- pid ，接收诊断命令请求的进程 ID
  - main class ，接收诊断命令请求的进程 main 类。
- command ，接收诊断命令请求的进程 main 类。
- PerfCounter.print ，打印目标 Java 进程上可用的性能计数器。
- -f file ，从文件 file 中读取命令，然后在目标 Java 进程上调用这些命令。
- -l ，查看所有进程列表信息。
- -h 、 -help ，查看帮助信息。

### jcmd pid VM.flags,查看 JVM 启动参数

```bash
jcmd 54465 VM.flags

54465:
-XX:CICompilerCount=2 -XX:CompileCommand=exclude,com/intellij/openapi/vfs/impl/FileP
artNodeRoot,trieDescend -XX:ConcGCThreads=2 -XX:ErrorFile=/Users/andy/java_error_in_
idea_%p.log -XX:G1ConcRefinementThreads=8 -XX:G1EagerReclaimRemSetThreshold=16 -XX:G
1HeapRegionSize=2097152 -XX:GCDrainStackTargetSize=64 -XX:+HeapDumpOnOutOfMemoryErro
r -XX:HeapDumpPath=/Users/andy/java_error_in_idea.hprof -XX:+IgnoreUnrecognizedVMOpt
ions -XX:InitialHeapSize=134217728 -XX:MarkStackSize=4194304 -XX:MaxHeapSize=4294967
296 -XX:MaxNewSize=2575302656 -XX:MinHeapDeltaBytes=2097152 -XX:MinHeapSize=13421772
8 -XX:NonNMethodCodeHeapSize=5826188 -XX:NonProfiledCodeHeapSize=265522362 -XX:-Omit
StackTraceInFastThrow -XX:ProfiledCodeHeapSize=265522362 -XX:ReservedCodeCacheSize=5
36870912 -XX:+SegmentedCodeCache -XX:SoftMaxHeapSize=4294967296 -XX:SoftRefLRUPolicy
MSPerMB=50 -XX:SweeperThreshold=0.234375 -XX:+UseCompressedClassPointers -XX:+UseCom
pressedOops -XX:+UseFastUnorderedTimeStamps -XX:+UseG1GC -XX:-UseNUMA -XX:-UseNUMAIn
terleaving
```

### jcmd pid VM.uptime，查看 JVM 运行时长

```bash
jcmd 54465 VM.uptime

54465:
18974.783 s
```

### jcmd pid PerfCounter.print，查看 JVM 性能相关参数

```bash
jcmd 54465 PerfCounter.print
```

### jcmd pid GC.class_histogram，查看系统中类的统计信息

```bash
jcmd 37952 GC.class_histogram
37952:

num     #instances         #bytes  class name
----------------------------------------------
   1:          1376         155440  [C
   2:           585          66984  java.lang.Class
   3:            84          64776  [B
   4:          1362          32688  java.lang.String
   5:           591          30792  [Ljava.lang.Object;
   6:           113           8136  java.lang.reflect.Field
   7:            88           5632  java.net.URL
   8:           106           4240  java.lang.ref.SoftReference
   9:           256           4096  java.lang.Integer
...
```

### jcmd pid Thread.print，查看线程堆栈信息

```bash
jcmd 37952 Thread.print
```

### jcmd pid VM.system_properties，查看 JVM 系统参数

```bash
jcmd 37952 VM.system_properties
```

### jcmd pid GC.heap_dump 路径，导出 heap dump 文件

```bash
jcmd 37952 GC.heap_dump ~/_dump_0110
```

### jcmd pid help，列出可执行操作

### jcmd pid help JFR.stop，查看命令使用

## jinfo Java 配置信息工具

jinfo（Configuration Info for Java），实时查看和调整 JVM 的各项参数。
在上面讲到 jps -v 指令时，可以看到它把虚拟机启动时显式的参数列表都打印出来了，但如果想更加清晰的看具体的一个参数或者想知道未被显式指定的参数时，就可以通过 jinfo -flag 来查询了。

### 命令格式

jinfo [ option ] pid

### 使用方式

```bash
jinfo -flag MetaspaceSize 111552

jinfo -flag MaxMetaspaceSize 111552

jinfo -flag HeapDumpPath 111552
```

## jstat 收集虚拟机运行数据

jstat（JVM Statistics Monitoring Tool），用于监视虚拟机各种运行状态信息。它可以查看本地或者远程虚拟机进程中，类加载、内存、垃圾收集、即时编译等运行时数据。

### 命令格式

jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

- vmid ：如果是查看远程机器，需要按照 此格式：
  [protocol:][//]lvmid[@hostname[:port]/
- interval 和 count ，表示查询间隔和次数，比如每隔 1000 毫秒查询一次进程 ID 的
  gc 收集情况，每次查询 5 次。 jstat gc 111552 1000 5

### 选项列表

| 选项              | 描述                                                                                                                          |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| -class            | 监视类加载、卸载数量、总空间以及类装载所耗费时长                                                                              |
| -gc               | 监视 Java 堆情况，包括 Eden 区、 2 个 Survivor 区、老年代、永久代或者 jdk1.8 元空间等，容量、已用空间、垃圾收集时间合计等信息 |
| -gccapacity       | 监视内容与 gc 基本一致，但输出主要关注 Java 堆各个区域使用到的最大、最小空间                                                  |
| -gcutil           | 监视内容与 gc 基本相同，但输出主要关注已使用空间占总空间的百分比                                                              |
| -gccause          | 与 gcutil 功能一样，但是会额外输出导致上一次垃圾收集产生的原因                                                                |
| -gcnew            | 监视新生代垃圾收集情况                                                                                                        |
| -gcnewcapacity    | 监视内容与 gcnew 基本相同，输出主要关注使用到的最大、最小空间                                                                 |
| -gcold            | 监视老年代垃圾收集情况                                                                                                        |
| -gcoldcapacity    | 监视内容与 gcold 基本相同，输出主要关注使用到的最大、最小空间                                                                 |
| -compiler         | 输出即时编译器编译过的方法、耗时等信息                                                                                        |
| -printcompilation | 输出已经被即时编译的方法                                                                                                      |
| -gcpermcapacity   | jdk1.7 及以下，永久代空间统计                                                                                                 |
| -gcmetacapacity   | jdk1.8 ，元空间统计                                                                                                           |

jstat 的监视选项还是非常多的，但最常用的主要有上面这些。

### jstat -class，类加载统计

```bash
jstat -class 37952

Loaded  Bytes  Unloaded  Bytes     Time
602  1209.2        0     0.0       0.11
```

- Loaded ，加载 class 的数量
- Bytes ：所占用空间大小
- Unloaded ：未加载数量
- Bytes ：未加载占用空间
- Time ：时间

### jstat -compiler，编译统计

```bash
jstat -compiler 37952

Compiled Failed Invalid   Time   FailedType FailedMethod
    73      0       0     0.03          0
```

- Compiled ：编译数量
- Failed ：失败数量
- Invalid ：不可用数量
- Time ：时间
- FailedType ：失败类型
- FailedMethod ：失败方法

### jstat -gc，垃圾回收统计

```bash
jstat -gc 37952

S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT
10752.0 10752.0  0.0    0.0   65536.0   3932.2   175104.0     0.0     4480.0 779.9  384.0   76.4       0    0.000   0      0.000    0.000
```

- S0C 、 S1C ，第一个和第二个幸存区大小
- S0U 、 S1U ，第一个和第二个幸存区使用大小
- EC 、 EU ，伊甸园的大小和使用
- OC 、 OU ，老年代的大小和使用
- MC 、 MU ，方法区的大小和使用
- CCSC 、 CCSU ，压缩类空间大小和使用
- YGC 、 YGCT ，年轻代垃圾回收次数和耗时
- FGC 、 FGCT ，老年代垃圾回收次数和耗时
- GCT ，垃圾回收总耗时

### jstat -gccapacity，堆内存统计

```bash
jstat -gccapacity 37952

NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC       MCMN     MCMX      MC     CCSMN    CCSMX     CCSC    YGC    FGC
87040.0 1397760.0  87040.0 10752.0 10752.0  65536.0   175104.0  2796544.0   175104.0   175104.0      0.0 1056768.0   4480.0      0.0 1048576.0    384.0      0     0
```

- NGCMN 、 NGCMX ，新生代最小和最大容量
- NGC ，当前新生代容量
- S0C 、 S1C ，第一和第二幸存区大小
- EC ，伊甸园区的大小
- OGCMN 、 OGCMX ，老年代最小和最大容量
- OGC 、 OC ，当前老年代大小
- M CMN 、 MCMX ，元数据空间最小和最大容量
- MC ，当前元空间大小
- CCSMN 、 CCSMX ，压缩类最小和最大空间
- YGC ，年轻代 GC 次数
- FGC ，老年代 GC 次数

### jstat -gcnewcapacity，新生代内存统计

```bash
jstat -gcnewcapacity 37952

NGCMN      NGCMX       NGC      S0CMX     S0C     S1CMX     S1C       ECMX        EC      YGC   FGC
87040.0  1397760.0    87040.0 465920.0  10752.0 465920.0  10752.0  1396736.0    65536.0     0     0
```

- NGCMN 、 NGCMX ，新生代最小和最大容量
- NGC ，当前新生代容量
- S0CMX ，最大幸存 0 区大小
- S0C ，当前幸存 0 区大小
- S1CMX ，最大幸存 1 区大小
- S1C ，当前幸存 1 区大小
- ECMX ，最大伊甸园区大小
- EC ，当前伊甸园区大小
- YGC ，年轻代垃圾回收次数
- FGC ，老年代回收次数

### jstat -gcnew，新生代垃圾回收统计

```bash
jstat -gcnew 37952

S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT
10752.0 10752.0    0.0    0.0 15  15    0.0  65536.0   3932.2      0    0.000
```

- S0C 、 S1C ，第一和第二幸存区大小
- S0U 、 S1U ，第一和第二幸存区使用
- TT ，对象在新生代存活的次数
- MTT ，对象在新生代存活的最大次数
- DSS ：期望的幸存区大小
- EC ，伊甸园区的大小
- EU ，伊甸园区的使用
- YGC ，年轻代垃圾回收次数
- YGCT ，年轻代垃圾回收消耗时间

### jstat -gcold，老年代垃圾回收统计

```bash
jstat -gcold 37952

MC       MU      CCSC     CCSU       OC          OU       YGC    FGC    FGCT     GCT
4480.0    779.9    384.0     76.4    175104.0         0.0      0     0    0.000    0.000
```

- MC 、 MU ，方法区的大小和使用
- CCSC 、 CCSU ，压缩类空间大小和使用
- OC 、 OU ，老年代大小和使用
- YGC ，年轻代垃圾回收次数
- FGC ，老年代垃圾回收次数
- FGCT ，老年代垃圾回收耗时
- GCT ，垃圾回收总耗时

### jstat -gcoldcapacity，老年代内存统计

```bash
jstat -gcoldcapacity 37952

OGCMN       OGCMX        OGC         OC       YGC   FGC    FGCT     GCT
175104.0   2796544.0    175104.0    175104.0     0     0    0.000    0.000

```

- OGCMN 、 OGCMX ，老年代最小和最大容量
- OGC ，当前老年代大小
- OC ，老年代大小
- YGC ，年轻代垃圾回收次数
- FGC ，老年代垃圾回收次数
- FGCT ，老年代垃圾回收耗时
- GCT ，垃圾回收消耗总耗时

### jstat -gcmetacapacity，元空间统计

```bash
jstat -gcmetacapacity 37952

MCMN       MCMX        MC       CCSMN      CCSMX       CCSC     YGC   FGC    FGCT     GCT
0.0  1056768.0     4480.0        0.0  1048576.0      384.0     0     0    0.000    0.000

```

- MCMN 、 MCMX ，元空间最小和最大容量
- MC ，当前元数据空间大小
- CCSMN 、 CCSMX ，压缩类最小和最大空间
- CCSC ，压缩类空间大小
- YGC ，年轻代垃圾回收次数
- FGC ，老年代垃圾回收次数
- FGCT ，老年代垃圾回收耗时
- GCT ，垃圾回收消耗总耗时

### jstat -gcutil，垃圾回收统计

```bash
jstat -gcutil 37952

S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
0.00   0.00   6.00   0.00  17.41  19.90      0    0.000     0    0.000    0.000
```

- S0 、 S1 、幸存 1 区和 2 区，当前使用占比
- E ，伊甸园区使用占比
- O ，老年代区使用占比
- M ，元数据区使用占比
- CCS ，压缩类使用占比
- YGC ，年轻代垃圾回收次数
- FGC ，老年代垃圾回收次数
- FGCT ，老年代垃圾回收耗时
- GCT ，垃圾回收消耗总耗时

### jstat -printcompilation，JVM 编译方法统计

```bash
jstat -printcompilation 37952

Compiled  Size  Type Method
    30     83    1 java/lang/String <init>
```

- Compiled ：最近编译方法的数量
- Size ：最近编译方法的字节码数量
- Type ：最近编译方法的编译类型
- Method ：方法名标识

## jmap 内存映射工具

jmap（Memory Map for Java），用于生成堆转储快照（heapdump 文件）。
jmap 的作用除了获取堆转储快照，还可以查询 finalize 执行队列、Java 堆和方法区的详细信息。

### 命令格式

jmap [ option ] pid

- option ：选项参数
- pid ：需要打印配置信息的进程 ID
- executable ：产生核心 dump 的 Java 可执行文件
- core ：需要打印配置信息的核心文件
- server id ：可选的唯一 id ，如果相同的远程主机上运行了多台调试服务器，用此选
  项参数标识服务 器
- remote server IP or hostname 远程调试服务

### 选项列表

| 选项           | 描述                                                                       |
| -------------- | -------------------------------------------------------------------------- |
| -dump          | 生成 Java 堆转储快照。                                                     |
| -finalizerinfo | 显示在 F Queue 中等待 Finalizer 线程执行 finalize 方法的对象。 Linux 平台  |
| -heap          | 显示 Java 堆详细信息，比如：用了哪种回收器、参数配置、分代情况。Linux 平台 |
| -histo         | 显示堆中对象统计信息，包括类、实例数量、合计容量                           |
| -permstat      | 显示永久代内存状态， jdk1.7 ，永久代                                       |
| -F             | 当虚拟机进程对 dump 选项没有响应式，可以强制生成快照。 Linux 平台          |

### jmap，打印共享对象映射

```bash
jmap 55669

Attaching to process ID 55669, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.161-b12
...

```

### jmap -heap，堆详细信息

```bash
jmap -heap 55669

Attaching to process ID 55669, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.161-b12

using thread-local object allocation.
Parallel GC with 8 thread(s)
Heap Configuration:
    MinHeapFreeRatio = 0
    MaxHeapFreeRatio = 100
    MaxHeapSize = 268435456 (256.0MB)
    NewSize = 8388608 (8.0MB)
    MaxNewSize = 89128960 (85.0MB)
    OldSize = 16777216 (16.0MB)
    NewRatio = 2
    SurvivorRatio = 8
    MetaspaceSize = 21807104 (20.796875MB)
    CompressedClassSpaceSize = 1073741824 (1024.0MB)
    MaxMetaspaceSize = 17592186044415 MB
    G1HeapRegionSize = 0 (0.0MB)

```

### jmap -clstats，打印加载类

```bash
jmap -clstats 55669

Attaching to process ID 55669, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.161-b12
finding class loader instances ..done.
computing per loader stat ..done.
please wait.. computing liveness........................................................
.........liveness analysis may be inaccurate ... class_loader classes bytes parent_loader alive? type
<bootstrap> 3779 6880779 null live <internal> 0x00000000f03853b8 57 132574 0x00000000f031aac8 live org/netbeans/StandardModule$OneModuleClassLoader@0x00000001001684f0
0x00000000f01b9b98 0 0 0x00000000f031aac8 live org/netbeans/StandardModule$OneModuleClassLoader@0x00000001001684f0
0x00000000f005b280 0 0 0x00000000f031aac8 live java/util/ResourceBundle$RBClassLoader@0x00000001000c6ae0
0x00000000f01dfa98 0 0 0x00000000f031aac8 live org/netbeans/StandardModule$OneModuleClassLoader@0x00000001001684f0
0x00000000f01ec518 79 252894 0x00000000f031aac8 live org/netbeans/StandardModule$OneModuleClassLoader@0x00000001001684f0

```

### jmap -dump，堆转储文件

```bash
jmap -dump:live,format=b,file=C:/Users/xiaofuge/Desktop/heap.bin 55669

Dumping heap to ~/heap.bin ...
Heap dump file created
```

## jhat 堆转储快照分析工具

jhat（JVM Heap Analysis Tool），与 jmap 配合使用，用于分析 jmap 生成的堆转储快照。

jhat 内置了一个小型的 http/web 服务器，可以把堆转储快照分析的结果，展示在浏览器中查看。不过用途不大，基本大家都会使用其他第三方工具。

### 命令格式

jhat [-stack <bool>] [-refs <bool>] [-port <port>] [-baseline <file>] [-debug <int>] [-version] [-h|-help] <file>

### 命令使用

```bash
jhat -port 8090 ~/heap.bin

Reading from ~/heap.bin...
Dump file created Wed Jan 13 16:53:47 CST 2022
Snapshot read, resolving...
Resolving 246455 objects...
Chasing references, expect 49 dots.................................................
Eliminating duplicate references.................................................
Snapshot resolved. Started HTTP server on port 8090 Server is ready.
```

打开浏览器 http://localhost:8090/

## jstack Java 堆栈跟踪工具

jstack（Stack Trace for Java），用于生成虚拟机当前时刻的线程快照（threaddump、javacore）。

线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合，生成线程快照的目的通常是定位线程出现长时间停顿的原因，如：线程死锁、死循环、请求外部资源耗时较长导致挂起等。

线程出现停顿时通过 jstack 来查看各个线程的调用堆栈，就可以获得没有响应的线程在搞什么鬼。

### 命令格式

jstack [ option ] vmid

### 选项参数

| 选项 | 描述                                            |
| ---- | ----------------------------------------------- |
| -F   | 当正常输出的请求不被响应时，强制输出线程堆栈    |
| -l   | 除了堆栈外，显示关于锁的附加信息                |
| -m   | 如果调用的是本地方法的话，可以显示 c/c++ 的堆栈 |

### 命令使用

```bash
jstack 63946

2023-03-23 01:29:52
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.211-b12 mixed mode):

"Attach Listener" #11 daemon prio=9 os_prio=31 tid=0x00007f939580d000 nid=0x3207 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Service Thread" #10 daemon prio=9 os_prio=31 tid=0x00007f9396062000 nid=0x4903 runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C1 CompilerThread3" #9 daemon prio=9 os_prio=31 tid=0x00007f9394057000 nid=0x4603 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread2" #8 daemon prio=9 os_prio=31 tid=0x00007f939487b000 nid=0x4a03 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread1" #7 daemon prio=9 os_prio=31 tid=0x00007f9394862000 nid=0x4403 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread0" #6 daemon prio=9 os_prio=31 tid=0x00007f9392847000 nid=0x4c03 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Monitor Ctrl-Break" #5 daemon prio=5 os_prio=31 tid=0x00007f9392845000 nid=0x4203 runnable [0x0000700007b92000]
   java.lang.Thread.State: RUNNABLE
        at java.net.SocketInputStream.socketRead0(Native Method)
        at java.net.SocketInputStream.socketRead(SocketInputStream.java:116)
        at java.net.SocketInputStream.read(SocketInputStream.java:171)
        at java.net.SocketInputStream.read(SocketInputStream.java:141)
        at sun.nio.cs.StreamDecoder.readBytes(StreamDecoder.java:284)
        at sun.nio.cs.StreamDecoder.implRead(StreamDecoder.java:326)
        at sun.nio.cs.StreamDecoder.read(StreamDecoder.java:178)
        - locked <0x000000076ac84448> (a java.io.InputStreamReader)
        at java.io.InputStreamReader.read(InputStreamReader.java:184)
        at java.io.BufferedReader.fill(BufferedReader.java:161)
        at java.io.BufferedReader.readLine(BufferedReader.java:324)
        - locked <0x000000076ac84448> (a java.io.InputStreamReader)
        at java.io.BufferedReader.readLine(BufferedReader.java:389)
        at com.intellij.rt.execution.application.AppMainV2$1.run(AppMainV2.java:56)
...
```

在验证使用的过程中，可以尝试写一个死循环的线程，之后通过 jstack 查看线程信息。

## 可视化故障处理工具

### console，Java 监视与管理控制台

JConsole（ Java Monitoring and Management Console），是一款基于 JMX（ Java Manage-ment Extensions） 的可视化监视管理工具。

它的功能主要是对系统进行收集和参数调整，不仅可以在虚拟机本身管理还可以开发在软件上，是开放的服务，有相应的代码 API 调用。

```bash
jconsole
```

### VisualVM，多合故障处理工具

VisualVM（ All-in-One Java Troubleshooting Tool），是功能最强大的运行监视和故障处理工具之一。

它除了常规的运行监视、故障处理外，还可以做性能分析等工作。因为它的通用性很强，对应用程序影响较小，所以可以直接接入到生产环境中。

![VisualVM](https://visualvm.github.io/)
