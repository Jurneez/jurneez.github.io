---
layout: page
title: Docker命令
image: '/images/pages/about.jpeg'
---

## docker命令
### 一、容器生命周期管理
#### 1. run
- `docker run`：创建一个新的容器，并运行一个命令
- 语法：docker run 
#### 2. start/stop/restart
- `docker start container_name[s]`: 启动一个或多个被停止的容器
- `docker stop container_name`:停止一个运行中的容器
- `docker restart container_name`:重启容器
#### 3. kill
- `docker kill`:杀掉运行中的容器
- 语法：`docker kill [options] container_name`
    - options
        -   `-s` 向容器发送一个信号
#### 4. rm
- `docker rm`：删除一个或多个容器
- 语法：`docker rm [options] container_name[s]`
    - options
        - `-f` 强制删除
        - `-l` 删除容器间的网络链接，而非容器本身
        - `-v` 删除与容器相关的卷
#### 5. pause｜unpause
- `docker pause container_name`:暂停容器运行
- `docker unpause container_name`:恢复容器运行
#### 6. create
- `docker create container_name`:创建一个容器，但是不启动它
#### 7. exec命令
- `docker exec [options] container_name command [args]`: 在运行的容器中执行命令
    - options
        - `-d`  后台运行
        - `-t` 分配一个伪终端
        - `-i` 没有附加也保持STDIN 打开
    - example
        - `docker ps -a `: 查看已经在运行的容器，然后使用容器 ID 进入容器。 
        - `docker exec -it 9df70f9a0714 /bin/bash`: 第一列的 9df70f9a0714 就是容器 ID。通过 exec 命令对指定的容器执行 bash:
### 二、容器操作
#### 1. ps
- `docker ps [options]`:列出容器
    - options  
        - `-a` ： 列出所有的容器，包括未运行的 all
#### 2. inspect
- `docker inspect [options] container_name|container_ip[s]`: 获取容器｜镜像的元数据
    - options
        - `-f`:指定返回值的模版文件
        - `-s`:显示总的文件大小
        - `--type`:为指定类型返回json
#### 3. top
- `docker top [options] container_name [ps options]`:查看容器中运行的进程信息，支持ps命令参数
#### 4. attach
- `docker attach [options] container_name`:连接到正在运行中的容器。
#### 5. events
- `docker events [options]` 从服务器获取实时事件
    - options
        - `-f` 根据条件过滤事件
        - `--since` 从指定的时间戳后显示所有事件
        - `--until` 流水时间显示到指定的时间为止
    - example
        - `docker events  --since="1467302400"`
        -  `docker events -f "image"="mysql:5.6" --since="1467302400" `

#### 6. logs
- `docker logs [options] container_name`:查看容器的日志
    - options
        - `-f`:跟踪日志输出
        - `-t`:显示时间戳
        - `--since`:显示某个时间开始的所有日志
        - `--tail`:仅列出最近n条日志
    - example
        - `docker logs --since="2016-07-01" --tail=10 mynginx`
        - `docker logs -f mynginx`
#### 7. wait
#### 8. export
#### 9. port

### 三、容器rootfs命令
#### 1. commit
#### 2. cp
#### 3. diff

### 四、镜像仓库
#### 1. login
- `docker login -u username -p password`: 登陆到一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub
- `docker logout -u username -p password`:登出一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub
#### 2. pull
 
### 五、本地镜像管理
### 六、info/version
#### 1. info
- `docker info`:显示docker信系统信息，包括镜像和容器数量
#### 2. version
- `docker version [options]`:查看docker版本信息
    - options
        -  `-f` :指定返回值的模板文件。