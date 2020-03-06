---
title: 浅谈德州仪器Type-C控制器CD3215/TPS65983
date: 2020-03-03 21:16:50
tags:
    - 德州仪器
    - IC
    - Type-C
    - USB
    - Technology
---

## 前言
<!-- more -->

。

我想稍微谈谈德州仪器出品的Tpye-C控制器CD3215家族，因为它和`Macbook`产品有很大的关系；

结论：

1. CD3215后缀不同，则不能互换使用，电压有区别。
2. CD3215就是Ti的TPS65983，CD3215应该是苹果自己定制的"名字"
3. NMB2016开始使用CD3215B01。NMB2017使用了CD3215C00。
4. 2018款`Macbook Pro`使用了CD3215C00。

## New Macbook

2015年发布的`Macbook(Retina)`是苹果重启`MacBook`产品线的第一代产品，经过多轮迭代后，它很不幸被`Macbook Air(Retina)`，目前已经完全下架。和`Macbook Air`、`Macbook Pro`不同，它放弃了风扇主动散热，同时首先引入了万恶的蝶式键盘。为了极致轻薄，全身只搭载了一个`USB-C`接口(这个`USB-C`口不具备雷电功能)。2015年的`New Macbook`也就成为了第一台吃USB-C螃蟹的笔记本，这个螃蟹还吃得很彻底。

在[ifixit-2015-Retina-Macbook-teardown][1]中，笔者没有发现任何有关`USB-C`控制器的有价值线索。

而在[ifixit-2016-Retina-Macbook-teardown][2]中，我们可以发现`USB-C`控制器的蛛丝马迹，2016款Macbook使用的`USB-C`控制器是`PS8741A`，虽然芯片制造商[paradetech][3]没有`PS8741A`的资料，但是依然可以找到它的上代产品[PS8740][4]和换代产品[PS8743][5]。感兴趣的可以点进去看看，总之`PS8741A`是一个标准的USB-C Host控制器，主机端使用它就可以实现`USB-C`的大部分功能(`PD`、`DP Alt`、`USB3`)。但是在`Macbook`上，这颗芯片似乎只是用来做`USB`信号中继的功能，因为连接`Macbook`主板和`USB-C接口`的排线太长了，有必要做`USB`信号中继。

为什么认为`PS8741A`只起`USB`信号中继作用呢？这是因为2016`Macbook`的`USB-C`排线的另一侧连接口上能发现德州仪器的`CD3215B01`USB主控芯片。这颗主控应该是连接到对应的Intel USB控制器和DP lanes上，所以他才是真正的USB主控。

在[ifixit-2017-Retina-Macbook-teardown][6]中，虽然USB排线部分被略过了，但是在看到[ifixit售卖的USB-C排线][7]通用于2016、2017`Macbook`，可以确认2017款`Macbook`也搭载了`PS8741`，同时2017款`Macbook`搭载了`CD3215C00`，和2016款`Macbook`有差别。

在Mid-2017款`Macbook`后，Macbook With Retina产品线没有再推出新品，它被搭载了Macbook Air取代了，我们先讨论到这里。

## Macbook Pro 2016-2019

https://zh.ifixit.com/Guide/MacBook+Pro+13-Inch+Touch+Bar+2017+%E6%8B%86%E8%A7%A3/92171

https://zh.ifixit.com/Guide/2018+%E6%AC%BE+13+%E8%8B%B1%E5%AF%B8%E5%B8%A6%E8%A7%A6%E6%8E%A7%E6%9D%A1%E7%9A%84+MacBook+Pro+%E6%8B%86%E8%A7%A3/111384

RMBP 2019 16.1: CD3217B12
https://zh.ifixit.com/Guide/MacBook+Pro+16-Inch+2019+%E6%8B%86%E8%A7%A3/128106

## iMac
https://zh.ifixit.com/Guide/iMac+Intel+%E9%85%8D%E6%9C%89+21.5-Inch+Retina+4K+%E6%98%BE%E7%A4%BA%E5%99%A8%EF%BC%882017%E7%89%88%EF%BC%89%E6%8B%86%E8%A7%A3/92170

## CD3215

