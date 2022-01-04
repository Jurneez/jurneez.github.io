---
layout: post
title: Redis之常见错误及解决办法
tags: [redis, error]
image: '/images/posts/1.jpg'
---

### 问题一

---

> 报错：

MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk. Commands that may modify the data set are disabled. Please check Redis logs for details about the error.

> 原因：

强制停止redis快照导致，redis运行用户没有权限写rdb文件或者磁盘空间满了.

> 解决办法

config set stop-writes-on-bgsave-error no

### 问题二

---

> 报错：

(error) OOM command not allowed when used memory

> 原因：

设置了maxmemory的选项,redis内存使用达到上限。

> 解决办法：

可以通过设置LRU算法来删除部分key,释放空间。