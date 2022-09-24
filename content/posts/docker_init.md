---
title: "Docker"
date: 2022-09-18T01:32:42+08:00
draft: false
tags: ["net"]
---

# intro
Docker是基于Go语言开发的开源项目
官网：https://www.docker.com/
文档：https://docs.docker.com/ 超级详细
仓库：https://hub.docker.com/

# Docker能做什么
一般来说，电脑有内核（Kernel），库/环境（Lib），以及各种应用app

## 虚拟机技术
模拟了绝大部分组成，导致一些缺点：
1、资源占用多
2、冗余步骤多
3、启动慢
![virtual machine tech](https://tva4.sinaimg.cn/large/007Z9xVHgy1h68cf4f1x0j30x60p5mzw.jpg)

## 容器化技术
没有模拟一个完整的操作系统，只打包了Lib和各种apps，这些docker共用一个内核，可以最大化压榨内核潜力，同时各个docker互不干扰，避免某个docker环境等配置失败导致机器不能正常使用

![docker tech](https://tva2.sinaimg.cn/large/007Z9xVHgy1h68cic0sykj30xp0p8gos.jpg)

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

![docker image1](http://blog.kevinyang.net/2020/07/30/docker-101-note-1/screen-shot-2020-03-19-at-9.01.25-am.png)

**镜像（image）**：
docker镜像类似于一个模板，可以通过这个模板来创建容器服务，xxx镜像->run->xxx01容器（提供服务器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的）

**容器（container）**：
Docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建的
启动，停止，删除（基本命令）

**仓库（repository）**：
类似于github，国外有dockerhub，国内阿里云等需要配置镜像加速

# install Docker

我这里是将docker安装到云服务器上，相关下载帮助文档如图所示

![docker doc](https://tva3.sinaimg.cn/large/007Z9xVHgy1h68d0unaraj30hk11hqdx.jpg)

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

## 帮助文档（ubuntu）：

https://docs.docker.com/engine/install/ubuntu/

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
                curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo
                gpg --dearmor -o /etc/apt/keyrings/docker.gpg
                # set up the repository
                echo \
                  "deb [arch=$(dpkg --print-architecture)
                  signed-by=/etc/apt/keyrings/docker.gpg]
                  https://download.docker.com/linux/ubuntu \
                    $(lsb_release -cs) stable" | sudo tee
                    /etc/apt/sources.list.d/docker.list > /dev/null
```

只要没有报错就成功

## 设置阿里云镜像软件源

```shell
sudo add-apt-repository "deb [arch=amd64]
http://mirrors.aliyun.com/docker-ce/linux/ubuntu
$(lsb_release -cs) stable"
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

# Docker的常用命令

ps.后面一定位置开始我嫌麻烦，所以将`sudo docker`简写为`sdr`

## 帮助命令

```shell
docker version   # 显示docker的版本信息
docker info      # 显示docker的系统信息，包括镜像和容器的数量
docker <command> --help  # 帮助命令
```

帮助文档的地址：https://docs.docker.com/reference/

## 镜像命令

**docker images**查看所有本地主机上的镜像
```shell
$ sudo docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB

# 解释
REPOSITORY  镜像的仓库源
TAG         镜像的标签
IMAGE ID    镜像的id
CREATED     镜像的创建时间
SIZE        镜像的大小

# 可选项
  -a, --all     # 列出所有镜像
  -q, --quiet   # 只显示镜像的id
```

**docker search**搜索镜像
```shell
$ sudo docker search mysql
NAME                            DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                           MySQL is a widely used, open-source relation…   13187     [OK]
mariadb                         MariaDB Server is a high performing open sou…   5040      [OK]

$ sudo docker search mysql --filter=STARS=3000  #搜索stars不少于3000的镜像
```

**docker pull**下载镜像
```shell
# 下载镜像 docker pull 镜像名[:tag]
$ sudo docker pull mysql
Using default tag: latest # 如果不写 tag，默认就是 latest
latest: Pulling from library/mysql
051f419db9dd: Pull complete # 分层下载，docker image的核心 联合文件系统
7627573fa82a: Pull complete
a44b358d7796: Pull complete
95753aff4b95: Pull complete
a1fa3bee53f4: Pull complete
f5227e0d612c: Pull complete
b4b4368b1983: Pull complete
f26212810c32: Pull complete
d803d4215f95: Pull complete
d5358a7f7d07: Pull complete
435e8908cd69: Pull complete
Digest: sha256:b9532b1edea72b6cee12d9f5a78547bd3812ea5db842566e17f8b33291ed2921 # 签名（防伪标志）
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest  # 真实地址

# 二者等价：
docker pull mysql
docker pull docker.io/library/mysql:latest

# 指定版本下载
$ sudo docker pull mysql:5.7
5.7: Pulling from library/mysql
9815334b7810: Pull complete   # 如果之前下载过某些层，就会显示"Already exists 直接拿过来用而不重新下载
f85cb6fccbfd: Pull complete
b63612353671: Pull complete
447901201612: Pull complete
9b6bc806cc29: Pull complete
24ec1f4b3b0d: Pull complete
207ed1eb2fd4: Pull complete
27cbde3edd97: Pull complete
0a5aa35cc154: Pull complete
e6c92bf6471b: Pull complete
07b80de0d1af: Pull complete
Digest: sha256:c1bda6ecdbc63d3b0d3a3a3ce195de3dd755c4a0658ed782a16a0682216b9a48
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

**docker rmi**删除镜像
docker rmi -f 镜像id [镜像id] [镜像id] ...
```shell
$ sudo docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
mysql         latest    43fcfca0776d   35 hours ago    449MB
mysql         5.7       daff57b7d2d1   3 weeks ago     430MB
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB
$ sudo docker rmi -f daff57b7d2d1 # 根据id删除镜像
Untagged: mysql:5.7
Untagged: mysql@sha256:c1bda6ecdbc63d3b0d3a3a3ce195de3dd755c4a0658ed782a16a0682216b9a48
Deleted: sha256:daff57b7d2d1e009d0b271972f62dbf4de64b8cdb9cd646442aeda961e615f44
Deleted: sha256:a2b6de0d8fad38b5d19b0207decce5994598e15ae6aaf974733924dcbd8f04de
Deleted: sha256:f3bd23ad30ca816481c810885546988744e444e8b4f52c196c29d5e3f667157f
Deleted: sha256:7789a0eac346070602d9fae747fb5d81952454c6e11d5381cbc12a4825198d6e
Deleted: sha256:2cdfa876efe3302b715dad6bfd13cee0c512c58843200920953efe3355f5931c
Deleted: sha256:8d4cf9ffb69b960e2a2ebb157660b37a15ea645a71791aefc2b1600f1f603ebb
Deleted: sha256:d98069632a6bb3b2ba690a11cff2481a9b954aba58a7c3388484dd65ef8e1580
Deleted: sha256:0295feed0657a087e23700dcf0e41069bf8c565a092eea9426be99d8de5a45dc
Deleted: sha256:c9a9086bbafa8383c2002a50cce49e22bd060897dc121c18b41a9893b83816f5
Deleted: sha256:ed4bd9721438e36a8667ddcb461b958473d7b5f155df32155e08764648486087
Deleted: sha256:018f336482437233f5c9e135a18f5ba391448340d3ef2ec99fd88ab599906463
Deleted: sha256:bcf2a1ad6472a891de95b5132d013c064a07ec9995cb61b0cc0f8d4a4ea855fb

$ sudo docker rmi -f $(sudo docker images -aq) # $()传入多个参数，这里传入所有镜像的id然后删除
Untagged: mysql:latest
Untagged: mysql@sha256:b9532b1edea72b6cee12d9f5a78547bd3812ea5db842566e17f8b33291ed2921
Deleted: sha256:43fcfca0776df8e192d1647da2866237fbd9f8e875fb496e4ca887369b2dd995
Deleted: sha256:45d11760ca3e62bb36a589002b413a42c60c9b917b7a089b116c1ab69155aa4d
Deleted: sha256:af1876abdf5bbbef0ac13e24068669d600bf7de8aa74f31a43ae5f56b83331c2
Deleted: sha256:e1668f1334215580aa7a19beef5d1ee6c6ba121c305154f1ddd7253e21bf65e8
Deleted: sha256:91d92a76bd6a29d88aa511715731ac59d1df33c8e4f5b393dbc16c16b9c08b1c
Deleted: sha256:8608eb76a683654e96af6087a19f416c4498e46f1121e15d63d2ce983750a3a2
Deleted: sha256:fd95c084aad87623a6db2c76480f0765e16ae2ba6de68ce1aefdb7ea4e8fe120
Deleted: sha256:968e68a28e13606ce58651654f554c342f51d5f3ea7e792cdcb21809fefe1a4f
Deleted: sha256:15628dda9c1556632ae3577f5dd8ff16c6456fb95da60284783e8c1f89c588ae
Deleted: sha256:49904d4d7d64ce4a3b64466e4d9bdc624fa8acc760c6a622dcbde03a0ada9fed
Deleted: sha256:df0e3d67e1ecb153c572e3110d36a8bbc46ded2dc376395384c6171732241d90
Deleted: sha256:05dc728e5e49b5db657ec403b875f757afdd8d31f624eea76d706d6eee6395b2
Untagged: hello-world:latest
Untagged: hello-world@sha256:62af9efd515a25f84961b70f973a798d2eca956b1b2b026d0a4a63a3b0b6a3f2
Deleted: sha256:feb5d9fea6a5e9606aa995e879d862b825965ba48de054caab5ef356dc6b3412
```

## 容器命令

+ 有了镜像才可以创建容器，linux，下载一个centos镜像:`docker pull centos`

**新建容器并启动**
```shell
docker run [可选参数] image

# 参数说明
--name="Name"   # 容器名字 区分容器
-d          # 后台方式运行（linux本身是nohup）
-it         # 使用交互方式运行，进入容器查看内容
-p          # 指定容器的端口 -p 8080:8080
  -p ip:主机端口:容器端口
  -p 主机端口:容器端口
  -p 容器端口
  容器端口
-P          # 随机指定端口

# 测试，启动并进入容器
$ sudo docker run -it centos /bin/bash
[root@f4373afebe37 /]# ls # 查看容器内的centos，基础版本，很多命令不完善
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr

# 从容器中退回主机
[root@f4373afebe37 /]# exit
exit
$ ls
blog  clash
```

**列出所有运行中容器**
```shell
# docker ps 命令
    # 列出当前正在运行的容器
-a    # 列出当前正在运行的容器+带出历史运行过的容器
-n=?  # 显示最近创建的容器
-q    # 只显示容器的编号
```

**退出容器**
```shell
exit  # 直接容器停止并退出
CTRL + P + Q  # 容器不停止退出
```

**删除容器**
```shell
docker rm 容器id          # 删除指定容器，不能删除正在运行的容器
docker rm -f $(docker ps -aq) # 删除所有容器
docker ps -aq | xargs docker rm # 删除所有容器
```

**启动和停止容器**
```shell
docker start 容器id   # 启动容器
docker restart 容器id   # 重启容器
docker stop 容器id    # 停止当前正在运行的容器
docker kill 容器id    # 强制停止容器
```

## 常用其他命令

**后台启动容器**
```shell
# 命令 docker run -d 镜像名
$ sudo docker run -d centos

# docker ps 发现centos停止了

# 常见的坑，docker 容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

**查看日志**
```shell
docker logs -f -t --tail <num> 容器id
# 空挂容器会导致关机或者logs为空
# 后台开启一个容器并运行一个脚本
$ sudo docker run -d centos /bin/sh -c "while true;do echo hello;sleep 1;done"

# 显示日志
-tf     # 显示所有日志，t时间戳，f持续输出
-tail number# 要显示的日志条数
$ sudo docker logs -ft --tail 10 2cb886ae2fa9 # 10为日志条数，由于加了参数f，会持续输出日志
2022-09-16T10:33:50.928401034Z hello
2022-09-16T10:33:51.930308966Z hello
2022-09-16T10:33:52.932148793Z hello
2022-09-16T10:33:53.933995843Z hello
```

**查看容器中的进程信息**
```shell
# 命令 docker top 容器id
$ sudo docker top 2cb886ae2fa9
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                162268              162248              0                   18:32               ? 
root                164167              162268              0                   18:40               ? 
```

**查看镜像的元数据**
```shell
# 命令 docker inspect 容器id

$ sudo docker inspect 2cb886ae2fa9
[
    {
        "Id": "2cb886ae2fa927bc1bca61010b04a97f3aadf0a5e0d830c98dca6a920278017e",
        "Created": "2022-09-16T10:32:21.383810621Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo hello;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 162268,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-09-16T10:32:21.762354014Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/2cb886ae2fa927bc1bca61010b04a97f3aadf0a5e0d830c98dca6a920278017e/resolv.conf",        "HostnamePath": "/var/lib/docker/containers/2cb886ae2fa927bc1bca61010b04a97f3aadf0a5e0d830c98dca6a920278017e/hostname",
        "HostsPath": "/var/lib/docker/containers/2cb886ae2fa927bc1bca61010b04a97f3aadf0a5e0d830c98dca6a920278017e/hosts",
        "LogPath": "/var/lib/docker/containers/2cb886ae2fa927bc1bca61010b04a97f3aadf0a5e0d830c98dca6a920278017e/2cb886ae2fa927bc1bca61010b04a97f3aadf0a5e0d830c98dca6a920278017e-json.log",
        "Name": "/festive_mclaren",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "docker-default",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/5e5a494c7a50b728f7118cdcf59378061bfc825810cc1cdda3a61900898f31d3-init/diff:/var/lib/docker/overlay2/67c73992368fc175a0d2849bc97620a48f90ed375eabe859da0816b8eb4b25cd/diff",
                "MergedDir": "/var/lib/docker/overlay2/5e5a494c7a50b728f7118cdcf59378061bfc825810cc1cdda3a61900898f31d3/merged",
                "UpperDir": "/var/lib/docker/overlay2/5e5a494c7a50b728f7118cdcf59378061bfc825810cc1cdda3a61900898f31d3/diff",
                "WorkDir": "/var/lib/docker/overlay2/5e5a494c7a50b728f7118cdcf59378061bfc825810cc1cdda3a61900898f31d3/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "2cb886ae2fa9",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo hello;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "09f57f62adb9fc11d121a70bd0484a6e49284692ad39d86ba58d6bd52ffac681",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/09f57f62adb9",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "4ac1492efb6c8d55fcc381116917dbb3abcfb5bcde25de84f8efd084ccd018cf",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "4171a585caf462e0363f1976199c44032198de483e6ec43c17962f490b730c95",
                    "EndpointID": "4ac1492efb6c8d55fcc381116917dbb3abcfb5bcde25de84f8efd084ccd018cf",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

**进入当前正在运行的容器**

```shell
# 通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

# 命令
docker exec -it 容器id bashshell

$ sdr exec -it 2cb886ae2fa9 /bin/bash
[root@2cb886ae2fa9 /]# ls
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
[root@2cb886ae2fa9 /]# ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 10:32 ?        00:00:00 /bin/sh -c while true;do
root        1487       0  0 10:57 pts/0    00:00:00 /bin/bash
root        1519       1  0 10:57 ?        00:00:00 /usr/bin/coreutils --cor
root        1520    1487  0 10:57 pts/0    00:00:00 ps -ef

# 方式二
docker attach 容器id
$ sudo docker attach thisisanid

# 区别：
# exec进入容器后打开了一个新的命令行
# attach进入容器当前正在执行的终端，不会启动新的进程
```



**从容器内拷贝文件到主机上**
```shell
docker cp 容器id:容器内路径 目的主机路径

# 在容器内新建文件
[root@78e4d3ef944f /]# cd /home
[root@78e4d3ef944f home]# ls
[root@78e4d3ef944f home]# la
bash: la: command not found
[root@78e4d3ef944f home]# ls -al
total 8
drwxr-xr-x 2 root root 4096 Nov  3  2020 .
drwxr-xr-x 1 root root 4096 Sep 16 11:09 ..
[root@78e4d3ef944f home]# touch test.md

# 回到主机，拷贝文件到主机
[root@78e4d3ef944f home]# exit
exit
$ sdr cp 78e4d3ef944f:/home/test.md /home
$ cd /home

# 成功拷贝
$ ls
lighthouse  test.md  ubuntu

# 拷贝是一个手动过程，可以使用 -v 卷的技术，实现自动同步，即连同内外两个目录
```

## 小结

![summary](https://img-blog.csdnimg.cn/383865dd59f64bd88cbe6b4a56f1315c.png)

## 练习

### nginx

```shell
sdr pull nginx
sdr run -d --name nginx-test -p 7788:80 nginx
curl localhost:7788
```

pull nginx镜像
启动一个容器
curl本地7788端口

```html
<!--返回以下网页-->
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
外网通过7788端口访问到Linux，然后通过`-p 7788:80`将7788端口接入容器80端口，从而访问nginx

### tomcat

```shell
sdr pull tomcat
sdr run -d -p 6677:8080 --name tomcat-test tomcat
```
通过公网ip访问发现404（还是能访问的，只不过是404）

`sdr exec -it tomcat-test /bin/bash`进入容器，发现webapps为空

`cp -r webapps.dist/* webapps`把实例文档拷贝进去

再次访问公网ip:6677发现成功显示页面

# Docker镜像讲解

## 镜像是什么

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时的库、环境变量和配置文件。

所有的应用，直接打包docker镜像，就可以直接跑起来

如何得到镜像：

+ 从远程仓库下载
+ 手动拷贝
+ 自己制作一个镜像DockerFile

## Docker镜像加载原理

> UnionFS（联合文件系统）
> 

下载的时候看到的一层层的就是这个

UnionFS（联合文件系统）：Union文件系统是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。Union文件系统时Docker镜像的基础。镜像可以通过分层来进行集成，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，智能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录

> Docker镜像加载原理
> 

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS

bootfs(boot file system)主要包含bootloader和kernel，bootloader主要是引导加载kernel，linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层时bootfs。这一层与我们典型的Linux/Unix系统时一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs

rootfs(root file system)，在bootfs之上，包含的就是典型的Linux系统中的/dev,/proc,/bin,/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。

![UnionFS](https://tvax2.sinaimg.cn/large/007Z9xVHgy1h6a4yadmmmj30pw07zwj2.jpg)

平时虚拟机的CentOS有几个G，但是centos镜像只有二百多兆

对于一个精简的OS，rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以，因为底层直接用Host的kernel，自己只需要提供rootfs就可以了。由此可见对于不同的linux发行版，bootfs基本都是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs

> 分层的镜像
> 

下载镜像时，观察下载的日志输出，发现是一层一层的下载

**原因**：

资源共享。比如多个镜像都从相同的base镜像构建而来，那么宿主机只需在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。

查看镜像分层的方式可以通过 docker image inspect 命令

```shell
$ sdr image inspect 22afbacf753d
...
"RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:7ac795f5ab480bb362e19552cce3cd9780aba31de91aca50606e381f2b5fdc04",
                "sha256:24c3f9ffc7df0db8b16e98211592d96583c39ff5c8ea586350e4bfbb11663bce",
                "sha256:ca9d5bcb66762cb171518b1d28b1a2296ba0bd09bc5de8f5eff644a1b8f64043",
                "sha256:44dd6b099cde96414b9275866c1a43bbc8e69a29348e0e3501e0e30b4fb73213",
                "sha256:b8c503d0b5359439591163e5322340cf600d1eeb9c73214ed6d561b0e4586c58",
                "sha256:a4c678688c9ae89a07c707072edf5a8194a5a24e89a298ab0282d587b35aa90a"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

**理解**：

所有的Docker镜像都起始于一个基础镜像层，当进行修改或增加新内容时，就会在当前镜像层之上，创建新的镜像层。

eg. 加入基于 Ubuntu Liux 16.04 创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加Python包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层。

该镜像当前已经包含3个镜像层，如下图所示：

![image0](https://tva2.sinaimg.cn/large/007Z9xVHgy1h6a32ci1itj30nl0evn0i.jpg)

在添加额外的镜像层的同时，镜像始终保持时当前所有镜像的组合。下图中每个镜像时包含3个文件，而镜像包含了来自两个镜像层的6个文件

![image1](https://tvax1.sinaimg.cn/large/007Z9xVHgy1h6a3g1kzrgj30nm0cbq5g.jpg)

上图的镜像层跟之前图中的稍有区别，主要目的是便于展示文件。

下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有6个文件，这是因为最上层中的文件7是文件5的一个更新版本

![image2](https://tva2.sinaimg.cn/large/007Z9xVHgy1h6a3iugb44j30mu0gbn0q.jpg)

这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像当中。

Docker通过存储引擎（新版本采用快照机制）的方式来实现镜像层堆栈，并保证多镜像层对外展示为统一的文件系统。

Linux上可用的存储引擎有AUFS、Overlay2、Device Mapper、Btrfs以及ZFS。顾名思义，每种存储引擎都基于Linux中对应的文件系统或者设备技术，并且每种存储引擎都有其独有的性能特点。

Docker在Windows上仅支持windowsfilter一种存储引擎，该引擎基于NTFS文件系统之上实现了分层和CoW[1]。

下图展示了与系统显示相同的三层镜像。所有镜像层堆叠并合并，对外提供统一的视图。

![image3](https://tva3.sinaimg.cn/large/007Z9xVHgy1h6a3r5kggfj30s408nwgl.jpg)

> 特点
> 

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部
这一层就是通常说的容器层，容器之下的都叫镜像层

## commit镜像

```shell
# 和git差不多
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:TAG 
```

# 容器数据卷

## 什么是容器数据卷

docker理念：将应用和环境打包成一个镜像

but：如果数据都在容器中，那么容器一删除，数据就会丢失。

–->需求：数据可以持久化

容器之间可以有一个数据共享技术：Docker容器中产生的数据同步到本地

即卷技术，目录的挂载，将容器内的目录挂载到Linux上

**容器的持久化和同步操作，同时容器间也可以数据共享**

## 使用数据卷

```shell
# docker run -it -v 主机目录:容器内目录

sdr run -it -v /home/ubuntu/test:/home centos /bin/bash

```

无论容器是否开启，无论正向操作还是反向操作，指定通道文件均正常共享

优点：本地修改，容器自动同步

## 安装MySQL

详情查看dockerhub解释，其中`-e`是环境配置，配置root密码之类的

## 具名和匿名挂载

```shell
# 匿名挂载
-v 容器内路径 # 自动生成主机目录
$ sdr run -d -P --name nginx-test -v /etc/nginx nginx
# -P是随机映射端口

# 卷相关操作
sdr volume [参数]
$ sdr volume ls
DRIVER    VOLUME NAME
local     e971a7e87e336058a1b2466da64c75d2938c5c6b3b70f07ccc8ea6fbfd005673
# 匿名卷挂载

# 具名挂载
# -v 卷名:容器内路径
$ sdr run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
$ sdr volume ls
DRIVER    VOLUME NAME
local     e971a7e87e336058a1b2466da64c75d2938c5c6b3b70f07ccc8ea6fbfd005673
local     juming-nginx
```

查看具体位置

```shell
$ sdr volume inspect juming-nginx
[
    {
        "CreatedAt": "2022-09-20T01:09:40+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
# mountpoint就是具体挂载地址
```

所有docker容器内的卷，在没有指定目录的情况下都是在`/var/lib/docker/volumes/xxxx/_data`

```shell
cd /var/lib/docker/volumes
root@VM-4-3-ubuntu:/var/lib/docker/volumes# ls
backingFsBlockDev                                                 juming-nginx
e971a7e87e336058a1b2466da64c75d2938c5c6b3b70f07ccc8ea6fbfd005673  metadata.db
```

一般都是具名挂载，方便找到卷，或者自定主机路径

**拓展：**

```shell
# 有时会在容器后再加参数ro rw
ro：readonly
rw：readwrite

# 参数是对容器生效，ro指该文件只能从外部改变
sdr run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
sdr run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx

```

## Dockerfile初识

Dockerfile是用来构建docker镜像的构建文件，是命令脚本

通过这个脚本可以生成镜像，镜像一层一层的，脚本一个一个的命令，每个命令都是一层

```shell
$ mkdir docker-test-volume
$ cd docker-test-volume
$ vim dockerfile1
# 这里的每个命令,就是镜像的一层
FROM centos
# 挂载数据卷目录
# 通过docker inspect中的mounts查看挂载的目录
VOLUME ["/volume01","/volume02"]

CMD echo "---end---"

CMD /bin/bash

$ docker build -f dockerfile1 -t qinm/centos:latest .
# -f 设置地址
# -t tag,设置标签
Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM centos
 ---> 5d0da3dc9764
Step 2/4 : VOLUME ["volume01","volume02"]
 ---> Running in 502845653bb0
Removing intermediate container 502845653bb0
 ---> cc7399fde6c6
Step 3/4 : CMD echo "---end---"
 ---> Running in 1c6e2ad7f22b
Removing intermediate container 1c6e2ad7f22b
 ---> 02c44d547986
Step 4/4 : CMD /bin/bash
 ---> Running in 9fa10a85fe26
Removing intermediate container 9fa10a85fe26
 ---> 3f4f6e063605
Successfully built 3f4f6e063605
Successfully tagged qinm/centos:latest
```

## 数据卷容器

docker run 时,通过添加参数`\--volumes-from docker名`挂载其他容器的数据卷

似乎可以通过 -v 指定要挂载的容器

# Dockerfile

dockerfile 是用来构建docker镜像的文件,就是一个命令参数脚本

官方镜像都是一些基础镜像,我们需要构建自己的镜像

## DockerFile构建过程

**基础知识:**

1. 每个保留关键字(指令)都最好是大写字母
2. 执行从上到下顺序执行
3. \# 表示注释
4. 每一个指令都会创建提交一个新的镜像层,并提交

dockerfile –>dockerimages –>docker容器

## dockerfile指令

```shell
FROM			# 指定基础镜像
MAINTAINER		# 镜像是谁写的, 姓名+邮箱
RUN				# 镜像构建的时候需要运行的命令
ADD				# 步骤,添加其他东西(比如tomcat镜像之类的)
WORKDIR			# 镜像的工作目录
VOLUME			# 挂载的目录
EXPOSE			# 指定暴露的端口
CMD				# 指定这个容器要启动时候要运行的命令,只有最后一个会生效,可被替代
ENTRYPOINT		# 指定这个容器要启动时候要运行的命令,可以追加命令
ONBUILD			# 当构建一个被继承 Dockerfile 这个时候会运行该指令,是一个触发指令
COPY			# 类似add,将我们的文件拷贝到镜像中
ENV				# 构建的时候设置环境变量
```

**CMD和ENTRYPOINT区别:**

```SHELL
CMD ls -a
docker run -l时会将-l替换ls -a

ENTRYPOINT ls -a
docker run -l时会将-l追加到之后,变为ls -a -l
```

## 实操

```shell
# 编写dockerfile
$ vim mydockerfile-centos
FROM centos:7 # 需要指定centos:7不然yum会报错
MAINTAINER wnqinm<flidwala@gmail.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD /bin/bash
$ sdr build -f mydockerfile-centos -t mycentos:latest .
```
之后会输出以下信息:

```shell
Sending build context to Docker daemon  2.048kB
Step 1/8 : FROM centos:7
7: Pulling from library/centos
2d473b07cdd5: Pull complete
Digest: sha256:c73f515d06b0fa07bb18d8202035e739a494ce760aa73129f60f4bf2bd22b407
Status: Downloaded newer image for centos:7
 ---> eeb6ee3f44bd
Step 2/8 : MAINTAINER wnqinm<flidwala@gmail.com>
 ---> Running in 3f3163ef6fc6
Removing intermediate container 3f3163ef6fc6
 ---> 7cc6b3b953d1
Step 3/8 : ENV MYPATH /usr/local
 ---> Running in 91d3be697355
Removing intermediate container 91d3be697355
 ---> 3c456a5a6c70
Step 4/8 : WORKDIR $MYPATH
 ---> Running in 34d1a3844471
Removing intermediate container 34d1a3844471
 ---> 0aed096774e0
Step 5/8 : RUN yum -y install vim
 ---> Running in 8ff92ad9ec1e
Loaded plugins: fastestmirror, ovl
Determining fastest mirrors
 * base: mirrors.nju.edu.cn
 * extras: ftp.sjtu.edu.cn
 * updates: ftp.sjtu.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package vim-enhanced.x86_64 2:7.4.629-8.el7_9 will be installed
--> Processing Dependency: vim-common = 2:7.4.629-8.el7_9 for package: 2:vim-enhanced-7.4.629-8.el7_9.x86_64
--> Processing Dependency: which for package: 2:vim-enhanced-7.4.629-8.el7_9.x86_64
--> Processing Dependency: perl(:MODULE_COMPAT_5.16.3) for package: 2:vim-enhanced-7.4.629-8.el7_9.x86_64
--> Processing Dependency: libperl.so()(64bit) for package: 2:vim-enhanced-7.4.629-8.el7_9.x86_64
--> Processing Dependency: libgpm.so.2()(64bit) for package: 2:vim-enhanced-7.4.629-8.el7_9.x86_64
--> Running transaction check
---> Package gpm-libs.x86_64 0:1.20.7-6.el7 will be installed
---> Package perl.x86_64 4:5.16.3-299.el7_9 will be installed
--> Processing Dependency: perl(Socket) >= 1.3 for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Scalar::Util) >= 1.10 for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl-macros for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(threads::shared) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(threads) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(constant) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Time::Local) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Time::HiRes) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Storable) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Socket) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Scalar::Util) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Pod::Simple::XHTML) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Pod::Simple::Search) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Getopt::Long) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Filter::Util::Call) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(File::Temp) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(File::Spec::Unix) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(File::Spec::Functions) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(File::Spec) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(File::Path) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Exporter) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Cwd) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Carp) for package: 4:perl-5.16.3-299.el7_9.x86_64
---> Package perl-libs.x86_64 4:5.16.3-299.el7_9 will be installed
---> Package vim-common.x86_64 2:7.4.629-8.el7_9 will be installed
--> Processing Dependency: vim-filesystem for package: 2:vim-common-7.4.629-8.el7_9.x86_64
---> Package which.x86_64 0:2.20-7.el7 will be installed
--> Running transaction check
---> Package perl-Carp.noarch 0:1.26-244.el7 will be installed
---> Package perl-Exporter.noarch 0:5.68-3.el7 will be installed
---> Package perl-File-Path.noarch 0:2.09-2.el7 will be installed
---> Package perl-File-Temp.noarch 0:0.23.01-3.el7 will be installed
---> Package perl-Filter.x86_64 0:1.49-3.el7 will be installed
---> Package perl-Getopt-Long.noarch 0:2.40-3.el7 will be installed
--> Processing Dependency: perl(Pod::Usage) >= 1.14 for package: perl-Getopt-Long-2.40-3.el7.noarch
--> Processing Dependency: perl(Text::ParseWords) for package: perl-Getopt-Long-2.40-3.el7.noarch
---> Package perl-PathTools.x86_64 0:3.40-5.el7 will be installed
---> Package perl-Pod-Simple.noarch 1:3.28-4.el7 will be installed
--> Processing Dependency: perl(Pod::Escapes) >= 1.04 for package: 1:perl-Pod-Simple-3.28-4.el7.noarch
--> Processing Dependency: perl(Encode) for package: 1:perl-Pod-Simple-3.28-4.el7.noarch
---> Package perl-Scalar-List-Utils.x86_64 0:1.27-248.el7 will be installed
---> Package perl-Socket.x86_64 0:2.010-5.el7 will be installed
---> Package perl-Storable.x86_64 0:2.45-3.el7 will be installed
---> Package perl-Time-HiRes.x86_64 4:1.9725-3.el7 will be installed
---> Package perl-Time-Local.noarch 0:1.2300-2.el7 will be installed
---> Package perl-constant.noarch 0:1.27-2.el7 will be installed
---> Package perl-macros.x86_64 4:5.16.3-299.el7_9 will be installed
---> Package perl-threads.x86_64 0:1.87-4.el7 will be installed
---> Package perl-threads-shared.x86_64 0:1.43-6.el7 will be installed
---> Package vim-filesystem.x86_64 2:7.4.629-8.el7_9 will be installed
--> Running transaction check
---> Package perl-Encode.x86_64 0:2.51-7.el7 will be installed
---> Package perl-Pod-Escapes.noarch 1:1.04-299.el7_9 will be installed
---> Package perl-Pod-Usage.noarch 0:1.63-3.el7 will be installed
--> Processing Dependency: perl(Pod::Text) >= 3.15 for package: perl-Pod-Usage-1.63-3.el7.noarch
--> Processing Dependency: perl-Pod-Perldoc for package: perl-Pod-Usage-1.63-3.el7.noarch
---> Package perl-Text-ParseWords.noarch 0:3.29-4.el7 will be installed
--> Running transaction check
---> Package perl-Pod-Perldoc.noarch 0:3.20-4.el7 will be installed
--> Processing Dependency: perl(parent) for package: perl-Pod-Perldoc-3.20-4.el7.noarch
--> Processing Dependency: perl(HTTP::Tiny) for package: perl-Pod-Perldoc-3.20-4.el7.noarch
--> Processing Dependency: groff-base for package: perl-Pod-Perldoc-3.20-4.el7.noarch
---> Package perl-podlators.noarch 0:2.5.1-3.el7 will be installed
--> Running transaction check
---> Package groff-base.x86_64 0:1.22.2-8.el7 will be installed
---> Package perl-HTTP-Tiny.noarch 0:0.033-3.el7 will be installed
---> Package perl-parent.noarch 1:0.225-244.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package                    Arch       Version                Repository   Size
================================================================================
Installing:
 vim-enhanced               x86_64     2:7.4.629-8.el7_9      updates     1.1 M
