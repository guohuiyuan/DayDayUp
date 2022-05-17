# docker学习和应用

- 引言
    
	稍微使用了一下docker，有点感慨，为什么我大学的时候没有去了解使用它。
    
	感觉使用docker，就可以瞬间在Linux下写脚本，试命令，比在本机安装一个虚拟机跑linux方便的多。
    
	一年前下载了docker desktop，用不会，发现命令行才是真的好用。
    
- docker定义

	一句话概括容器：容器就是将软件打包成标准化单元，以用于开发、交付和部署。

	容器镜像是轻量的、可执行的独立软件包 ，包含软件运行所需的所有内容：代码、运行时环境、系统工具、系统库和设置。

	容器化软件适用于基于 Linux 和 Windows 的应用，在任何环境中都能够始终如一地运行。

	容器赋予了软件独立性，使其免受外在环境差异（例如，开发和预演环境的差异）的影响，从而有助于减少团队间在相同基础设施上运行不同软件时的冲突。

	Docker 是世界领先的软件容器平台。

	Docker 使用 Google 公司推出的 Go 语言 进行开发实现，基于 Linux 内核 提供的 CGroup 功能和 namespace 来实现的，以及 AUFS 类的 
	UnionFS 等技术，对进程进行封装隔离，属于操作系统层面的虚拟化技术。 由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。

	Docker 能够自动执行重复性任务，例如搭建和配置开发环境，从而解放了开发人员以便他们专注在真正重要的事情上：构建杰出的软件。

	用户可以方便地创建和使用容器，把自己的应用放入容器。容器还可以进行版本管理、复制、分享、修改，就像管理普通的代码一样。

- docker 容器的特点

	轻量 ：在一台机器上运行的多个 Docker 容器可以共享这台机器的操作系统内核；它们能够迅速启动，只需占用很少的计算和内存资源。镜像是通过文件系统层进行构造的，并共享一些公共文件。这样就能尽量降低磁盘用量，并能更快地下载镜像。

	标准 ：Docker 容器基于开放式标准，能够在所有主流 Linux 版本、Microsoft Windows 以及包括 VM、裸机服务器和云在内的任何基础设施上运行。

	安全 ：Docker 赋予应用的隔离性不仅限于彼此隔离，还独立于底层的基础设施。Docker 默认提供最强的隔离，因此应用出现问题，也只是单个容器的问题，而不会波及到整台机器。

- docker命令

1. 使用 docker run 命令来在容器内运行一个应用程序

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

2. 进入后台状态的容器

docker attach [CONTAINER ID] [执行的命令]：退出会停止容器

docker exec [CONTAINER ID] [执行的命令]：退出不会停止容器，所以一般使用这一种

```
docker run -itd ubuntu:15.10 /bin/bash
docker exec -it c1264844c099 /bin/bash
```

3. 查看容器状态

docker ps：查看正在运行的容器

docker ps -a：查看停止运行的容器

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

4. 改变容器状态

docker stop [CONTAINER ID] | docker stop [NAMES]：停止容器

docker start [CONTAINER ID] | docker start [NAMES]：启动容器

docker restart [CONTAINER ID] | docker restart [NAMES]：重启容器

有点类似supervisor命令

```
docker stop 98f5aa2579f3
docker start 98f5aa2579f3
docker restart 98f5aa2579f3
```

5. 在宿主主机内使用 docker logs 命令，查看容器内的标准输出

docker logs [CONTAINER ID] | docker logs [NAMES]：查看日志

docker logs -f [CONTAINER ID]：类似tail -f，动态查看日志
xxx logs id 这种查日志的方式，好像很多命令行查日志都是这样。

```
docker logs 98f5aa2579f3
docker logs clever_almeida
```

6. 导出和导入容器

docker export [CONTAINER ID] > [本地路径]

cat [本地路径] | docker import - [镜像]

```
docker export 15c4c8e80a5a > hello.tar
cat hello.tar | docker import - hello:test1
```

7. 删除容器

docker rm -f [CONTAINER ID]：删除容器，可以删除运行的

docker rm：删除容器，必须是停止状态的容器

docker container prune：处理所有处于终止状态的容器

```
docker rm -f 15c4c8e80a5a
docker container prune
```

8. 运行一个web服务器

docker run -d -P training/webapp python app.py：这个是不行的，因为会默认用其他端口

docker run -d -p 5000:5000 training/webapp python app.py： 前面是访问的端口，后面的是docker内部的端口

docker run -d -p 127.0.0.1:5001:5000 training/webapp python app.py：可指定绑定的网址

docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py：可选择连接的方式为udp

```
docker run -d -p 5000:5000 training/webapp python app.py
docker run -d -p 127.0.0.1:5001:5000 training/webapp python app.py
docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py
```

9. Dockerfile

最近需要构建一个编译springboot项目的镜像，所以先编写这一章

[docker使用maven时修改镜像](https://www.jianshu.com/p/4d61dc51c98b)

首先把java和maven搞清楚

maven安装在 /usr/share/maven 目录下，要替换maven的settings文件

```
docker run -it maven:3.6-jdk-8 /bin/bash

```
