---
layout: mypost
title: QuadTreeAttn&DKM论文阅读
categories: [论文阅读, 特征匹配]
---
> QUADTREE ATTENTION FOR VISION TRANSFORMERS

> Deep Kernelized Dense Geometric Matching

## QuadTree Attention
QuadTree Attention这篇文章自己很久以前就看到过，当时感觉这篇文章很好，但是不适合自己，一是当时感觉这篇论文介绍的方法贼复杂，以自己当时那么菜idea给自己代码也写不出来，另一个就是显卡不够，自己最多跑一下推理的代码。但是当时自己应该仔细看一下论文以及背后的思想，而不是看了个大概就过了。

QuadTree Attention这篇论文也是新意十足，在我看来这篇论文跟其他很多基于Transformer改进的论文一样都是来解决Transformer全局搜索带来的计算复杂度过高和只考虑全局信息（对局部信息处理并不是很好）的问题。这篇论文看名字就可以知道是采用四叉树方法的，在论文中，作者写道：我们观察到图片中大部分的区域彼此之间都是不相关的，因此作者使用了一个金字塔类型的结构可以快速跳过不相关的区域，每次将待搜索的区域分为4块，只取其中的topk（2）块来计算attention操作，最后在模型效果和计算复杂度上都有了提升。

说点个人观点，我其实是讨厌这种结构的，感觉不是很优雅（也可能是树结构对我来说太难了 **| :+)**

## DKM
DKM这篇论文是我看到有人和这个方法进行比较，而且看到这个方法效果还不错，来读一下这篇论文（太劝退了，这篇论文好多数学公式）。
