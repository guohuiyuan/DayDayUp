# docker学习和应用

<!-- GFM-TOC -->
- [docker学习和应用](#docker学习和应用)
	- [引言](#引言)
	- [定义](#定义)
	- [特点](#特点)
	- [image](#image)
	- [container](#container)
	- [常见问题](#常见问题)
	- [常用命令](#常用命令)
		- [在容器内运行一个应用程序](#在容器内运行一个应用程序)
		- [进入后台状态的容器](#进入后台状态的容器)
		- [查看容器状态](#查看容器状态)
		- [改变容器状态](#改变容器状态)
		- [查看容器内的标准输出](#查看容器内的标准输出)
		- [导出和导入容器](#导出和导入容器)
		- [删除容器](#删除容器)
		- [运行一个web服务器](#运行一个web服务器)
		- [Dockerfile](#dockerfile)
		- [容器互联](#容器互联)
<!-- GFM-TOC -->

## 引言
    
	稍微使用了一下docker，有点感慨，为什么我大学的时候没有去了解使用它。
    
	感觉使用docker，就可以瞬间在Linux下写脚本，试命令，比在本机安装一个虚拟机跑linux方便的多。
    
	一年前下载了docker desktop，图形化界面用不好，命令行才是真的好用。
    
## 定义

	Docker 是一个容器化平台，它包装你所有开发环境依赖成一个整体，像一个容
	器。保证项目开发，如开发、测试、发布等各生产环节都可以无缝工作在不同
	的平台

	Docker 容器：将一个软件包装在一个完整的文件系统中，该文件系统包含运行
	所需的一切：代码，运行时，系统工具，系统库等。可以安装在服务器上的任
	何东西。

	这保证软件总是运行在相同的运行环境，无需考虑基础环境配置的改变。

## 特点

	轻量 ：在一台机器上运行的多个 Docker 容器可以共享这台机器的操作系统内核；它们能够迅速启动，只需占用很少的计算和内存资源。镜像是通过文件系统层进行构造的，并共享一些公共文件。这样就能尽量降低磁盘用量，并能更快地下载镜像。

	标准 ：Docker 容器基于开放式标准，能够在所有主流 Linux 版本、Microsoft Windows 以及包括 VM、裸机服务器和云在内的任何基础设施上运行。

	安全 ：Docker 赋予应用的隔离性不仅限于彼此隔离，还独立于底层的基础设施。Docker 默认提供最强的隔离，因此应用出现问题，也只是单个容器的问题，而不会波及到整台机器。

## image

	Docker image 是 Docker 容器的源。换言之，Docker images 用于创建 Docker 容器（containers）。
	
	映像（Images）通过 Docker build 命令创建，当 run 映像时，
	
	它启动成一个 容器（container）进程。 做好的映像由于可能非常庞大，常注册存储在诸如 registry.hub.docker.com 这样的公共平台上。
	
	映像常被分层设计，每层可单独成为一个小映像，由多层小映像再构成大映像，这样碎片化的设计为了使映像在互联网上共享时，最小化传输数据需求。

## container

	Docker containers -- Docker 容器 -- 是包含其所有运行依赖环境，但与其它容器共享操作系统内核的应用，它运行在独立的主机操作系统用户空间进中。
	
	Docker 容器并不紧密依赖特定的基础平台：可运行在任何配置的计算机，任何平台以及任何云平台上

## 常见问题

## 常用命令

---

### 在容器内运行一个应用程序

docker pull [镜像]：获得镜像，一般不需要这一步，因为若本地无镜像，docker run会自动拉取

docker run [镜像] [执行的命令]：在容器内运行一个应用程序

docker run -i -t [镜像] [执行的命令]：运行交互式的容器，-i 交互式操作，-t 终端

docker run -d [镜像] [执行的命令]：后台运行，-d 后台运行

```
docker pull ubuntu:15.10
docker run ubuntu:15.10 /bin/echo "Hello world"
docker run -i -t ubuntu:15.10 /bin/bash
docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

---

### 进入后台状态的容器

docker attach [CONTAINER ID] [执行的命令]：退出会停止容器

docker exec [CONTAINER ID] [执行的命令]：退出不会停止容器，所以一般使用这一种

```
docker run -itd ubuntu:15.10 /bin/bash
docker exec -it c1264844c099 /bin/bash
```

---

### 查看容器状态

docker ps：查看正在运行的容器

docker ps -a：查看所有的容器，包括停止运行的

docker ps -l：显示最近创建的容器，只有一个容器

docker port [CONTAINER ID]：查看容器的端口

docker top [CONTAINER ID]：查看容器运行的进程

docker inspect [CONTAINER ID]：查看 Docker 的底层信息

docker port [CONTAINER ID] 5000：查看端口绑定的网址

```
docker ps 
docker ps -a
docker port 3d454c23db0c
docker top 6d809d5785d7
docker inspect 6d809d5785d7
docker port 536406b5056f 5000
```

---

### 改变容器状态

docker stop [CONTAINER ID] | docker stop [NAMES]：停止容器

docker start [CONTAINER ID] | docker start [NAMES]：启动容器

docker restart [CONTAINER ID] | docker restart [NAMES]：重启容器

有点类似supervisor命令

```
docker stop 98f5aa2579f3
docker start 98f5aa2579f3
docker restart 98f5aa2579f3
```

---

### 查看容器内的标准输出

docker logs [CONTAINER ID] | docker logs [NAMES]：查看日志

docker logs -f [CONTAINER ID]：类似tail -f，动态查看日志
xxx logs id 这种查日志的方式，好像很多命令行查日志都是这样。

```
docker logs 98f5aa2579f3
docker logs clever_almeida
```

---

### 导出和导入容器

docker export [CONTAINER ID] > [本地路径]

cat [本地路径] | docker import - [镜像]

```
docker export 15c4c8e80a5a > hello.tar
cat hello.tar | docker import - hello:test1
```

---

### 删除容器

docker rm -f [CONTAINER ID]：删除容器，可以删除运行的

docker rm：删除容器，必须是停止状态的容器

docker container prune：处理所有处于终止状态的容器

docker rmi -f [IMAGE ID]：删除镜像

```
docker rm -f 15c4c8e80a5a
docker container prune
docker rmi -f 6fae60ef3446
```

---

### 运行一个web服务器

docker run -d -P training/webapp python app.py：这个是不行的，因为会默认用其他端口

docker run -d -p 5000:5000 training/webapp python app.py： 前面是访问的端口，后面的是docker内部的端口

docker run -d -p 127.0.0.1:5001:5000 training/webapp python app.py：可指定绑定的网址

docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py：可选择连接的方式为udp

```
docker run -d -p 5000:5000 training/webapp python app.py
docker run -d -p 127.0.0.1:5001:5000 training/webapp python app.py
docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py
```

---

### Dockerfile

最近需要构建一个编译springboot项目的镜像，所以先编写这一章

[docker使用maven时修改镜像](https://www.jianshu.com/p/4d61dc51c98b)

首先把java和maven搞清楚

maven安装在 /usr/share/maven 目录下，要替换maven的settings文件

```
docker run -it maven:3.6-jdk-8 /bin/bash
```

java项目的dockerfile

```
FROM maven:3.6-jdk-8 AS builder

VOLUME /root/.m2

# Get data producer code and compile it
COPY ./src /opt/data-producer/src
COPY ./pom.xml /opt/data-producer/pom.xml
COPY ./settings.xml /usr/share/maven/conf/settings.xml

WORKDIR /opt/data-producer
RUN mvn clean package -Dmaven.test.skip=true

FROM java:8
EXPOSE 8080
COPY --from=builder /opt/data-producer/target/renren-fast.jar /app.jar
RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java","-jar","/app.jar"]
```

go项目的dockerfile

```
FROM golang:1.18 AS builder

ENV GO111MODULE=on \
    CGO_ENABLED=0 \
    GOOS=linux \
    GOARCH=amd64 \
	GOPROXY="https://goproxy.cn,direct"

WORKDIR /workdir
COPY . .
RUN go build -o clock 

FROM alpine:3.15
COPY --from=builder /workdir/clock clock
CMD ["./clock"]

```

---

### 容器互联

docker run -d -P --name runoob training/webapp python app.py：使用--name给容器命名

docker network create -d bridge test-net：新建网络，-d 参数指定 Docker 网络类型，有 bridge、overlay

docker network -ls：查看网络状态

```
docker run -d -P --name runoob training/webapp python app.py
docker network create -d bridge test-net

# 运行两个相同网络的容器
docker run -itd --name test1 --network test-net ubuntu /bin/bash
docker run -itd --name test2 --network test-net ubuntu /bin/bash

# 连接两个容器
docker exec -it test1 /bin/bash
docker exec -it test2 /bin/bash

# 安装ping命令
apt-get update
apt install iputils-ping

# 测试网络是否互通
ping test2
ping test1
```