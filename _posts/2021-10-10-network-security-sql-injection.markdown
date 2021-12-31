---
layout: post
title: 网络安全之SQL注入
tags: [网络安全, sql注入]
image: '/images/posts/5.jpg'
---

最近在看网络安全的东西，了解到sql注入，是平时很常见，写代码的时候很需要留意的东西。所以就将在网上看到的内容总结到这里，后面可以经常回顾。

---

首先，什么是sql注入呢？

SQL注入是一种将SQL代码添加到输入参数中，传递到服务器解析并执行的一种攻击手法。

SQL注入攻击是输入参数未经过滤，然后直接拼接到SQL语句当中解析，执行达到预想之外的一种行为，称之为SQL注入攻击。

---

其次，SQL注入是怎么产生的呢？

1. WEB开发人员无法保证所有的输入都已经过滤
2. 攻击者利用发送给SQL服务器的输入参数构造可执行的SQL代码（可加入到get请求、post请求、http头信息、cookie中）
3. 数据库未做相应的安全配置

--- 

sql注入攻击的方法有哪些

>  一、数字注入

在浏览器地址栏输入：`learn.me/sql/article.php?id=1`，这是一个get型接口，发送这个请求相当于调用一个查询语句:

``` golang
sql = "SELECT * FROM article WHERE id =",$id
```

正常情况下，应该返回一个id=1的文章信息。那么，如果在浏览器地址栏输入：

``` golang
learn.me/sql/article.php?id=-1 OR 1 =1
```

这就是一个SQL注入攻击了，可能会返回所有文章的相关信息。为什么会这样呢？

这是因为，id = -1永远是false，1=1永远是true，所有整个where语句永远是ture，所以where条件相当于没有加where条件，那么查询的结果相当于整张表的内容。

> 二、字符串注入

有这样一个用户登录场景：登录界面包括用户名和密码输入框，以及提交按钮。输入用户名和密码，提交。

这是一个post请求，登录时调用接口learn.me/sql/login.html，首先连接数据库，然后后台对post请求参数中携带的用户名、密码进行参数校验，即sql的查询过程。假设正确的用户名和密码为user和pwd123，输入正确的用户名和密码、提交，相当于调用了以下的SQL语句：

``` mysql
SELECT * FROM user WHERE username = 'user' ADN password = 'pwd123'
```

由于用户名和密码都是字符串，SQL注入方法即把参数携带的数据变成mysql中注释的字符串。mysql中有2种注释的方法：

1. '#'：'#'后所有的字符串都会被当成注释来处理

```
SELECT * FROM user WHERE username = 'user'#'ADN password = '111'
等同于：
SELECT * FROM user WHERE username = 'user'
```

2. '-- ' （--后面有个空格）：'-- '后面的字符串都会被当成注释来处理

```
SELECT * FROM user WHERE username = 'user'-- 'AND password = '111'
等同于：
SELECT * FROM user WHERE username = 'user'
```

---

最后，怎么预防SQL注入呢？

1. 严格检查输入变量的类型和格式
2. 过滤和转义特殊字符
3. 利用mysql的预编译机制

> 参考

1. https://blog.csdn.net/github_36032947/article/details/78442189