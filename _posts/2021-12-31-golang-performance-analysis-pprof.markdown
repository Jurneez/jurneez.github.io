---
layout: post
title: golang学习笔记之pprof性能分析.
tags: [golang, proformance-analysis, pprof]
image: '/images/posts/2.jpg'
---

pprof 是Golang自身提供的性能分析工具，有两个标准工具包：

`net/http/pprof`:对运行时的http服务进行性能分析
`runtime/pprof`:采集程序的运行数据进行分析

## net/http/pprof

### 如何使用`net/http/pprof`的性能分析呢

如何对http服务进行pprof性能分析呢，在代码中导入`_ net/http/pprof`即可。

我们用一个简单的例子学习 `net/http/pprof` 的使用

``` golang
package main

import (
	"fmt"
	"net/http"
	_ "net/http/pprof" 
)

func main() {
	go func() {
		for {
			fmt.Println("pprof 性能")
		}
	}()

	http.ListenAndServe("0.0.0.0:6060", nil)
}
```
开启服务，在浏览器的`0.0.0.0:6060`网址下，会自动有一个`debug/pprof`的路径。

![about](/images/posts/golang-performance-analysis-pprof-1.png)

从上面可以看出，`debug/pprof`中都描述了哪些方面的性能：

> `allocs`

allocs是allocations的缩写，表示分配额的意思。

英文描述为：`A sampling of all past memory allocations`.

指显示 当前时间之前，内存（主要是栈内存）的分配情况。

> `block`

查看导致阻塞同步的堆栈追踪
> `cmdline`

里面是一个文件路径，是当前程序的执行路径

> `goroutine`
所以当前所有运行的goroutine的堆栈追踪

> `threadreate`

新操作系统线程创建的堆栈追踪

通过点击各个按钮，可以查看不同的子页面进行性能分析。

### 如何在交互式终端进行性能分析

我们可以通过上面的操作对页面进行简单的性能分析，但是在终端进行呢？

我们可以用`go tool pprof xxx` 命令。

#### `go tool pprof http://localhost:6060/debug/pprof/profile\?seconds\=60`

执行这个命令后，等待60秒，pprof会进行cpu性能分析。

之后会将分析结果保存到 一个`xx/pprof.samples.cpu.001.pb.gz`文件中，并默认进入终端交互模式。

`ps:可以通过命令  go tool pprof profile /Users/user/pprof/pprof.samples.cpu.001.pb.gz 进入交互式模式，查看内容`。

![about](/images/posts/golang-performance-analysis-pprof-2.png)

执行top命令，下看cpu的运行情况。
![about](/images/posts/golang-performance-analysis-pprof-3.png)

1. flat：给定函数上运行耗时
2. flat%：同上的 CPU 运行耗时总比例
3. sum%：给定函数累积使用 CPU 总比例
4. cum：当前函数加上它之上的调用运行总耗时
5. cum%：同上的 CPU 运行耗时总比例
6. 函数名称

#### `go tool pprof http://localhost:6060/debug/pprof/heap`

这里，`type=inuse_space`

表示此处分析应用程序的常驻内存占用情况


当 `type=-alloc_objects`的时候，表示分析应用程序的内存临时分配情况
## runtime/pprof


## 参考

1. https://segmentfault.com/a/1190000016412013

2. https://www.cnblogs.com/Dr-wei/p/11742414.html