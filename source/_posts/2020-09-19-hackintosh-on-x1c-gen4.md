---
title: X1C Gen4黑苹果(未完)
date: 2020-09-19 22:05:09
tags:
    - OpenCore
    - Hackintosh
    - X1Carbon
    - Thinkpad
---

建议参考这个教程来进行，链接：[Dortania's OpenCore Install Guide][1]，在我撰写的时间点，我使用的`OpenCore`版本为`0.6.1`；

<!-- more -->

根据前面的步骤做完后，现在的`OpenCore`配置足以用来安装黑苹果，但是USB接口和内置键盘不能使用，需要继续以下的操作。

### USB部分修复

使用`USBInjectAll.kext`;

### 内置触摸板/键盘修复

使用`VoodooPS2Controller.kext`;

### 挂载EFI分区

刚安装完的时候EFI分区通过`ESP Mounter Pro`和[corpnewt][2]开发的`MountEFI`都可以使用；在时间机器还原老mac的备份后(不确定是不是它的原因)，这两个工具都无法挂载，通过`diskutil`指令也不行:

```` shell
# sudo diskutil mount disk0s1

Volume on disk0s1 failed to mount
Perhaps the operation is not supported (kDAReturnUnsupported)
If you think the volume is supported but damaged, try the "readOnly" option
````

使用`readOnly`选项后一样GG。

但是指定分区格式和挂载到某个文件夹后，却可以挂载成功了：

```` shell
# sudo mount -t msdos /dev/disk0s1 /Volumes/efi
````

这就很奇怪了，后来我却发现EFI分区的`TYPE NAME`并不是`EFI`，而是：

```` shell
3ED58CD7-F81F-11D2-BA4B-00A0C93EC93B
````

而根据[GUID Wiki][3]，EFI分区的默认`TYPE NAME`应该是：

```` shell
C12A7328-F81F-11D2-BA4B-00A0C93EC93B
````

在windows下按照[Tenforums][4]的教程更新了一下分区的`TYPE NAME`，回到mac下就正常挂载了。也不知道为什么`TYPE NAME`会变化。

```` shell
# select part 1
# set id=c12a7328-f81f-11d2-ba4b-00a0c93ec93b override
````

[1]: https://dortania.github.io/OpenCore-Install-Guide/
[2]: https://github.com/corpnewt/MountEFI
[3]: https://zh.wikipedia.org/wiki/GUID%E7%A3%81%E7%A2%9F%E5%88%86%E5%89%B2%E8%A1%A8
[4]: https://www.tenforums.com/drivers-hardware/80762-how-sign-existing-partition-efi-partition-diskpart.html?__cf_chl_jschl_tk__=9789e766affeba1f6f2d26227d92bfb465648816-1607320904-0-ASCn0XpUNHgixD-isIN0WE31Q2oCVm4EMRM2cq2mQDfafZaT9kU42Vbwvq76uU60pBMfqhba11qRxaLIzYzS50F0hzwqVwG-fgnD8oFZmLhKMYx2VZmVfsW3QBqzoBNdC_6nMGxnzenCCI6kD6kx1pd7SwPmJPNxqHg46onN3HBvonWrri3j06zz8fE7HhuQ2WxR3hjMIXDu4SKaINMpCoc2jP_4BcpYEiQM1s0ptnikLPKDzZ9mnrzum8DjWrmeUemznBb36dAbMvxX2nRfdINqgWggmT0pcpA34QzX0ygouWSgMckhaRgO0s25ZMyWRHAS16TKd28q4oWTJa2WL7_xfTN0DQDYKQA9WaJd5itNUOuDs9UOzHRx6fA91115JUkgCom_R3dNL0TpSCeHvVmU-BJluoBbavZoL3td5pzd