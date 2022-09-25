# vmware直接接入物理机所在网络


此篇旨在解决vmware虚拟机桥接ping通外网的方法

```shell
$ vim /etc/network/interfaces
```

![vmware_bridge1](https://tvax2.sinaimg.cn/large/007Z9xVHgy1h6ixq8qemtj30xi0cxtht.jpg)

**注意ip地址是自定义的，和物理机在同一网段的ip地址，网关是物理机的网关！**

```shell
$ vim /etc/resolv.conf
domain
nameserver 8.8.8.8
nameserver 114.114.114.114
nameserver [网关地址]
```

进入高级网络配置（不同系统不一样地方）

在ipv4设置中，方法选择手动，地址填写刚刚自定义的ip地址，以及子网掩码和网关

DNS服务器填一个8.8.8.8等，保存

命令行输入`systemctl restart networking`重启网络（**不同系统不一样**，实在不知道就重启虚拟机）


