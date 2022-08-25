---
layout: mypost
title: PyTorch源码阅读之torch.autograd()
categories: [PyTorch]
---
## 0 设计
nn.Module是PyTorch体系下所有神经网络模块的基类，此处顺带梳理了一下torch.nn中的各个组件，他们的关系概览如下图所示：
![nn.Module_small](nn.Module结构v1.jpg)
展开模块后，模块之间的继承关系与层次关系结构如下图所示：
![nn.Module_big](nn.Module结构v2.jpg)
从各模块的继承关系来看，模块的组织和实现有几个常见的特点，供PyTorch代码库的开发者参考借鉴：
- 一般有一个基类来定义接口，通过继承来处理不同维度的input，如：
  1. Conv1d, Conv2d, Conv3d, ConvTransposeNd继承自_ConvNd
  2. MaxPool1d, MaxPool2d, MaxPool3d继承自_MaxPoolNd
- 每一个类都有一个对应的nn.functional函数，类定义了所需要的arguments和模块的parameters，在forward函数中将arguments和parameters传给nn.functional的对应函数来实现forward功能。比如：
  1. 所有的非线性激活函数，都是在forward中直接调用对应的nn.functional函数
  2. Normalization层都是调用的如F.layer_norm, F.group_norm等函数
- 继承nn.Module的模块主要重载init, forward, 和extra_repr函数，含有parameters的模块还会实现reset_parameters函数来初始化参数。

## 1. nn.Module实现
### 1.1 常用接口
#### 1.1.1__init__函数
在nn.Module的`__init__`函数中，会首先调用`torch._C_.log_api_usage_once("python.nn_Module")`,这一行代码是PyTorch 1.7的新功能，用于检测并记录API的调用，详见解释文档。

在此之后，nn.Module初始化了一系列重要的成员变量。这些变量初始化了在forward、backward和权重加载等时候会被调用的hooks，也定义了parameters和buffers，如下面的代码所示：
```python 
self.training = True  # 控制 training/testing 状态
self._parameters = OrderedDict()  # 在训练过程中会随着 BP 而更新的参数
self._buffers = OrderedDict()  # 在训练过程中不会随着 BP 而更新的参数
self._non_persistent_buffers_set = set()
self._backward_hooks = OrderedDict()  # Backward 完成后会被调用的 hook
self._forward_hooks = OrderedDict()  # Forward 完成后会被调用的 hook
self._forward_pre_hooks = OrderedDict()  # Forward 前会被调用的 hook
self._state_dict_hooks = OrderedDict()  # 得到 state_dict 以后会被调用的 hook
self._load_state_dict_pre_hooks = OrderedDict()  # load state_dict 前会被调用的 hook
self._modules = OrderedDict()  # 子神经网络模块
```
各个成员变量的功能在后面还会继续提到，这里先在注释中简单解释。由源码的实现可见，**继承nn.Moudle的神经网络模块在实现自己的__init__函数时，一定要先调用`super(),__init__()`。**
#### 1.1.2状态的转换
- 训练与测试
nn.Module通过self.training来区分训练和测试两种状态，使得模块可以在训练和测试时有不同的forward行为（如Batch Normalization）。nn.Module通过self.train()和self.eval()来修改训练和测试状态，其中self.eval直接调用了self.train(False)，而self.train()会修改self.training并通过self.children()来调整所有子模块的状态。
``` python
def train(self: T, mode: bool = True) -> T:
    self.training = mode
    for module in self.children():
        module.train(mode)
    return self
```




*本文转载自https://zhuanlan.zhihu.com/p/340453841*