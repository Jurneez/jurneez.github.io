---
layout: post
title: 工作笔记之记录一次docker容器异常退出问题排查
tags: [redis, error]
image: '/images/posts/1.jpg'
---

### 一、现象
服务部署在docker上，每次部署，编译一个容器。

出现了以下现象，启动的服务会5分钟异常退出并再次重新启动。

![现象](/images/posts/记录一次docker容器异常退出问题排查-现象.png)
<!-- 执行命令：docker ps -a -->
### 二、原因定位
#### step1: 
`docker logs container-name` ,查看容器log，没有发现异常

#### step2: 
`docker inspect container-name`,在`state`查看容器状态.里面的`ExitCode`可以分析容器退出原因。

其中“state”部分：
![图片](/images/posts/记录一次docker容器异常退出问题排查-state.png)

分析退出码`ExitCode`,
```
ExitCode=137表示：
- 表明容器收到了SIGKILL信号，进程被杀掉，对应kill -9
- 引发SIGKILL的是docker kill。这可以由用户或由docker守护程序来发起，手动执行：docker kill
- 137 比较常见，如果 pod 中的limit 资源设置较小，会运行内存不足导致OOMKilled，此时state 中的”OOMKilled”值为true，你可以在系统的 dmesg 中看到 oom 日志
```
通过以上，我发现， `State.OOMKilled=true`
表示由于内存不足引起的程序killed。

#### step3:
`docker inspect container-name`,在`Mounts`中可以查看程序信息。
![图片](/images/posts/记录一次docker容器异常退出问题排查-mounts.png)

#### step4:
去部署程序的地方，调整程序配置的Mem。

### 三、结果
第二天查看该程序，发现该程序发起的新的容器没有再次异常退出重启。
`成功`。

### 四、参考文章
1. 理解 docker 容器的退出码： http://www.xuyasong.com/?p=1802#Exit_Code_137
2. Docker常见故障排查指南 - 阿里云容器服务：https://developer.aliyun.com/article/59144
