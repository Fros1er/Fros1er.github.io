---
title: 自动开灯(?)
date: 2022-07-09 18:21:42
tags: embedded
---

总之打完第二年RM之后（也许应该是学完计算机组成原理）突然发现，我好像会那么点嵌入式了。于是想做点好玩的，于是就经典老番自动开灯了。

## 设计+选型

主控用esp8266，便宜。9g舵机和继电器随便买的。本来想用三极管或者MOS管开关，问题是我没学过模拟电路...

关键问题在于供电...从插排拉条线到门口开关太不优雅了。好在舵机支持3.3-6V，NodeMCU的话，去查了下VIN引脚接的稳压芯片，好像最高可以到20V。于是整了电池+电池盒，打算用1.5V电池并联四节，顺便整了个数码管电压表看电池电压。

最后一套下来不算线25块。线（还有焊台万用表热缩管）直接白嫖的\(

## 制作过程

接线没啥好说的，除了两根一分四的电源线。感觉是个人看了都想杀我。

接完线不带主控板进行了一个smoke test（字面义）。省流：没冒烟也没起火。

代码打算用arduino写，但是我第一次碰见ubuntu比win的环境还难配的东西。。。目前摸了，还没配好。

---

*未完待续*