---
layout: mypost
title: MatchFormer&ASpanFormer论文阅读
categories: [论文阅读, 特征匹配]
---
> MatchFormer: Interleaving Attention in Transformers for Feature Matching

> ASpanFormer: Detector-Free Image Matching with Adaptive Span Transformer

## MatchFormer
MatchFormer这篇文章我很早之前就看到过，但是没看懂，现在读ASpanFormer的时候发现引用MatchFormer这篇文章了，因此又看了一遍MatchFormer，这次自己看懂了。从这篇论文中给出的实验效果来看相比LoFTR效果更好，计算复杂度更低。这篇论文作者提出了自己的一个比较新颖的观点，作者认为LoFTR方法从网络结构来看是一个Extract-to-match的结构，用CNN来提取每个点的特征，然后使用Transformer来得到用来得到进行match的特征，作者认为使用CNN进行extract导致得到的特征不够好，导致Transformer层需要承担的任务太大（overburden）。因此作者提出了一个Extract-and-match的结构，实际操作就是将LoFTR中的CNN也换成了一个Transformer backbone，并且这个Transformer backbone作者也加入了很多技巧，比如self-attention和cross-attention的排放顺序上，感兴趣可以读原文，我感觉这篇论文还是值得一读的。
### MatchFormer个人评价
论文作者提出了一个很好的网络结构，实验数据也很好看，但是我个人感觉解释网络结构为何这样设计的时候有些牵强，并且如果不仔细读论文的话就会认为只是在LoFTR的基础上将CNN部分换成Transformer，创新点不够。

## ASpanFormer
这篇文章也是Hongkai Chen(HKUST)的一作，感觉这篇文章又是比较难懂的（可能是今天我不在状态），这篇文章出发点与QuadTree Attention很相似，都是为了解决detector-free方法中直接在图像上使用Transformer导致计算复杂度过高并且无法获取局部信息的问题。因此作者提出了一种hierarchical attention的方法，采用了一种名为Global-Local attention的方法，最后实现了SOTA（提示了1~2%左右）。
正如这篇论文的名字SpanFormer，在计算local attention时，没有采用固定的大小，而是根据点的uncertainty的大小，点来确定search region或者说attentional span（计算attention的区域），在这篇文章中，作者的做法有点像光流法，并且最后也与一些使用深度学习的光流法做了对比。但是感觉文章在如何估计另一张图片上不确定区域大小上解释有些模糊，应该要结合代码来看才能看懂。
