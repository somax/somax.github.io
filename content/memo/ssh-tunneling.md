---
title: "用 SSH 建立安全隧道"
tags: ["Linux","运维"]
date: 2018-06-14T15:19:06+08:00
authors: ["somax"]
---

> 参考：https://forwardhq.com/help/ssh-tunneling-how-to

使用 SSH 能够安全地访问远程服务器，同时 SSH 也有一些鲜为人知的特点。其中一个很厉害功能就是隧道。

隧道允许你将远程服务器上的一个端口转发到本地服务器上的一个端口。这对于web开发者尤其有用，它允许你在本地Web服务器和互联网之间创建一个隧道，在互联网的任何地方都可以通过这个隧道访问您的本地应用程序或网站。

这篇文章介绍如何利用 SSH Tunneling 突破防火墙建立远程链接。



大致流程如下：

```
{ 私有服务器 } ---- 发起 ssh tunneling --->  { 公开服务器 }  <----- { 客户机 }
```



## 前置条件

1. 你需要有一个**可公开访问的服务器**，可以是任何一种服务器只要有安装有 SSH 服务。

2. 你还需要设置SSH服务器的配置文件，**添加 `GatewayPorts yes`**。

    > 配置文件通常是位于`/etc/ssh/sshd_config`，可能取决于您的系统的不同。修改后，一定要重新启动SSH服务，运行 `sudo service sshd restart`

3. 为了保持连接防止超时断开，还需要在配置中**添加 `ClientAliveInterval 60`**

    >  `ClientAliveInterval` 指定了服务器端向客户端请求消息的时间间隔, 默认是`0`，不发送。而`ClientAliveInterval 60`表示每 60 秒发送一次，然后客户端响应，这样就保持长连接了。
    >
    > 另外`ClientAliveCountMax` 使用默认值 `3` 即可。`ClientAliveCountMax` 表示服务器发出请求后客户端没有响应的次数达到一定值，就自动断开。

## 修改配置


1. 先查看当前配置
```sh
cd /etc/ssh
sudo grep "ClientAlive" sshd_config
#ClientAliveInterval 0
#ClientAliveCountMax 3
```

2. 备份配置
```sh
sudo cp sshd_config sshd_config.bak
```


3. 修改配置
```sh
sudo sed -i "s/#ClientAliveInterval 0/ClientAliveInterval 60/g" sshd_config
# ClientAliveCountMax 默认 3 可以不用修改
sudo sed -i "s/#ClientAliveCountMax 3/ClientAliveCountMax 5/g" sshd_config
```

4. 验证修改结果

```sh
sudo diff sshd_config sshd_config.bak
119,120c119,120
< ClientAliveInterval 60
< ClientAliveCountMax 3
---
> #ClientAliveInterval 0
> #ClientAliveCountMax 3
```

5. 重启服务

```sh
sudo systemctl restart sshd
```



## 创建隧道

万事俱备只欠东风，运行：

```sh
# ssh -NR <公开服务端口>:<私有服务器地址>:<私有服务端口> <公开服务地址>
ssh -NR 8080:localhost:80 you@example.com
```

分解说明：

- `-N` 不执行远程命令，用于端口转发
- `-R` 建立隧道

- `8080:localhost:80`

  1. 第一个数字是你希望远程服务器侦听的端口。可以是 1024-65535 之间的任何数字，你需要确保让你的防火墙通过这个端口。如果这个数字是`0` ，服务端会动态分配一个端口并返回给客户端。

  2. 中间的是私有服务器地址。大多数时候是 `localhost` ，但也可以是私有网络中的其他服务器，这样的话发起连接的这台服务只是个中转，类似这样：

     ```
       { 私有服务器1 } ---- 发起 ssh tunneling --->  { 公开服务器 }  <----- { 客户机 }
             |
             |-->{ 私有服务器2 }
     ```

  3. 最后，右面一个数字是你的本地服务器正在侦听的端口。

- `you@example.com` 最后一部分是公网的域名或者 ip 地址，如果远程 SSH 服务端口不是默认的 `22`，需要加上 `-p` 参数指定端口，如 ` -p 34567` ）



现在，有了一个开放的SSH隧道，你就可以从任何与互联网连接的地方查看你本地的网站或应用程序。

