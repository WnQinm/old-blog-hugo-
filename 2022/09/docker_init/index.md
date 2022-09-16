# Docker help


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
# Docker的常用命令

ps.后面一定位置开始，我嫌麻烦，所以将`sudo docker`简写为`sdr`

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
Using default tag: latest	# 如果不写 tag，默认就是 latest
latest: Pulling from library/mysql
051f419db9dd: Pull complete	# 分层下载，docker image的核心 联合文件系统
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
Digest: sha256:b9532b1edea72b6cee12d9f5a78547bd3812ea5db842566e17f8b33291ed2921	# 签名（防伪标志）
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest	# 真实地址

# 二者等价：
docker pull mysql
docker pull docker.io/library/mysql:latest

# 指定版本下载
$ sudo docker pull mysql:5.7
5.7: Pulling from library/mysql
9815334b7810: Pull complete		# 如果之前下载过某些层，就会显示"Already exists 直接拿过来用而不重新下载
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
$ sudo docker rmi -f daff57b7d2d1	# 根据id删除镜像
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
--name="Name"		# 容器名字 区分容器
-d					# 后台方式运行（linux本身是nohup）
-it					# 使用交互方式运行，进入容器查看内容
-p					# 指定容器的端口 -p 8080:8080
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口
	-p 容器端口
	容器端口
-P					# 随机指定端口

# 测试，启动并进入容器
$ sudo docker run -it centos /bin/bash
[root@f4373afebe37 /]# ls	# 查看容器内的centos，基础版本，很多命令不完善
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
-a 		# 列出当前正在运行的容器+带出历史运行过的容器
-n=?	# 显示最近创建的容器
-q		# 只显示容器的编号
```

**退出容器**
```shell
exit	# 直接容器停止并退出
CTRL + P + Q 	# 容器不停止退出
```

**删除容器**
```shell
docker rm 容器id					# 删除指定容器，不能删除正在运行的容器
docker rm -f $(docker ps -aq)	# 删除所有容器
docker ps -aq | xargs docker rm # 删除所有容器
```

**启动和停止容器**
```shell
docker start 容器id		# 启动容器
docker restart 容器id		# 重启容器
docker stop 容器id		# 停止当前正在运行的容器
docker kill 容器id		# 强制停止容器
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
-tf			# 显示所有日志，t时间戳，f持续输出
-tail number# 要显示的日志条数
$ sudo docker logs -ft --tail 10 2cb886ae2fa9	# 10为日志条数，由于加了参数f，会持续输出日志
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

