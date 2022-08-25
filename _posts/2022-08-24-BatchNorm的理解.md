---
layout: mypost
title: 对Batch Normalization的理解
categories: [深度学习基础知识]
---
*本文来自https://www.cnblogs.com/guoyaohua/p/8724433.html*

Batch Normalization作为DL的重要成果，已经广泛被证明其有效性和重要性。虽然其理论还解释得不是很清楚，但是实践证明好用才是真的好。Batch Normalization来自论文"Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift"

机器学习领域有个重要的假设：IID独立同分布假设，就是假设训练数据和测试数据是满足相同分布的，这是通过训练数据获得的模型能够在测试集获得好的效果的一个基本保障。BatchNorm就是在深度神经网络中使得每一层神经网络的输入保持相同分布。
### 1. Internal Covariate Shfit问题
从论文上来看，BN是用来解决“Interval Covariate Shift”问题的。在深度神经网络中，由于每一层参数都是可学习的，因此神经网络的各个隐层的输入分布会在不停变化，这被称为“Interval Covariate Shift”。BatchNorm的基本目的就是为了让每个隐层神经网络的输入分布稳定下来。

BatchNorm方法的提出也是收了前人的启发：之前的研究表明如果在图像处理中对输入进行白化（Whiten）操作——所谓白化，就是对输入数据分布变换到0均值，单位方差的正态分布——那么神经网络会较快收敛。
### 2. BatchNorm的本质思想
BN的基本思想：随着神经网络深度加深，其分布逐渐发生偏移，之所以训练收敛较慢