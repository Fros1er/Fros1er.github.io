---
title: Yet another 寄网八股文
date: 2023-03-06 20:16:53
tags: networking
---

寄！

# 三次握手/四次挥手

client syn -> svr syn+ack -> client ack。

三次的原因：排除syn发出之后cli死掉的常见情况。 \
也有说法是：收syn时svr确认自己可以收cli可以发，收syn+ack时cli确认自己和svr可以收发，收ack时svr确认cli可以收自己可以发。

client fin -> svr ack | svr fin -> client ack. cli收到fin后等待2MSL，确保自己的ack没丢。（如果丢了svr会重发fin，cli得再给出回复。2MSL是自己ack+svr第二个fin的最长时间）。