Installing for dependencies:
 gpm-libs                   x86_64     1.20.7-6.el7           base         32 k
 groff-base                 x86_64     1.22.2-8.el7           base        942 k
 perl                       x86_64     4:5.16.3-299.el7_9     updates     8.0 M
 perl-Carp                  noarch     1.26-244.el7           base         19 k
 perl-Encode                x86_64     2.51-7.el7             base        1.5 M
 perl-Exporter              noarch     5.68-3.el7             base         28 k
 perl-File-Path             noarch     2.09-2.el7             base         26 k
 perl-File-Temp             noarch     0.23.01-3.el7          base         56 k
 perl-Filter                x86_64     1.49-3.el7             base         76 k
 perl-Getopt-Long           noarch     2.40-3.el7             base         56 k
 perl-HTTP-Tiny             noarch     0.033-3.el7            base         38 k
 perl-PathTools             x86_64     3.40-5.el7             base         82 k
 perl-Pod-Escapes           noarch     1:1.04-299.el7_9       updates      52 k
 perl-Pod-Perldoc           noarch     3.20-4.el7             base         87 k
 perl-Pod-Simple            noarch     1:3.28-4.el7           base        216 k
 perl-Pod-Usage             noarch     1.63-3.el7             base         27 k
 perl-Scalar-List-Utils     x86_64     1.27-248.el7           base         36 k
 perl-Socket                x86_64     2.010-5.el7            base         49 k
 perl-Storable              x86_64     2.45-3.el7             base         77 k
 perl-Text-ParseWords       noarch     3.29-4.el7             base         14 k
 perl-Time-HiRes            x86_64     4:1.9725-3.el7         base         45 k
 perl-Time-Local            noarch     1.2300-2.el7           base         24 k
 perl-constant              noarch     1.27-2.el7             base         19 k
 perl-libs                  x86_64     4:5.16.3-299.el7_9     updates     690 k
 perl-macros                x86_64     4:5.16.3-299.el7_9     updates      44 k
 perl-parent                noarch     1:0.225-244.el7        base         12 k
 perl-podlators             noarch     2.5.1-3.el7            base        112 k
 perl-threads               x86_64     1.87-4.el7             base         49 k
 perl-threads-shared        x86_64     1.43-6.el7             base         39 k
 vim-common                 x86_64     2:7.4.629-8.el7_9      updates     5.9 M
 vim-filesystem             x86_64     2:7.4.629-8.el7_9      updates      11 k
 which                      x86_64     2.20-7.el7             base         41 k

