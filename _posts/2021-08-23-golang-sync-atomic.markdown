---
layout: post
title: Golang学习笔记之Golang中的原子操作sync/atomic
tags: [golang, sync.atomic, 原子性]
image: '/images/posts/6.jpg'
---
在看golang的源代码的时候，经常会遇到用atomic.Load**()方法进行赋值的操作。所以，本章我想好好了解一些golang中的sync.Atomic包。

---

在Golang中，sync.Atomic包提供几种基本数据类型的原子操作。

---
> 关于原子性

一个或者多个操作在 CPU 执行的过程中不被中断的特性，称为原子性（atomicity）.这些操作对外表现成一个不可分割的整体，他们要么都执行，要么都不执行，外界不会看到他们只执行到一半的状态。有些朋友可能不知道，在 Go（甚至是大部分语言）中，一条普通的赋值语句其实不是一个原子操作。例如，在32位机器上写int64类型的变量就会有中间状态，因为它会被拆成两次写操作（MOV）——写低 32 位和写高 32 位，如下图所示：

![about](/images/posts/golang-sync-atomic-1.png)

如果一个线程刚写完低32位，还没来得及写高32位时，另一个线程读取了这个变量，那它得到的就是一个毫无逻辑的中间变量，这很有可能使我们的程序出现诡异的 Bug。

---
#### Golang中的sync/atomic支持的几种基本数据类型
1. int32
2. int64
3. uint32
4. uint64
5. uintptr
6. unsafe.Pointer

---

#### 支持的操作
> 交换数据:SwapT(addr *T, new T) (old T)

ps:SwapT 中的T代表“Int32”、“Int64”、“Uint32”,"Uint64","Uintptr","Pointer"(后面的几个操作中的T同理)

参数中的T代表数据类型“int32”、“int64”，“uint32”，“uint64”，“uintptr”，“unsafe.pointer”(后面的几个操作中的T同理)

> 比较并交换数据：CompareAndSwapT(addr *T, old, new T) (swapped bool)

> 增减数据：AddT(addr *T, delta T) (new T)

delta代表增加/减少的数值，如果要做减法操作，delta为负数

> 加载数据：LoadT(addr *T) (val T)
> 存储数据：StoreT(addr *T, val T)

---
> 参考文章：

1. Go 语言标准库中 atomic.Value 的前世今生:https://blog.betacat.io/post/golang-atomic-value-exploration/
