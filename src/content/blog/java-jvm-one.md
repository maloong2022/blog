---
author: Maloong
pubDatetime: 2023-03-22T14:21:00Z
title: The JVM(I)
postSlug: java-jvm
featured: false
draft: false
tags:
  - java
  - jvm
ogImage: ""
description: Oracle has two products that implement Java Platform Standard Edition(Java SE) 8, Java SE Development Kit (JDK) 8 and Java SE Runtime Environment (JRE) 8.
---

Oracle has two products that implement Java Platform Standard Edition
(Java SE) 8: Java SE Development Kit (JDK) 8 and Java SE Runtime
Environment (JRE) 8.
JDK 8 is a superset of JRE 8, and contains everything that is in JRE
8, plus tools such as the compilers and debuggers necessary for
developing applets and applications. JRE 8 provides the libraries, the
Java Virtual Machine (JVM), and other components to run applets and
applications written in the Java programming language. Note that the
JRE includes components not required by the Java SE specification, including both standard and non-standard Java components.
The following conceptual diagram illustrates the components of Oracle's Java SE products:
Description of Java Conceptual Diagram

![Java Platform Standard Edition 8 Documentation](https://s2.loli.net/2023/03/22/UQ6daiHnceyRfKI.png)

## Table of contents

## Java 平台标准(JDK 8)

关于 JDK、JRE、JVM 之间是什么关系，在 Java 平台标准中已经明确定义了。 也就是上面的英文介绍部分。

- Oracle 有两个 Java 平台标准的产品，Java SE 开发工具包(JDK) 和 Java SE 运行 时环境(JRE)。
- JDK(Java Development Kit Java 开发工具包)，JDK 是提供给 Java 开发人员使用的， 其中包含了 java 的开发工具，也包括了 JRE。所以安装了 JDK，就不用在单独安装 JRE 了。其中的开发工具包括编译工具(javac.exe) 打包工具(jar.exe)等。
- JRE(Java Runtime Environment Java 运行环境) 是 JDK 的子集，也就是包括 JRE 所有内容，以及开发应用程序所需的编译器和调试器等工具。JRE 提供了库、Java 虚拟机(JVM)和其他组件，用于运行 Java 编程语言、小程序、应用程序。
- JVM(Java Virtual Machine Java 虚拟机)，JVM 可以理解为是一个虚拟出来的计算 机，具备着计算机的基本运算方式，它主要负责把 Java 程序生成的字节码文件， 解释成具体系统平台上的机器指令，让其在各个平台运行。

### JDK 是什么?

JDK 是 JRE 的超集，JDK 包含了 JRE 所有的开发、调试以及监视应用程序的工具。以及如下重要的组件:

- java – 运行工具，运行 .class 的字节码
- javac– 编译器，将后缀名为.java 的源代码编译成后缀名为.class 的字节码
- javap – 反编译程序
- javadoc – 文档生成器，从源码注释中提取文档，注释需符合规范
- jar – 打包工具，将相关的类文件打包成一个文件
- jdb – debugger，调试工具
- jps – 显示当前 java 程序运行的进程状态
- appletviewer – 运行和调试 applet 程序的工具，不需要使用浏览器
- javah – 从 Java 类生成 C 头文件和 C 源文件。这些文件提供了连接胶合，使 Java
  和 C 代码可进行交互。
- javaws – 运行 JNLP 程序
- extcheck – 一个检测 jar 包冲突的工具
- apt – 注释处理工具
- jhat – java 堆分析工具
- jstack – 栈跟踪程序
- jstat – JVM 检测统计工具
- jstatd – jstat 守护进程
- jinfo – 获取正在运行或崩溃的 java 程序配置信息
- jmap – 获取 java 进程内存映射信息
- idlj – IDL-to-Java 编译器. 将 IDL 语言转化为 java 文件
- policytool – 一个 GUI 的策略文件创建和管理工具
- jrunscript – 命令行脚本运行
- appletviewer:小程序浏览器，一种执行 HTML 文件上的 Java 小程序的 Java 浏览器

### JRE 是什么?

JRE 本身也是一个运行在 CPU 上的程序，用于解释执行 Java 代码。一般像是实施的工作，会在客户现场安装 JRE，因为这是运行 Java 程序的最低 要求。

- bin:有 java.exe 但没有 javac.exe。也就是无法编译 Java 程序，但可以运行 Java 程序，可以把这个 bin 目录理解成 JVM。
- lib:Java 基础&核心类库，包含 JVM 运行时需要的类库和 rt.jar。也包含用于安 全管理的文件，这些文件包括安全策略(security policy)和安全属性(security properties)等。

## JVM 是什么?

其实简单说 JVM 就是运行 Java 字节码的虚拟机，JVM 是一种规范，各个供应 商都可以实现自己 JVM 虚拟机。JVM 之所以称为虚拟机，主要就是因为它为了实现 “write-once-run- anywhere”。提供了一个不依赖于底层操作系统和机器硬件结构的运行环境。

### Client 模式、Server 模式

在 JVM 中有两种不同风格的启动模式， Client 模式、Server 模式。

- Client 模式:加载速度较快。可以用于运行 GUI 交互程序。
- Server 模式:加载速度较慢但运行起来较快。可以用于运行服务器后台程序。

修改配置模式文件:C:\Program Files\Java\jre1.8.0_45\lib\amd64\jvm.cfg

```bash
# List of JVMs that can be used as an option to java, javac, etc.
# Order is important -- first in this list is the default JVM.
# NOTE that this both this file and its format are UNSUPPORTED and # WILL GO AWAY in a future release.
#
# You may also select a JVM in an arbitrary location with the
# "-XXaltjvm=<jvm_dir>" option, but that too is unsupported
# and may not be available in a future release.
#
-server KNOWN
-client IGNORE
```

如果需要调整，可以把 client 设置为 KNOWN，并调整到 server 前面。

- JVM 默认在 Server 模式下，-Xms128M、-Xmx1024M
- JVM 默认在 Client 模式下，-Xms1M、-Xmx64M

### JVM 结构和执行器

- Class Loader:类装载器是用于加载类文件的一个子系统，其主要功能有三个: loading(加载)，linking(链接),initialization(初始化)。
- JVM Memory Areas:方法区、堆区、栈区、程序计数器。
- Interpreter(解释器):通过查找预定义的 JVM 指令到机器指令映射，JVM 解释器
  可以将每个字节码指令转换为相应的本地指令。它直接执行字节码，不执行任何优
  化。
- JIT Compiler(即时编译器):为了提高效率，JIT Compiler 在运行时与 JVM 交互，
  并适当将字节码序列编译为本地机器代码。典型地，JIT Compiler 执行一段代码， 不是每次一条语句。优化这块代码，并将其翻译为优化的机器代码。JIT Compiler 是默认开启
