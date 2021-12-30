---
layout: post
title: Golang踩坑之map并发
tags: [golang, map, trap]
image: '/images/posts/5.jpg'
---
> 场景
全局定义map变量，导并发写map，然后panic
> 报错
fatal error: concurrent map writes
> 原因
map并非并发安全，多个grountine对map进行写操作时，就会panic，报上面的错误。
1. 少定义全局map变量。
2. 必须要定义map全局变量的时候，加锁。（采用sync.Map或者copy on write-read 的时候不加锁，write的时候加锁）
</div>