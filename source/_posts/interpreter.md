---
layout: post
title: 怎样写一个解释器(未完)
comments: true
tags:
  - 笔记
  - 转载
date: 2020-01-20 20:00:58
---
原文来自于王垠的[怎样写一个解释器](http://www.yinwang.org/blog-cn/2012/08/01/interpreter)
写一个解释器，通常是设计和实现程序语言的第一步。解释器是简单却又深奥的东西，以至于好多人都不会写，所以我决定写一篇这方面的入门读物。


<!-- more -->
   所以可以这样解决：
```
   1、运行 bcdedit /copy {current} /d “Windows 10 (关闭 Hyper-V)” 命令，随后会提示已经创建了另外一个启动菜单项，记下 { } 中的一串代码。

　　2、运行 bcdedit /set {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX} hypervisorlaunchtype OFF 命令，将上面的代码替换掉这里的红色XXX代码即可

```
再次启动 Windows 10 即可手动选择是否要启用 Hyper-V，在“关闭 Hyper-V”的模式中，即可运行 Vmware 虚拟机，而另一个选项则可以运行 Hyper-V 虚拟机，这样就可以避免为了运行 VMware 虚拟机而卸载 Hyper-V 功能了。