Transaction Summary
================================================================================
Install  1 Package (+32 Dependent packages)

Total download size: 19 M
Installed size: 63 M
Downloading packages:
warning: /var/cache/yum/x86_64/7/base/packages/gpm-libs-1.20.7-6.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
Public key for gpm-libs-1.20.7-6.el7.x86_64.rpm is not installed
Public key for perl-Pod-Escapes-1.04-299.el7_9.noarch.rpm is not installed
--------------------------------------------------------------------------------
Total                                              1.3 MB/s |  19 MB  00:15
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Importing GPG key 0xF4A80EB5:
 Userid     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Package    : centos-release-7-9.2009.0.el7.centos.x86_64 (@CentOS)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : gpm-libs-1.20.7-6.el7.x86_64                                1/33
  Installing : 2:vim-filesystem-7.4.629-8.el7_9.x86_64                     2/33
  Installing : 2:vim-common-7.4.629-8.el7_9.x86_64                         3/33
  Installing : which-2.20-7.el7.x86_64                                     4/33
install-info: No such file or directory for /usr/share/info/which.info.gz
  Installing : groff-base-1.22.2-8.el7.x86_64                              5/33
  Installing : 1:perl-parent-0.225-244.el7.noarch                          6/33
  Installing : perl-HTTP-Tiny-0.033-3.el7.noarch                           7/33
  Installing : perl-podlators-2.5.1-3.el7.noarch                           8/33
  Installing : perl-Pod-Perldoc-3.20-4.el7.noarch                          9/33
  Installing : 1:perl-Pod-Escapes-1.04-299.el7_9.noarch                   10/33
  Installing : perl-Encode-2.51-7.el7.x86_64                              11/33
  Installing : perl-Text-ParseWords-3.29-4.el7.noarch                     12/33
  Installing : perl-Pod-Usage-1.63-3.el7.noarch                           13/33
  Installing : 4:perl-macros-5.16.3-299.el7_9.x86_64                      14/33
  Installing : perl-Storable-2.45-3.el7.x86_64                            15/33
  Installing : perl-Exporter-5.68-3.el7.noarch                            16/33
  Installing : perl-constant-1.27-2.el7.noarch                            17/33
  Installing : perl-Socket-2.010-5.el7.x86_64                             18/33
  Installing : perl-Time-Local-1.2300-2.el7.noarch                        19/33
  Installing : perl-Carp-1.26-244.el7.noarch                              20/33
  Installing : perl-PathTools-3.40-5.el7.x86_64                           21/33
  Installing : perl-Scalar-List-Utils-1.27-248.el7.x86_64                 22/33
  Installing : 1:perl-Pod-Simple-3.28-4.el7.noarch                        23/33
  Installing : perl-File-Temp-0.23.01-3.el7.noarch                        24/33
  Installing : perl-File-Path-2.09-2.el7.noarch                           25/33
  Installing : perl-threads-shared-1.43-6.el7.x86_64                      26/33
  Installing : perl-threads-1.87-4.el7.x86_64                             27/33
  Installing : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                      28/33
  Installing : perl-Filter-1.49-3.el7.x86_64                              29/33
  Installing : 4:perl-libs-5.16.3-299.el7_9.x86_64                        30/33
  Installing : perl-Getopt-Long-2.40-3.el7.noarch                         31/33
  Installing : 4:perl-5.16.3-299.el7_9.x86_64                             32/33
  Installing : 2:vim-enhanced-7.4.629-8.el7_9.x86_64                      33/33
  Verifying  : perl-HTTP-Tiny-0.033-3.el7.noarch                           1/33
  Verifying  : perl-threads-shared-1.43-6.el7.x86_64                       2/33
  Verifying  : perl-Storable-2.45-3.el7.x86_64                             3/33
  Verifying  : groff-base-1.22.2-8.el7.x86_64                              4/33
  Verifying  : perl-Exporter-5.68-3.el7.noarch                             5/33
  Verifying  : perl-constant-1.27-2.el7.noarch                             6/33
  Verifying  : perl-PathTools-3.40-5.el7.x86_64                            7/33
  Verifying  : 4:perl-macros-5.16.3-299.el7_9.x86_64                       8/33
  Verifying  : 2:vim-enhanced-7.4.629-8.el7_9.x86_64                       9/33
  Verifying  : 1:perl-parent-0.225-244.el7.noarch                         10/33
  Verifying  : perl-Socket-2.010-5.el7.x86_64                             11/33
  Verifying  : which-2.20-7.el7.x86_64                                    12/33
  Verifying  : 2:vim-filesystem-7.4.629-8.el7_9.x86_64                    13/33
  Verifying  : perl-File-Temp-0.23.01-3.el7.noarch                        14/33
  Verifying  : 1:perl-Pod-Simple-3.28-4.el7.noarch                        15/33
  Verifying  : perl-Time-Local-1.2300-2.el7.noarch                        16/33
  Verifying  : 1:perl-Pod-Escapes-1.04-299.el7_9.noarch                   17/33
  Verifying  : perl-Carp-1.26-244.el7.noarch                              18/33
  Verifying  : 2:vim-common-7.4.629-8.el7_9.x86_64                        19/33
  Verifying  : perl-Scalar-List-Utils-1.27-248.el7.x86_64                 20/33
  Verifying  : perl-Pod-Usage-1.63-3.el7.noarch                           21/33
  Verifying  : perl-Encode-2.51-7.el7.x86_64                              22/33
  Verifying  : perl-Pod-Perldoc-3.20-4.el7.noarch                         23/33
  Verifying  : perl-podlators-2.5.1-3.el7.noarch                          24/33
  Verifying  : 4:perl-5.16.3-299.el7_9.x86_64                             25/33
  Verifying  : perl-File-Path-2.09-2.el7.noarch                           26/33
  Verifying  : perl-threads-1.87-4.el7.x86_64                             27/33
  Verifying  : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                      28/33
  Verifying  : gpm-libs-1.20.7-6.el7.x86_64                               29/33
  Verifying  : perl-Filter-1.49-3.el7.x86_64                              30/33
  Verifying  : perl-Getopt-Long-2.40-3.el7.noarch                         31/33
  Verifying  : perl-Text-ParseWords-3.29-4.el7.noarch                     32/33
  Verifying  : 4:perl-libs-5.16.3-299.el7_9.x86_64                        33/33

