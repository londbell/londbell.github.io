---
title: macbook-12-retina-usb-system
date: 2020-03-04 21:21:07
tags:
    - 德州仪器
    - IC
    - Type-C
    - USB
    - Technology
    - USB-C
    - Parade
    - Mac
    - New Macbook
---

USB Type-C接口(以下在不引起混淆的地方都简写为USB-C)改变了很多数码产品。或许很多人对它的感知仍然停留在手机可以正反插这种显眼特性上；我觉得人们轻视了`USB-C`对笔记本电脑的变革，`USB-PD`、`DP Alt Mode`和`Thunderbolt3 Alt Mode`等新鲜玩意为笔记本电脑带来了无限的可能性。

众所周知，`Macbook`产品线是推广USB-C接口最激进的产品，所以我希望花点篇幅记录一下我对`Macbook`系列笔记本的`USB`系统的研究。今天的研究对象就是`New Macbook`。

## New Macbook

2015年发布的`Macbook(Retina)`是苹果重启`MacBook`产品线的第一代产品，经过多轮迭代后，它很不幸地被`Macbook Air(Retina)`取代，目前已经完全下架，成为了时代的眼泪。和`Macbook Air`、`Macbook Pro`不同，它完全放弃了主动式的风扇散热，首先引入了万恶的蝶式键盘。为了极致轻薄，全身只搭载了一个`USB-C`接口(这个`USB-C`口不具备雷电功能)。2015年的`New Macbook`也就成为了第一台吃`USB-C`螃蟹的笔记本，这个螃蟹还吃得很彻底。

2015款`New Macbook`使用的是`Intel Broadwell-Y`平台；2016款和2017款`New Macbook`则分别采用了`Skylake-Y`和`Kabylake-Y`，一般认为`Skylake`和`Kabylake`差别非常小。而`Broadwell`和这两者的差异比较大，它更像是上一代的`Hotwell`的14nm版本。

2015款`New Macbook`发布时间相当早，离`USB-IF`协会发布`USB-C`的时间并不远，这就导致了`USB-C`相关的IC成熟度相对不高；在探索`USB-C`多种可能性的初期，2015款`New Macbook`的USB系统构成也就更加复杂，这是和2016/2017款最大的差别。

总之对于`New Macbook`，分为2015和2016/2017进行讨论是比较合理的。

### New Macbook 2015

在[ifixit-2015-Retina-Macbook-teardown][1]中，我没有发现`USB-C`控制器的有价值线索，ifixit的拆解讲解还不够详细。

庆幸的是，根据一份公开的芯片详解资料[Apple MacBook 2016][2]。可以发现2015款`New Macbook`的USB方案相当复杂，它包含了多组芯片：
![2015-Macbook-USB-System-I][3]

![2015-Macbook-USB-System-II][4]

虽然这两张图上的器件不够完整，但已经足够进行分析了。

`NXP`提供的`LPC11U37`是一颗ARM MCU。
`Ti`提供的`TS3DS10224`是一颗MUX，即多路选择器，`PERICOM`生产的`PI3DBS3224`([Datasheet][5])也是多路选择器，它主要负责USB2.0与`DisplayPort`信号中的`AUX`的多路复用。同样来自于`PERICOM`的`PI3USB9281`([Datasheet][6])负责充电协议和供电保护。`PI3USB102EZLE`负责USB2.0


  [1]: https://zh.ifixit.com/Guide/2015%E6%AC%BE%E5%B8%A6%E6%9C%89Retina%E5%B1%8F%E5%B9%95%E7%9A%84MacBook%E6%8B%86%E8%A7%A3/39841
  [2]: https://www.slideshare.net/jjwu6266/apple-macbook-2016
  [3]: https://cdn.jsdelivr.net/gh/londbell/pic/img/2015-Macbook-USB-System-I.png
  [4]: https://cdn.jsdelivr.net/gh/londbell/pic/img/2015-Macbook-USB-System-II.png
  [5]: https://www.diodes.com/assets/Datasheets/PI3DBS3224.pdf
  [6]: https://www.diodes.com/assets/Datasheets/PI3USB9281.pdf