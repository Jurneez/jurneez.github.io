---
layout: post
title: Golang中输出带有特殊字符的字符串
tags: [frontpage, jekyll, blog]
image: '/images/posts/4.jpg'
---
> 场景

{% highlight golang%}
str := `abd\n123`
fmt.Print(str)
{% endhighlight %}

以上代码的执行结果如下：
```
abc
123
```

然而，我希望可以正常输出“abd\n123”，golang中提供了方法strconv.Quote()对字符串进行处理。
{% highlight golang%}
str := `abd\n123`
str=strconv.Quote(str)
fmt.Print(str) // 此时就可以正常输出想要的结果 “abd\n123”了。
{% endhighlight %}