CD3215是一款如此诡异的USB主控芯片，甚至你都无法确定它是否`USB-C`主控芯片。

在Google上它会出现很多意义不明的结果：

![CD3215-Google-Result][8]

德州仪器官网一般会有非常详尽的Datasheet以供参考，结果CD3215就像不存在一样：

![CD3215-Tii-No-Datasheet][9]

但是Ti官网上的[这段对话][10]很有趣，CD3215=TPS65983B?

![CD3215-TPS65983B-I][11]

而[这段对话][12]就更有趣了：

![CD3215-TPS65983B-II][13]

既然`CC3211`是苹果定制的`TPS22981`，那么`CD3215`自然也就是苹果定制的`TPS65983`

关于`CD3215`，国外的Mac维修大神[Rossmann][14]给出了以下结论：
1. `CD3215B03`和`CD3215C00`功能、针脚一致，但是耐压性完全不一样，`CD3215C00`能够承受20V电压，`CD3215B03`不行，所以不能简单替换。网上卖的基本都是`CD3215B03`，[Rossmann Shop][15]售卖拆板的`CD3215C00`。
2. 混用这两者会导致Macbook Pro无法充电，有兴趣的可以看看这个视频:[How to fix charging issues on new USB-C Macbooks and IMPORTANT CD3215 COMPATIBILITY INFORMATION!][16]
3. 网上流出的Macbook电路图上是`CD3215A`，这是错误的。
4. CD3215C00才能提供雷电主控所需的20V电压：[CD3215 Bootup Sequence][17]
5. 一个USB-C接口需要一个CD3215主控接管，包括供电、通信。在Mac上，只要有一个CD3215无法工作，那么所有CD3215掌管的所有USB-C接口都无法充电...
6. `CD3215B`很容易买到，而且都已经写好了固件。但是`CD3215C`就不一样，没有人单独售卖它们，只能从坏掉的Macbook主板上回收——这意味着不存在全新的`CD3215C`备件。

顺便一提，这位老哥的念头可谓天马行空：[此处输入链接的描述][18]


  [1]: https://zh.ifixit.com/Guide/2015%E6%AC%BE%E5%B8%A6%E6%9C%89Retina%E5%B1%8F%E5%B9%95%E7%9A%84MacBook%E6%8B%86%E8%A7%A3/39841
  [2]: https://zh.ifixit.com/Teardown/Retina+MacBook+2016+Teardown/62149#s130742
  [3]: https://www.paradetech.com/
  [4]: https://www.paradetech.com/products/ps8740/
  [5]: https://www.paradetech.com/products/ps8743/
  [6]: https://zh.ifixit.com/Guide/MacBook%EF%BC%88+%E5%B8%A6%E6%9C%89Retina%E6%98%BE%E7%A4%BA%E5%B1%8F%EF%BC%89+2017%E6%AC%BE%E6%8B%86%E8%A7%A3/92172
  [7]: https://zh.ifixit.com/Store/Mac/MacBook-12-Inch-Retina-Early-2016-2017-USB-C-Port-Assembly/IF301-022?o=1
  [8]: https://cdn.jsdelivr.net/gh/londbell/pic/img/CD3215-Google-Result.png
  [9]: https://cdn.jsdelivr.net/gh/londbell/pic/img/CD3215-Ti-No-Datasheet.png
  [10]: https://e2e.ti.com/support/interface/f/138/t/818497?keyMatch=CD3215&tisearch=Search-EN-everything
  [11]: https://cdn.jsdelivr.net/gh/londbell/pic/img/CD3215-TPS65983B-I.png
  [12]: https://irclog.whitequark.org/~h~openfpga/2018-09-01
  [13]: https://cdn.jsdelivr.net/gh/londbell/pic/img/CD3215-TPS65983B-II.png
  [14]: https://store.rossmanngroup.com/index.php/
  [15]: https://store.rossmanngroup.com/index.php/za2-cd3215c00.html
  [16]: https://vimeo.com/315961233
  [17]: http://logiwiki.shinycomputers.com/index.php?title=CD3215_Bootup_Sequence&mobileaction=toggle_view_desktop
  [18]: https://forum.hddguru.com/viewtopic.php?f=10&t=37524&start=20&mobile=desktop