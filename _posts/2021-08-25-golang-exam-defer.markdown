---
layout: post
title: Golang学习笔记之defer机制考点
tags: [golang, defer]
image: '/images/posts/3.jpg'
---

defer 机制一般有两个考点，第一个是多defer的执行顺序，第二个就是defer func(){...}() 和 defer func(a) {...}(a) 的区别。

---

> 例题一：执行顺序

``` golang
package main

import "fmt"

func out(str string) {
        fmt.Println(str)
}

func main() {
    str := "str_0"
    defer out(str)
    
    for i := 1;i < 11; i++ {
        str = fmt.Sprintf("str_%v",i)
        defer out(str)
    }
    
    defer out(str)
}
```
> 答题思路：

1. 首选我们知道了defer执行顺序遵循堆栈结构的先入后出，后入先出。
2. 分析上文代码，发现defer入栈顺序为: defer out("str_0")->defer out("str_1")->defer out("str_2")->...->defer out("str_10")->defer out("str_10")
3. 反向整理后得到正确的输出顺序。

> 正确答案：

```
str_10
str_10
str_9
str_8
str_7
str_6
str_5
str_4
str_3
str_2
str_1
str_0
```

---

> 例题二：defer func(){...}() 和 defer func(a) {...}(a) 的区别

``` golang
package main

import "fmt"

func main() {
    str := "str_0"
    defer func(str string){
        fmt.Println(str)
    }(str)
    
    for i := 1;i < 11;i++ {
        str = fmt.Sprintf("str_%v",i)
        defer func() {
            fmt.Println(str)
        }()
        defer func(str string) {
            fmt.Println(str)
        }(str)
    }
    
    defer func(){
        fmt.Println(str)
    }()
        }
```
> 解题思路：

1. 题中有两种 defer 调用方法， defer func(){...}() 和 defer func(str) {...}(str) 。
2. 通过闭包定义，我们知道defer func(){...}()中引用的 str 为外部空间的变量 str，使用 str 时等于直接使用外部变量 str 。
3. defer func(str) {...}(str)中引用的 str 由于是作为参数传进来的，等于内部对变量进行了重定义，所以使用的是传进来的参数。
4. 外部变量和传入参数的区别在于使用时外部变量的值为调用那一刻的值，而传入参数使用时值为传入那一刻的值。
5. 配合上一题，整理出输入顺序。

> 正确答案：
```
str_10
str_10
str_10
str_9
str_10
str_8
str_10
str_7
str_10
str_6
str_10
str_5
str_10
str_4
str_10
str_3
str_10
str_2
str_10
str_1
str_10
str_0
```

---
> 题目三：进阶题

``` golang
package main

import "fmt"

func main() {
    str := "str_0"
    defer func(){
        fmt.Println(str)
    }()
    
    for i := 1;i < 11;i++ {
        str = fmt.Sprintf("str_%v",i)
        defer func() {
            fmt.Println(str)
        }()
    }
    
    defer func() {
        fmt.Println(str)
    }()
    
    for i := 11;i < 21;i++ {
        str = fmt.Sprintf("str_%v",i)
        defer func(str string) {
            fmt.Println(str)
        }(str)
    }
}
```
> 答案：
```
str_20
str_19
str_18
str_17
str_16
str_15
str_14
str_13
str_12
str_11
str_20
str_20
str_20
str_20
str_20
str_20
str_20
str_20
str_20
str_20
str_20
str_20
```