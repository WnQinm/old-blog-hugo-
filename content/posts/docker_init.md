---
title: "Docker help"
date: 2022-09-16T14:03:10+08:00
draft: false
tags: ["net"]
---

# intro
Docker是基于Go语言开发的开源项目
官网：https://www.docker.com/
文档：https://docs.docker.com/ 超级详细
仓库：https://hub.docker.com/

# Docker能做什么
一般，电脑有内核（Kernel），库/环境（Lib），以及各种应用app

## 虚拟机技术
模拟了绝大部分组成，导致一些缺点：
1、资源占用多
2、冗余步骤多
3、启动慢
![virtual-machine-tech](https://tva4.sinaimg.cn/large/007Z9xVHgy1h68cf4f1x0j30x60p5mzw.jpg)
## 容器化技术
没有模拟一个完整的操作系统，只打包了Lib和各种apps，这些docker共用一个内核，可以最大化压榨内核潜力，同时各个docker互不干扰，避免某个docker环境等配置失败导致机器不能正常使用
![docker-tech](https://tva2.sinaimg.cn/large/007Z9xVHgy1h68cic0sykj30xp0p8gos.jpg)
由于容器没有内核，也没有虚拟其他硬件，使得容器十分轻便，我们可以安装多个docker

**应用更快速的交付和部署**
传统：一堆帮助文档，安装程序
Docker：打包镜像发布测试，一键运行

**更便捷的升级和扩缩容**
可以类似搭积木来部署应用

**更简单的系统运维**
容器化之后，我们的开发，测试环境都是高度一致的

**更高效的计算资源利用**
Docker是内核级别的虚拟化，可以在一个物理机上运行很多的容器实例。服务器的性能可以被压榨到极致

# Docker基本组成
![docker-image1](http://blog.kevinyang.net/2020/07/30/docker-101-note-1/screen-shot-2020-03-19-at-9.01.25-am.png)

**镜像（image）**：
docker镜像类似于一个模板，可以通过这个模板来创建容器服务，xxx镜像->run->xxx01容器（提供服务器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的）
**容器（container）**：
Docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建的
启动，停止，删除（基本命令）
**仓库（repository）**：
类似于github，国外有dockerhub，国内阿里云等需要配置镜像加速

# install Docker

我这里是将docker安装到云服务器上，相关下载帮助文档如图所示
![docker-doc](https://tva3.sinaimg.cn/large/007Z9xVHgy1h68d0unaraj30hk11hqdx.jpg)

网速堪忧，最好提前在服务器部署clash

## 系统版本

```shell
$ cat /etc/os-release
NAME="Ubuntu"
VERSION="20.04 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal
```

## 帮助文档（ubuntu）：https://docs.docker.com/engine/install/ubuntu/

```shell
# uninstall old versions
sudo apt-get remove docker docker-engine docker.io containerd runc
```
## install using the repository

```shell
# set up the repository

# update some packages
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
# add Docker's official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
# set up the repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
只要没有报错就成功

## 设置阿里云镜像软件源

```shell
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

## install Docker Engine

(ce社区版，ee企业版)

```shell
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

(如果网络不好，下载超级慢)

## 启动docker

```shell
$ sudo systemctl start docker
$ sudo docker version
Client: Docker Engine - Community
 Version:           20.10.18
 API version:       1.41
 Go version:        go1.18.6
 Git commit:        b40c2f6
 Built:             Thu Sep  8 23:11:45 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.18
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.18.6
  Git commit:       e42327a
  Built:            Thu Sep  8 23:09:37 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.8
  GitCommit:        9cd3357b7fd7218e4aec3eae239db1f68a5a6ec6
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

## 测试hello-world

```shell
$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete
Digest: sha256:62af9efd515a25f84961b70f973a798d2eca956b1b2b026d0a4a63a3b0b6a3f2
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## 查看已有镜像列表

```shell
$ sudo docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB
```

# 卸载Docker

1、Uninstall the Docker Engine, CLI, Containerd, and Docker Compose packages:

```shell
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

2、Images, containers, volumes, or customized configuration files on your host are not automatically removed. To delete all images, containers, and volumes:

```shell
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

