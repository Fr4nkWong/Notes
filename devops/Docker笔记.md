---
title: Docker笔记
birth: 2019-05-23
category:
- devops
---

## 前言

docker的背后实质上是一种叫做`进程隔离`的概念，这个概念可追溯到1980年代UNIX V7里开发的`chroot`系统调用，作用是"重定向进程及其子进程的根目录到一个文件系统上的新位置，该进程被限制在这个指定的根目录里"，隔离出来的新环境称为“chroot jail"。

而后2000年代FreeBSD系统支持jail命令用于隔离出进程的完整视图(独立进程和用户体系，并为jail环境分配独立ip地址)，即真正实现了`进程沙箱`(实现是基于操作系统级别的隔离而非硬件虚拟化技术)。

2010年代后，云服务(`IaaS/PaaS/SaaS`)的发展让Paas兴起的同时，linux实现了`Cgroups`以支持`容器`，这使进程沙箱技术爆发式发展和备受推广，docker就是应运而生的初级产物。


## What
基于 Linux 内核的cgroup，namespace，以及 AUFS 类的 Union FS 等技术，对进程进行封装隔离，属于操作系统层面的虚拟化技术。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。容器就是将软件打包成标准化单元，以用于开发、交付和部署。
- Image(镜像)：镜像是一个分层存储的架构的文件系统(代码、运行时环境、系统工具、系统库、配置文件)，可理解为**静态的**、可以被用户互相分享的文件。用户不能对它操作，只能pull别人的镜像或者push自己的镜像。
- Container(容器)：镜像运行时的实体(表现形式类似于linux的iso文件的的Live CD模式)。容器实质上是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的 命名空间。同Live CD模式存在弊端，即用户关机后作出的修改安装的软件全部消失。**如果要保存修改，需要将当前容器封装成一个新的镜像，这样下次启动这个新的镜像后之前作出的修改还都在**。故容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有文件写入操作，都应该使用**数据卷(Volume)**或绑定宿主目录，在这些位置读写会跳过容器存储层，直接对宿主(或网络存储)发生读写，其性能和稳定性更高。
- Repository(仓库)：Docker仓库类似于git的代码仓库。一个Docker Registry中可包含多个Repository，每个Repository可包含多个Tag，每个Tag对应一个Image。<仓库名>:<标签> 来制定具体是这个软件哪个版本的景象，标签默认为latest。

## How

### Work

容器 = 镜像 + 可读层。并且容器的定义并没有提及是否要运行容器。

### Instruction
```
# 拉取镜像
$ docker pull registry.hub.docker.com/node:latest

# 拉取镜像（默认远端仓库地址）
$ docker pull node:latest

# 推到远端仓库（默认远端仓库地址）
$ docker push <username/repository:tag>

# 查看本地镜像仓库
$ docker images

# 查看当前正在运行的容器
$ docker ps <-ins>

# 后台启动一个容器后，想要进入此容器...
$ docker attach <image_id>

# 容器->镜像 (指令格式类似git)
$ docker commit -m " " -a <username> <image_id> <username/repository:tag> 

# 新建+启动容器
$ docker run <-ins> <container_name||container_id>
$ docker run <-ins> <repository:tag> <dir>

# 进入容器的命令行
$ docker exec it <image_name||image_id> bash
$ docker attach <image_name||image_id> bash //退出容器后容器会停止

# 删除容器
$ docker rm container_id

# 删除镜像(删除镜像前必须先删除以此镜像为基础的容器,哪怕是已经停止的容器)
$ docker rmi image_id

# 查看WEB应用程序容器的进程
$ docker top <container_name>

# 查看Docker的底层信息
$ docker inspect <container_name>
```
<img src='./img/docker命令.png' />

## Where&When
- 简化部署成本：docker使整个研发流程具备环境一致性，不需要担心开发、测试、生产环境下各种环境配置的问题。装好docker的server直接拉取到目标镜像，启动即可。
- 隔离应用：具有沙盒极值，提高了应用和server的安全性和稳定性。同一个server上跑着的多个容器互不干扰。若某个应用崩掉了，server os不会崩，大不了重新启动一个镜像。

## 容器与虚拟机的关系
虚拟机擅长于彻底隔离整个运行环境，而 Docker通常用于隔离不同的应用 。
<img src='./img/容器&虚拟机对比图.png'  />
两者也是可以共存的。Eg：硬件系统上装OS，OS上装多个虚拟机，虚拟机上装多个容器...
<img src='./img/容器&虚拟机对比图1.png'  />

## 小结

## 参考

https://weibo.com/ttarticle/p/show?id=2309404367940366520531