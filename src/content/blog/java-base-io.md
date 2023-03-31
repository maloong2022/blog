---
author: Maloong🐎🐲
pubDatetime: 2023-03-24T17:34:00Z
title: 卷起来🐎🐲💪 -- JavaIO流
postSlug: java-base-io
featured: true
draft: false
tags:
  - java
ogImage: ""
description: 按照流的流向分，可以分为输入流和输出流； 按照操作单元划分，可以划分为字节流和字符流； 按照流的角色划分为节点流和处理流。 Java Io流共涉及40多个类，这些类看上去很杂乱，但实际上很有规则，而且彼 此之间存在非常紧密的联系， Java I0流的40多个类都是从如下4个抽象类基类 中派生出来的。
---

按照流的流向分，可以分为输入流和输出流； 按照操作单元划分，可以划分为字节流和字符流； 按照流
的角色划分为节点流和处理流。 Java Io 流共涉及 40 多个类，这些类看上去很杂乱，但实际上很有规则，
而且彼 此之间存在非常紧密的联系， Java I0 流的 40 多个类都是从如下 4 个抽象类基类 中派生出来的。

- InputStream/Reader: 所有的输入流的基类，前者是字节输入流，后者是字符 输入流。
- OutputStream/Writer: 所有输出流的基类，前者是字节输出流，后者是字符输 出流。

## Table of contents

## 分类结构图

![按操作方式分类结构图](https://s2.loli.net/2023/03/24/hi1EVDnXTSesa2c.png)

![IO 操作方式分类按操作对象分类结构图](https://s2.loli.net/2023/03/24/9fODtZKe65L31wB.png)
