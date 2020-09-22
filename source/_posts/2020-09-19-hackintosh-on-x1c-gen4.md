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

[1]: https://dortania.github.io/OpenCore-Install-Guide/