Installed:
  vim-enhanced.x86_64 2:7.4.629-8.el7_9

Dependency Installed:
  gpm-libs.x86_64 0:1.20.7-6.el7
  groff-base.x86_64 0:1.22.2-8.el7
  perl.x86_64 4:5.16.3-299.el7_9
  perl-Carp.noarch 0:1.26-244.el7
  perl-Encode.x86_64 0:2.51-7.el7
  perl-Exporter.noarch 0:5.68-3.el7
  perl-File-Path.noarch 0:2.09-2.el7
  perl-File-Temp.noarch 0:0.23.01-3.el7
  perl-Filter.x86_64 0:1.49-3.el7
  perl-Getopt-Long.noarch 0:2.40-3.el7
  perl-HTTP-Tiny.noarch 0:0.033-3.el7
  perl-PathTools.x86_64 0:3.40-5.el7
  perl-Pod-Escapes.noarch 1:1.04-299.el7_9
  perl-Pod-Perldoc.noarch 0:3.20-4.el7
  perl-Pod-Simple.noarch 1:3.28-4.el7
  perl-Pod-Usage.noarch 0:1.63-3.el7
  perl-Scalar-List-Utils.x86_64 0:1.27-248.el7
  perl-Socket.x86_64 0:2.010-5.el7
  perl-Storable.x86_64 0:2.45-3.el7
  perl-Text-ParseWords.noarch 0:3.29-4.el7
  perl-Time-HiRes.x86_64 4:1.9725-3.el7
  perl-Time-Local.noarch 0:1.2300-2.el7
  perl-constant.noarch 0:1.27-2.el7
  perl-libs.x86_64 4:5.16.3-299.el7_9
  perl-macros.x86_64 4:5.16.3-299.el7_9
  perl-parent.noarch 1:0.225-244.el7
  perl-podlators.noarch 0:2.5.1-3.el7
  perl-threads.x86_64 0:1.87-4.el7
  perl-threads-shared.x86_64 0:1.43-6.el7
  vim-common.x86_64 2:7.4.629-8.el7_9
  vim-filesystem.x86_64 2:7.4.629-8.el7_9
  which.x86_64 0:2.20-7.el7

