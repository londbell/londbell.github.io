---
title: WSL安装Manjaro体验(烂尾工程)
date: 2020-02-22 20:48:05
tags:
    - wsl
    - arch
    - manjaro
---

## 安装Arch

使用[LxRunOffline](https://github.com/DDoSolitary/LxRunOffline/wiki)来安装Arch，需要准备好[Arch](https://lxrunoffline.apphb.com/download/ArchLinux)

在管理员权限运行下的Powershell中，使用以下的指令：

``` shell
.\LxRunOffline.exe i -n Manjaro -d .\Manjaro\ -f .\archlinux-bootstrap-2020.01.01-x86_64.tar.gz -s -r root.x86_64
```

上面只针对于我自己的配置，需要根据你自己想安装到的位置和名称来修改。

对于Arch，命令中的`-r root.x86_64`是必须的，`-s`说明会生成快捷方式。

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/微信截图_20200125201236.png)
如上图所示，说明完成了Manjaro的安装。

直接启动LxRunOffline生成的快捷方式，会进入这个WSL环境的Bash，默认以Root用户登录。说明已经安装成功：
![](https://cdn.jsdelivr.net/gh/londbell/pic/img/微信截图_20200125201414.png)

<!-- more -->

## 配置Arch

部署完毕后，需要先生成新的`resolv.conf`文件,否则无法联网：

``` shell
echo "# This file was automatically generated by WSL. To stop automatic generation of this file, remove this line." > /etc/resolv.conf
```
这时候`resolv.conf`文件应该长这样：

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/modify-resolv-but-not-reboot-wsl.png)

退出WSL的Bash，重新打开后(不需要任务管理器关闭相关进程)，可以看到`resolv.conf`文件已经变成了这样：

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/auto-gen-resolv.png)

## 配置Manjaro

因为目前Arch里并没有Vi/Nano等编辑器，我们可以直接进入Windows下面的WSL目录，在Windows中做一部分文本编辑。

先配置Arch的软件源:

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/微信截图_20200125202044.png)

默认的Arch源，我们都不需要：

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/arch-default-source.png)

由于我在中国，直接使用清华源即可：

``` 
## Country : China
Server = https://mirrors.tuna.tsinghua.edu.cn/manjaro/stable/$repo/$arch
Server = http://mirrors.tuna.tsinghua.edu.cn/manjaro/stable/$repo/$arch
```

### 配置pacman：
``` shell
pacman-key --init # 初始化key
```

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/pacman-key-init.png)

``` shell
pacman -Syy manjaro-keyring # 尝试安装manjaro-keyring软件包，是否导入key选择y，会安装失败，是否选择删除已下载文件时选n
```

实际上如果前面不对`resolv.conf`做修改，会提示连接超时错误：

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/tsinghua-https-error.png)

这里我们按正常连接成功流程走:

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/connect-ok-but-pgp-fail.png)

参考：[利用WSL打造Arch开发环境](https://zhuanlan.zhihu.com/p/51270874)的做法:尝试安装manjaro-keyring软件包，是否导入key选择y，会安装失败，是否选择删除已下载文件时选n

然后，使用-U指令：
``` shell
pacman -U 上一步提示的下载文件路径 # 强制安装，导入Manjaro的key
```

``` shell
pacman -U /var/cache/pacman/pkg/manjaro-keyring-20190608-1-any.pkg.tar.xz
```

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/install-local-manjaro-keyring.png)

接着：
```shell
pacman-key --init # 再次初始化key
pacman-key --populate archlinux manjaro # 下载Arch和Manjaro的key
pacman -Syyu # 强制更新软件源索引列表，并更新系统软件包
```

一路无脑y下去就完事了。
![](https://cdn.jsdelivr.net/gh/londbell/pic/img/arch-manjaro-key-download.png)

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/pacman-syyu-force-update.png)

这个时候，mirrorlist文件被自动修改成了一堆没用的mirror：

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/mirror-list-roll-back-by-Syuu.png)

我们修改回原来的清华源：

``` shell
## Country : China
Server = https://mirrors.tuna.tsinghua.edu.cn/manjaro/stable/$repo/$arch
Server = http://mirrors.tuna.tsinghua.edu.cn/manjaro/stable/$repo/$arch
```

参考：[利用WSL打造Arch开发环境](https://zhuanlan.zhihu.com/p/51270874)的packages.txt，将下面的软件包列表拷贝到一个packages.txt中，手动安装即可。

```
pacman -Sy --needed $(comm -12 <(pacman -Slq | sort) <(sort packages.txt))
```

### 创建用户

```
passwd # 初始化root密码
useradd -m -s /bin/bash 用户名 # 创建用户
passwd 用户名 # 初始化用户密码
```

### 修改新增用户的权限组

编辑/etc/sudoers文件，在root一行（大约79行）之下添加下列代码，设置 sudo 权限：
```
用户名 ALL=(ALL) ALL
```

![](https://cdn.jsdelivr.net/gh/londbell/pic/img/manjaro-config-result.png)


