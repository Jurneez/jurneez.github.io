---
layout: post
title: 数据库之SQL和NOSQL和NewSQL
tags: [db]
image: '/images/posts/1.jpg'
---

> 非关系型数据库的优势：

1. 性能NOSQL是基于键值对的，可以想象成表中的主键和值的对应关系，而且不需要经过SQL层的解析，所以性能非常高。

2. 可扩展性同样也是因为基于键值对，数据之间没有耦合性，所以非常容易水平扩展。

> 关系型数据库的优势：

1. 复杂查询可以用SQL语句方便的在一个表以及多个表之间做非常复杂的数据查询。

2. 事务支持使得对于安全性能很高的数据访问要求得以实现。

关系型数据库：Oracle、DB2、Microsoft SQL Server、Microsoft Access、MySQL等。