Complete!
Removing intermediate container 8ff92ad9ec1e
 ---> 8454f6fab960
Step 6/8 : RUN yum -y install net-tools
 ---> Running in 173fc318f351
Loaded plugins: fastestmirror, ovl
Loading mirror speeds from cached hostfile
 * base: mirrors.nju.edu.cn
 * extras: ftp.sjtu.edu.cn
 * updates: ftp.sjtu.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package net-tools.x86_64 0:2.0-0.25.20131004git.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package         Arch         Version                          Repository  Size
================================================================================
Installing:
 net-tools       x86_64       2.0-0.25.20131004git.el7         base       306 k

Transaction Summary
================================================================================
Install  1 Package

Total download size: 306 k
Installed size: 917 k
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : net-tools-2.0-0.25.20131004git.el7.x86_64                    1/1
  Verifying  : net-tools-2.0-0.25.20131004git.el7.x86_64                    1/1

Installed:
  net-tools.x86_64 0:2.0-0.25.20131004git.el7

Complete!
Removing intermediate container 173fc318f351
 ---> 6a1de638647d
Step 7/8 : EXPOSE 80
 ---> Running in b307ec95e208
Removing intermediate container b307ec95e208
 ---> 23b8c33b96c8
Step 8/8 : CMD /bin/bash
 ---> Running in 46eb85a62efa
