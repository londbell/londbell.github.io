---
title: X1C Gen4 修改Bios教程
date: 2020-10-21 10:43:27
tags:
    - Skylake
    - X1Carbon
    - Thinkpad
    - bios
    - CH341a
    - Hackintosh
    - flashrom
---

基本上我是参考这个教程：[T460s Mod Bios][1]。但有区别，因为Thinkpad x60世代（也就是Skylake）这一代的机器Bios芯片似乎有多种型号，按照原教程，`flashrom`没法读出我的X1C，于是研究了下稍作改动能够读出了。
<!-- more -->

先接好CH341A和芯片夹，由于封装的原因，不想焊下来你就只能用芯片夹，针脚一定不能错，不然会烧bios！！！，请参考[编程器连接教程][2]。

```` shell
sudo flashrom -p ch341a_spi -r bios1.img

> flashrom v1.2 on Darwin 17.7.0 (x86_64)
flashrom is free software, get the source code at https://flashrom.org

Calibrating delay loop... OK.
libusb: info [darwin_claim_interface] no interface found; setting configuration: 1
Found Macronix flash chip "MX25L12805D" (16384 kB, SPI) on ch341a_spi.
Found Macronix flash chip "MX25L12835F/MX25L12845E/MX25L12865E" (16384 kB, SPI) on ch341a_spi.
Multiple flash chip definitions match the detected chip(s): "MX25L12805D", "MX25L12835F/MX25L12845E/MX25L12865E"
Please specify which chip definition to use with the -c <chipname> option
````

这里就遇到第一个问题了，`flashrom`成功读出了bios信息，但是他不确定是什么芯片，匹配命中了两个型号。但这两个型号都和我的`mx25l12873f`都不匹配。查了一圈资料发现都没有解答：

[CH341A - Can't find my BIOS chip in the database][3]

[MXIC MX25L12873F][4]

[RE: [GUIDE] The Beginners Guide to Using a CH341A SPI Programmer/Flasher (With Pictures!)][5]

[Flashing MX 25L12873F on MSI board via Raspberry Pi SP][6]

于是我就直接梭哈使用`MX25L12805D`，结果成功了。。。


```` shell
sudo flashrom -c MX25L12805D -p ch341a_spi -r bios1.img
````

```` shell
sudo flashrom -c MX25L12805D -p ch341a_spi -r bios2.img
````

```` shell
diff bios1.img bios2.img

> Binary files bios1.img and bios2.img differ
````

```` shell
md5 bios1.img

> MD5 (bios1.img) = 5a9ba7b4eeae3805986061610784cc80
````

```` shell
md5 bios2.img

> MD5 (bios2.img) = f30cdd6da2af5c1ce8c957dff0adba1a
````

于是我又再次读取了两次，分别命名为`bios3.img`，`bios4.img`。

```` shell
md5 bios3.img

> MD5 (bios3.img) = f30cdd6da2af5c1ce8c957dff0adba1a

md5 bios4.img

> MD5 (bios4.img) = f30cdd6da2af5c1ce8c957dff0adba1a
````

看起来2、3、4都是可用的，第一次可能是夹子没夹好，所以把`bios1.img`删除，2、3、4读出的img随便找一个继续修改都行。

在修改完后，我又想到前面我随便选定了`MX25L12805D`，那么刷回去会不会挂呢？为了防止翻车，我直接将读出到的`bios2.img`直接原样刷回去，看起来没什么问题：

```` shell
sudo flashrom -c MX25L12805D -p ch341a_spi -w bios2.img

> flashrom v1.2 on Darwin 17.7.0 (x86_64)
flashrom is free software, get the source code at https://flashrom.org

Calibrating delay loop... OK.
Found Macronix flash chip "MX25L12805D" (16384 kB, SPI) on ch341a_spi.
Reading old flash chip contents... done.
Erasing and writing flash chip...
Warning: Chip content is identical to the requested image.
Erase/write done.
````

出现`Chip content is identical to the requested image.`说明刷写没问题，于是开干，把修改后的bios img刷写进去。

```` shell
sudo flashrom -c MX25L12805D -p ch341a_spi -w bios_patched.img

> flashrom v1.2 on Darwin 17.7.0 (x86_64)
flashrom is free software, get the source code at https://flashrom.org

Calibrating delay loop... OK.
Found Macronix flash chip "MX25L12805D" (16384 kB, SPI) on ch341a_spi.
Reading old flash chip contents... done.
Erasing and writing flash chip... Erase/write done.
Verifying flash... VERIFIED.
````

时间会有点长，还有个验证过程，耐心等等。

刷写进去后，我再次读取bios内现有内容，和我改后的bios进行比对，没有问题，说明完全OK了。

```` shell
sudo flashrom -c MX25L12805D -p ch341a_spi -r read_out_from_after_patched.img

diff read_out_from_after_patched.img bios_patched.img

> 无输出，说明两者一致
````


[1]: https://github.com/simprecicchiani/ThinkPad-T460s-macOS-OpenCore/blob/master/Guides/Bios-Mod.md

[2]: https://tieba.baidu.com/p/6103207732?red_tag=1520803492

[3]: https://forums.mydigitallife.net/threads/ch341a-cant-find-my-bios-chip-in-the-database.78727/?__cf_chl_jschl_tk__=92b255438e930c0646f84f8aa90281bc49cf0678-1603205374-0-AZzKxwsp-VDRSwKEPrUDg9A8iNT3t-0WOlisDNNQPYMeWmnOIR4Sb5UycKwKXZWNnm727B8A43ih0KYnsNtbJuol5VAcwd8TN7NipjD_aVImRX9xRha_Jlb3CR_KW0g6jG3YIefKd89Xo9PLkXN4PvGnGRL5C9SydKZ4psKKvhvJXCaPGUiAw5dZts-GBKqv-7zDlB2S0ohWyiPAU9u30VU6M5EsF5Gxezj88_PqNQDBre6m-T15HcpPitSHPXCYGemzz2sNmyexkLdprdfVxi7inzSZlPOyvzsCzlu7pr7xPmtchuj8Hrx480oTTlGOlIVEujLs8VC674J64zq0tdIKhj1uPQmmph822ufb5mUG

[4]: https://www.win-raid.com/t4464f16-MXIC-MX-L-F.html

[5]: https://www.win-raid.com/t4287f16-GUIDE-The-Beginners-Guide-to-Using-a-CH-A-SPI-Programmer-Flasher-With-Pictures-1.html

[6]: https://flashrom.flashrom.narkive.com/XggdBabr/flashing-mx-25l12873f-on-msi-board-via-raspberry-pi-spi

