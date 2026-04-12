---
title: "Sampling Algorithms"
source: "https://pbr-book.org/4ed/Sampling_Algorithms"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## A Sampling Algorithms

Chapter [2](https://pbr-book.org/4ed/Monte_Carlo_Integration.html#chap:mc) provided an introduction to the principles of sampling and Monte Carlo integration that are most widely used in pbrt. However, a number of additional sampling techniques—the alias method, reservoir sampling, and rejection sampling—that are used only occasionally were not described there. This appendix introduces each of those techniques and then concludes with two sections that further apply the inversion method to derive sampling techniques for a variety of useful distributions.

> 第 2 章介绍了 pbrt 中最广泛使用的采样和蒙特卡洛积分原理。然而，一些偶尔使用的额外采样技术——别名方法、水库采样和拒绝采样——没有在那里描述。本附录介绍了这些技术，然后以两节进一步应用逆变换方法来推导各种有用分布的采样技术作为结束。