Removing intermediate container 46eb85a62efa
 ---> 847c2b869d18
Successfully built 847c2b869d18
Successfully tagged mycentos:latest
```

发现images列表已经有了mycentos镜像

```shell
$ sdr images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
mycentos     latest    847c2b869d18   2 minutes ago   624MB
```

通过docker history查看镜像构建历史

```shell
$ sdr history 847
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
847c2b869d18   5 minutes ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "/bin…   0B
23b8c33b96c8   5 minutes ago   /bin/sh -c #(nop)  EXPOSE 80                    0B
6a1de638647d   5 minutes ago   /bin/sh -c yum -y install net-tools             182MB
8454f6fab960   5 minutes ago   /bin/sh -c yum -y install vim                   237MB
0aed096774e0   6 minutes ago   /bin/sh -c #(nop) WORKDIR /usr/local            0B
3c456a5a6c70   6 minutes ago   /bin/sh -c #(nop)  ENV MYPATH=/usr/local        0B
7cc6b3b953d1   6 minutes ago   /bin/sh -c #(nop)  MAINTAINER wnqinm<flidwal…   0B
eeb6ee3f44bd   12 months ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      12 months ago   /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>      12 months ago   /bin/sh -c #(nop) ADD file:b3ebbe8bd304723d4…   204MB
```

可以通过此命令查看各种官方镜像的构建过程

## 发布镜像到dockerhub

docker login -u 用户名

docker push 镜像

注意:镜像必须是 用户名/镜像名:tag   (eg. wnqinm/nginx:latest)

# docker 网络

```shell
$ ip addr
# 本机回环地址
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
# 服务器提供商(这里是腾讯云)内网地址
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 52:54:00:a8:6e:ca brd ff:ff:ff:ff:ff:ff
    inet 10.0.4.3/22 brd 10.0.7.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:fea8:6eca/64 scope link
       valid_lft forever preferred_lft forever
