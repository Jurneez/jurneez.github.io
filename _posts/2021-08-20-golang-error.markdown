---
layout: post
title: Golang学习笔记之error源码解析
tags: [golang,error,源码]
image: '/images/posts/1.jpg'
---
golang语言中的errors包是我们在编写代码的时候经常遇到的，本章解析errors的源代码。

ps：代码基于golang版本：1.14.3进行分析

---

>  golang关于error的定义是在builtin/builtin.go文件中实现的，具体如下：
{% highlight golang %}
type error interface {
    Error() string
} 
{% endhighlight %}


> golang关于error实现的代码是非常简单易懂的，去掉各种注释后的代码如下：
{% highlight golang %}
func New(text string) error {
    return &errorString{text}
}
type errorString struct {
    s string
}

func (e *errorString) Error() string {
    return e.s
}
{% endhighlight %}

通过实现error声明的Error()方法来实现

---

通过go源码的低层实现，我们也可以发现，通过实现Error方法，我们也可以定义自己所需要的error。

比如:(例子来自源码)
{% highlight golang %}           
type MyError struct {
    When time.Time
    What string
}

func (e MyError) Error() string {
    return fmt.Sprintf("%v: %v", e.When, e.What)
}

func oops() error {
    return MyError{
        time.Date(1989, 3, 15, 22, 30, 0, 0, time.UTC),
        "the file system has gone away",
    }
}

func Example() {
    if err := oops(); err != nil {
        fmt.Println(err)
    }
    // Output: 1989-03-15 22:30:00 +0000 UTC: the file system has gone away
}
{% endhighlight %}