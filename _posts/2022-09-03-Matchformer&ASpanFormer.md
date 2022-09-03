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

## SpanFormer
这篇文章也是Hongkai Chen(HKUST)的一作，