# docker0地址
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:6c:11:67:c5 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:6cff:fe11:67c5/64 scope link
       valid_lft forever preferred_lft forever
```

查看一下容器内部地址

```shell
$ sdr ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED      STATUS
9293afcf31a6   3d1465         "/bin/bash"   4 days ago   Up 2 seconds

# 可以在exec后直接加命令,不进入容器直接执行
$ sdr exec -it 9293 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
92: eth0@if93: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
# 每一个容器都会有一个类似上面的eth0@if93的网卡对应的ip地址172.17.0.2/16,这是docker分配的

# ping一下
$ ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.094 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.049 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.044 ms
64 bytes from 172.17.0.2: icmp_seq=4 ttl=64 time=0.046 ms
^C
--- 172.17.0.2 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3079ms
# 可以ping通

# 容器反过来ping主机
$ sdr exec -it 9293 ping 172.17.0.1
PING 172.17.0.1 (172.17.0.1) 56(84) bytes of data.
64 bytes from 172.17.0.1: icmp_seq=1 ttl=64 time=0.055 ms
64 bytes from 172.17.0.1: icmp_seq=2 ttl=64 time=0.050 ms
64 bytes from 172.17.0.1: icmp_seq=3 ttl=64 time=0.053 ms
64 bytes from 172.17.0.1: icmp_seq=4 ttl=64 time=0.062 ms
^C
--- 172.17.0.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3059ms
# 也能ping通
```

> 原理

首先,docker0的ip:172.17.0.1,容器9293的ip:172.17.0.2,是在同一网段下的

1. 每次运行一个容器,docker就会分配一个ip,我们只要安装了docker,就会有一个网卡docker0

桥接模式,使用的是 evth-pair 技术

2. 再次测试ip addr:

```shell
$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 52:54:00:a8:6e:ca brd ff:ff:ff:ff:ff:ff
    inet 10.0.4.3/22 brd 10.0.7.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:fea8:6eca/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:6c:11:67:c5 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:6cff:fe11:67c5/64 scope link
       valid_lft forever preferred_lft forever
