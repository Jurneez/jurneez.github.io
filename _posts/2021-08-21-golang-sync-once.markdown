---
layout: post
title: Golang学习笔记之sync.Once源码解析
tags: [golang, sync.Once, 源码]
image: '/images/posts/8.jpg'
---

sync.Once在golang语言中保证对象只会被执行一次的。

---

> 纯净版源代码如下(golang版本：1.14.3)
{% highlight golang %}
type Once struct {
    done uint32
    m    sync.Mutex
}

func (o *Once) Do(f func()) {
    if atomic.LoadUint32(&o.done) == 0 {
        o.doSlow(f)
    }
}

func (o *Once) doSlow(f func()) {
    o.m.Lock()
    defer o.m.Unlock()
    if o.done == 0 {
        defer atomic.StoreUint32(&o.done, 1)
        f()
    }
}   
{% endhighlight %}
分析源码了解系统是如何实现f()函数只执行一次的：

1. 通过done记录f()是否执行过，如果done为0，表示未执行，如果done未1，表示已执行
2. 在doSlow(f)中，defer设置done=1，即如果f()执行完成，通过sync/atomic设置done为1，防止该函数再次被执行
3. 因为采用了sync.Mutex互斥锁，所以不会出现并发，同时访问done变量的情况

---

下面通过测试用例了解sync.Once的运行：测试用例来自官方1.14.3源代码

> case 1: 测试点：无论你调用多少次once.Do(f)函数，f()函数都会只执行一次
{% highlight golang %}
type one int

func (o *one) Increment() {
    *o++
}

func run(t *testing.T, once *Once, o *one, c chan bool) {
    once.Do(func() {
        o.Increment()
    })
    if v := *o; v != 1 {
        t.Errorf("once failed inside run: %d is not 1", v)
    }
    c <- true
}

func TestOnce(t *testing.T) {
    o := new(one)
    once := new(Once)
    c := make(chan bool)
    const N = 10
    for i := 0; i < N; i++ {
        go run(t, once, o, c)
    }
    for i := 0; i < N; i++ {
        <-c
    }

    if *o != 1 {
        t.Errorf("once failed outside run:%d is not 1", *o)
    }
}
{% endhighlight %}                        
执行结果：

*o=1

分析： 尽管本次用例中我们用for循环和grountine循环调用例run()函数N次，但是one的值还是1.

因为，对于一个Once的实例，只有当其结构体中的done值为0的时候，才会再次执行传入的函数，一旦执行完毕，设置once.done=1,就不会再次执行传入的函数了。

> case 2:测试点：即使once.Do(f)运行的f函数panic了，f()也只会执行一次
{% highlight golang %}
func TestOncePanic(t *testing.T) {
    var once Once
    func() {
        defer func() {
            if r := recover(); r == nil {
                t.Fatalf("Once.Do did not panic")
            }
        }()

        once.Do(func() {
            fmt.Println("panic")
            panic("failed")
        })
    }()

    once.Do(func() {
        fmt.Println("twice")
        t.Fatalf("Once.Do called twice")
    })
}
{% endhighlight %}
测试执行结果：只输出panic,不输出twice
{% highlight golang%}
=== RUN   TestOncePanic
panic
--- PASS: TestOncePanic (0.00s)
PASS
{% endhighlight %}
分析：即使f()函数进行了panic，但是Once中的doSlow(f)也会在return之前执行defer atomic.StoreUint32(&o.done, 1)语句，设置once.done=1
</pre>