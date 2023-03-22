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