93: veth8b8785a@if92: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether 6a:fc:42:63:d2:cf brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::68fc:42ff:fe63:d2cf/64 scope link
       valid_lft forever preferred_lft forever
```

发现多了一个网卡

可以明白容器带来的网卡，都是一对对的

evth-pair就是一对虚拟设备接口，都是成对出现的，一端连着协议，一端彼此相连

所以evth-pair被用来充当桥梁

![evth-pair](https://tvax3.sinaimg.cn/large/007Z9xVHgy1h6gfgwgbtgj30xy0d6t9u.jpg)

主机上显示93: veth8b8785a@if92，93号，指向92号。而容器内显示92: eth0@if93，92号，指向93号，说明这两块网卡是互相绑定的

3. 容器间ping

```shell
$ sdr exec -it 9293 ping 172.20.0.2
PING 172.20.0.2 (172.20.0.2) 56(84) bytes of data.
^C
--- 172.20.0.2 ping statistics ---
8 packets transmitted, 0 received, 100% packet loss, time 7170ms
```

这里ping不通的原因是两个容器的ip字段不同，即不在一个网桥下

如果ping同一网桥下的容器：

```shell
$ sdr exec -it 9293 ping 172.17.0.3
```

此时就能ping通，我这里就不试了

网络模型图如下：

![docker0](https://tva2.sinaimg.cn/large/007Z9xVHgy1h6gfyu20jej30z20mmjut.jpg)

所有容器在不指定网络的情况下，都是docker0路由的，docker会给容器分配一个默认的可用ip

ip数目：255.255.255.255/16表示前16位是域，后16位是可分配的ip，此时255.255.0.1到255.255.255.254可用ip；255.255.255.255/24表示前24位是域，后8位是可分配的ip，此时255.255.255.1到255.255.255.254是可用ip

**可以通过修改/etc/hosts添加容器名实现通过容器名ping通网络**

## 自定义网络

```shell
# 查看所有的docker网络
$ sdr network ls
NETWORK ID     NAME                  DRIVER    SCOPE
4171a585caf4   bridge                bridge    local
3c8399a3e6b0   code-server_default   bridge    local
bf9782f177ef   host                  host      local
8c71ba624052   none                  null      local
```

**网络模式**

bridge：桥接 docker(默认)

none：不配置网络

host：和宿主机共享网络

container：容器内网络连通（用的少，局限性很大）

**测试**

```shell
# 启动容器时可以指定网络
docker run -d -P --name tomcat01 --net bridge tomcat
# 这里的bridge就是前面network ls的第一个网络
# docker0特点:默认,域名不能访问
```

**自定义网络**

```shell
# --driver bridge  设置网络模式
# --subnet 192.168.0.0/16   设置子网地址
# --gateway 192.168.0.1   网关
$ sdr network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
41eac8b4e2753f31116056357cabae0efc0f34259010c2b5b9244acceac34387
$ sdr network ls
NETWORK ID     NAME                  DRIVER    SCOPE
41eac8b4e275   mynet                 bridge    local
$ sdr network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "41eac8b4e2753f31116056357cabae0efc0f34259010c2b5b9244acceac34387",
        "Created": "2022-09-23T15:43:55.370425457+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

## 网络连通

```shell
$ sdr network connect --help

Usage:  docker network connect [OPTIONS] NETWORK CONTAINER

Connect a container to a network

Options:
      --alias strings           Add network-scoped alias for the container
      --driver-opt strings      driver options for the network
      --ip string               IPv4 address (e.g., 172.30.100.104)
      --ip6 string              IPv6 address (e.g., 2001:db8::33)
      --link list               Add link to another container
      --link-local-ip strings   Add a link-local address for the container
```

原理是再给容器分配一个ip地址，干脆利落直接将容器添加到其他网络中。

