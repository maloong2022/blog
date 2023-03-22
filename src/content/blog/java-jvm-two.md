---
author: Maloong
pubDatetime: 2023-03-22T14:44:00Z
title: The JVM(II) JVM 类加载实践
postSlug: java-jvm-classloader
featured: false
draft: false
tags:
  - java
  - jvm
ogImage: ""
description: Oracle has two products that implement Java Platform Standard Edition (Java SE) 8, Java SE Development Kit (JDK) 8 and Java SE Runtime Environment (JRE) 8.
---

![JVM 类加载](https://s2.loli.net/2023/03/22/ndNBfw7SE9VDvXH.png)

## Table of contents

## 类加载过程描述

JVM 类加载过程分为，加载、链接、初始化、使用和卸载这四个阶段，在链接中又 包括:验证、准备、解析。

- 加载:Java 虚拟机规范对 class 文件格式进行了严格的规则，但对于从哪里加载 class 文件，却非常自由。Java 虚拟机实现可以从文件系统读取、从 JAR(或 ZIP)压 缩包中提取 class 文件。除此之外也可以通过网络下载、数据库加载，甚至是运行 时直接生成的 class 文件。

- 链接:包括了三个阶段;

  - 验证，确保被加载类的正确性，验证字节流是否符合 class 文件规范，例如魔数 0xCAFEBABE，以及版本号等。
  - 准备，为类的静态变量分配内存并设置变量初始值等
  - 解析，解析包括解析出常量池数据和属性表信息，这里会包括 ConstantPool 结构体以及 AttributeInfo 接口等。

- 初始化:类加载完成的最后一步就是初始化，目的就是为标记常量值的字段赋值，以及执行 <clinit> 方法的过程。JVM 虚拟机通过锁的方式确保 clinit 仅被执行一次

- 使用:程序代码执行使用阶段。
- 卸载:程序代码退出、异常、结束等。
