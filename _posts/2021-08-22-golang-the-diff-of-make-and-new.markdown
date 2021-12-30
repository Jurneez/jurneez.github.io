---
layout: post
title: Golang学习笔记之new和make的区别
tags: [golang, make, new]
image: '/images/posts/7.jpg'
---
要学习make和new的区别，我们首先去源码中看源码对它们的定义：
{% highlight golang %}
/*
make内建函数分配并初始化slice、map、chan这几个type的对象。

*/
func make(t Type, size ...IntegerType) Type

/*
new内置函数分配内存。
第一个参数是type，而不是value，并且这个valus返回的是指向该类型分配空间且值为0的指针
*/
func new(Type) *Type
{% endhighlight %}
<div class="col-xs-12 col-sm-12">
    <pre class="soloCode">
        // The make built-in function allocates and initializes an object of type
        // slice, map, or chan (only). Like new, the first argument is a type, not a
        // value. Unlike new, make's return type is the same as the type of its
        // argument, not a pointer to it. The specification of the result depends on
        // the type:
        //	Slice: The size specifies the length. The capacity of the slice is
        //	equal to its length. A second integer argument may be provided to
        //	specify a different capacity; it must be no smaller than the
        //	length. For example, make([]int, 0, 10) allocates an underlying array
        //	of size 10 and returns a slice of length 0 and capacity 10 that is
        //	backed by this underlying array.
        //	Map: An empty map is allocated with enough space to hold the
        //	specified number of elements. The size may be omitted, in which case
        //	a small starting size is allocated.
        //	Channel: The channel's buffer is initialized with the specified
        //	buffer capacity. If zero, or the size is omitted, the channel is
        //	unbuffered.
        func make(t Type, size ...IntegerType) Type

        // The new built-in function allocates memory. The first argument is a type,
        // not a value, and the value returned is a pointer to a newly
        // allocated zero value of that type.
        func new(Type) *Type
    </pre>
    从源码中我们可以知道：
    <dl>
        <dt>new和make之间的相同点：</dt>
        <dd>1. make和new都是golang用来分配内存的内置函数</dd>
        <dt>new和make之间的不同点：</dt>
        <dd>1. 从传参数看：new只传入一个参数，参数必须为数据类型，make可传入多个参数，第一个参数必须为数据类型，其他的参数是整数。</dd>
        <dd>2. 从返回的参数看：new返回一个指向分配内存的指针，make返回一个传入的数据类型</dd>
        <dd>3. 从type看，new的type可以是任意一个数据类型，make的type只能是slice、map、chan</dd>
    </dl>

    <hr>

    <h5>
        下面通过代码进行实践
    </h5>
    用new声明变量如下：
    <pre class="soloCode">
        package main

        import (
            "fmt"
        )
        
        type People struct {
            name string
            age int64
        }
        func main() {
            a := new(int)
            fmt.Printf("用new声明int变量，a = %#v \n",a)
            fmt.Printf("用new声明int变量，a = %#v \n",*a)
            
            s := new(string)
            fmt.Printf("用new声明string变量，s = %#v \n",s)
            fmt.Printf("用new声明string变量，*s = %#v \n",*s)
            
            p := new(People)
            fmt.Printf("用new声明People struct变量，p = %#v \n",p)
            fmt.Printf("用new声明People People变量，*p = %#v \n",*p)
            
            
            m := new(map[int]People)
            fmt.Printf("用new声明map[int]People变量，m = %#v \n",m)
            fmt.Printf("用new声明map[int]People变量，*m = %#v \n",*m)
         
            
            sl := new([]int)
            fmt.Printf("用new声明[]int数组变量，sl = %#v \n",sl)
            fmt.Printf("用new声明[]int数组变量，*sl = %#v \n",*sl)
        
            ch := new(chan int)
            fmt.Printf("用new声明chan变量，ch = %#v \n",ch)
            fmt.Printf("用new声明chan变量，*ch = %#v \n",*ch)
        }
    </pre>
    执行代码，结果如下：
    <pre class="soloCode">
    用new声明int变量，a = (*int)(0xc00001a040)
    用new声明int变量，a = 0
    用new声明string变量，s = (*string)(0xc0000101e0)
    用new声明string变量，*s = ""
    用new声明People struct变量，p = &main.People{name:"", age:0}
    用new声明People People变量，*p = main.People{name:"", age:0}
    用new声明map[int]People变量，m = &map[int]main.People(nil)
    用new声明map[int]People变量，*m = map[int]main.People(nil)
    用new声明[]int数组变量，sl = &[]int(nil)
    用new声明[]int数组变量，*sl = []int(nil)
    用new声明chan变量，ch = (*chan int)(0xc00000e040)
    用new声明chan变量，*ch = (chan int)(nil)
    </pre>
    <dl>
        <dt>根据以上代码实践我们可以知道以下几点：</dt>
        <dd>1. new 传的参数是一个type</dd>
        <dd>2. new 返回的是一个指针</dd>
        <dd>3. new 会对传入的类型进行初始化，值为传入类型的默认值，并分配内存</dd>
    </dl>

    <hr>

    用make声明变量：
    <pre class="soloCode">
        package main

        import (
            "fmt"
        )

        type People struct {
            name string
            age int64
        }
        func main() {
            m := make(map[int]People,3)
            fmt.Printf("%#v \n",m)
            
            ch := make(chan int,3)
            fmt.Printf("%#v \n",ch)
            
            s := make([]int,2,5)
            fmt.Printf("%#v \n",s)
            fmt.Printf("make 声明的slice的长度 len(s) = %#v \n", len(s))
            fmt.Printf("make 声明的slice的cap cap(s) = %#v \n", cap(s))
            
            s2 := make([]int,3)
            fmt.Printf("%#v \n",s2)
            fmt.Printf("make 声明的slice的长度 len(s2) = %#v \n", len(s2))
            fmt.Printf("make 声明的slice的cap cap(s2) = %#v \n", cap(s2))
        }

    </pre>
    执行代码，结果如下：
    <pre class="soloCode">
        map[int]main.People{}
        (chan int)(0xc00005c080)
        []int{0, 0}
        make 声明的slice的长度 len(s) = 2
        make 声明的slice的cap cap(s) = 5
        []int{0, 0, 0}
        make 声明的slice的长度 len(s2) = 3
        make 声明的slice的cap cap(s2) = 3     
    </pre>

    <dl>
        <dt>
            通过以上代码，可以了解到make的以下信息：
        </dt>
        <dl>
            1. make返回的是传入的类型的实体
        </dl>
        <dl>
            2. make在声明slice的时候，第一个int是len，第二个int是cap，如果没有传第二个int参数，那么len和cap的值是一样的。
        </dl>
    </dl>

    <hr>
</div>