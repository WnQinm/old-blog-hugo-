# Clash for linux


第一次租云服务器，结果git clone时发现无法正常使用GitHub，无奈学习了在服务器部署clash

# 安装

首先在本地下载clash本体：https://github.com/Dreamacro/clash/releases
我选择的是clash-linux-amd64-v1.11.8.gz

**使用xftp软件连接服务器传输文件很方便，注意服务器防火墙开启对应端口**
xftp对于个人和学生有免费版，提交邮箱即可申请，会给邮箱发送下载地址

使用wsl或虚拟机在本地解压，获得一个可执行文件，我命名为cfl(clash for linux)
本地使用对应操作系统运行，会下载一些文件，如图：

![cfl-download](https://tvax3.sinaimg.cn/large/007Z9xVHgy1h68dfppx4pj30lw03fab8.jpg)

复制本地的配置文件到config.yaml中
`./cfl -d .`运行发现成功启动（最后有个点）

# 配置
clash提供了web接口方便进行管理，设置web接口的口令（登录密码）
```yaml
# Clash 的 RESTful API
external-controller: "0.0.0.0:9090"
# RESTful API口令
secret: "xxx"
```
服务器开放9090端口访问，TCP UDP都打开
登录网站http://clash.razord.top/#/proxies host为服务器公网ip，密钥是上面的api口令，即可进行快捷切换代理，查看日志，修改端口等操作

将clash放到后台运行
`nohup ./clash-linux-amd64 -d . &`

