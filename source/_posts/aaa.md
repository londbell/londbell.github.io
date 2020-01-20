---
layout: post
title: Windows-Hpyer-V-Fixaaa
comments: true
tags:
  - 笔记
date: 2017-03-30 10:46:58
---
在Windows 10操作系统中，微软为其搭载了自家的服务器虚拟化技术 Hyper-V，只需要在功能中启动即可创建并运行需要的的虚拟机实现操作系统测试或服务器虚拟化的工作。

　　但有的时候除了 Hyper-V 之外，还需要运行 VMware 的虚拟机，但如果在系统中同时安装着两个虚拟机平台， VMware、VM VirtualBox虚拟机都不能运行，提示与Hyper-V不兼容。

<!-- more -->
   所以可以这样解决：
```
   1、运行 bcdedit /copy {current} /d “Windows 10 (关闭 Hyper-V)” 命令，随后会提示已经创建了另外一个启动菜单项，记下 { } 中的一串代码。

　　2、运行 bcdedit /set {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX} hypervisorlaunchtype OFF 命令，将上面的代码替换掉这里的红色XXX代码即可

```
再次启动 Windows 10 即可手动选择是否要启用 Hyper-V，在“关闭 Hyper-V”的模式中，即可运行 Vmware 虚拟机，而另一个选项则可以运行 Hyper-V 虚拟机，这样就可以避免为了运行 VMware 虚拟机而卸载 Hyper-V 功能了。
