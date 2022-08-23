---
layout: mypost
title: PyTorch源码阅读之torch.autograd()
categories: [PyTorch]
---

# PyTorch源码阅读之torch.autograd()
## 前言
本篇笔记介绍PyTorch中的autograd模块功能为主,主要涉及torch/autograd下代码,不涉及底层C++实现,本文涉及源代码以PyTorch1.7为准.
- torch.autograd.function (函数的反向传播)
- torch.autograd.functional (计算图的反向传播)
- torch.autograd.gradcheck (数值梯度检查)
- torch.autograd.anomaly_mode (在自动求导时检测错误产生路径)
- torch.autpgrad.grad_mode (设置是否需要梯度)
- model.eval() 与 model.no_grad()
- torch.autograd.profiler (提供function级别的统计信息)

## `torch.autograd.function`(函数的反向传播)
我们在构建网络的时候,通常使用pytorch所提供的`nn.Module`(例如`nn.Conv2d`, `nn.ReLU`等)作为基本单元.而这些Module通常是包裹autograd function,以其作为真正实现的部分. 例如`nn.ReLU`实际使用`torch.nn.functional.relu`:
```python
from torch.nn import functional as F

class ReLU(Module):
    __constants__ = ['inplace']
    inplace: bool

    def __init__(self, inplace: bool = False):
        super(ReLU, self).__init__()
        self.inplace = inplace

    def forward(self, input: Tensor) -> Tensor:
        return F.relu(input, inplace=self.inplace)
```
这里`F.relu`类型为function,若再剥开一层,其实际包裹的函数类型为`builtin_function_or_method`, 这也是模型真正完成运算的部分,这些部分通常使用C++来实现. 
## `torch.autograd.functional` (计算图的反向传播)
在训练的过程中,我们通常用prediction和groundtruth label来计算loss(loss类型为Tensor),随后调用`loss.backward()`进行梯度反传.而Tensor类的`backward`方法实际调用的就是`torch.autograd.backward`这一接口.这一python接口实现了计算图的反向传播.
```python
class Tensor(torch._C._TensorBase)

    def backward(self, gradient=None, retain_graph=None, create_graph=False):
        relevant_args = (self,)
        ...
        torch.autograd.backward(self, gradient, retain_graph, create_graph)
        # gradient: 形状与tensor一致，可以理解为链式求导的中间结果，若tensor标量，可以省略（默认为1）
        # retain_graph: 多次反向传播时梯度累加。反向传播的中间缓存会被清空，为进行多次反向传播需指定retain_graph=True来保存这些缓存。
        # create_graph: 为反向传播的过程同样建立计算图，可用于计算二阶导
```
在PyTorch视线中,autograd会随着用户的操作,记录生成当前variable的所有操作,并建立一个有向无环图(DAG).图中记录了操作`Function`,每一个变量在图中的位置可通过其`grad_fn`属性在图中的位置推测得到。在反向传播过程中，autograd沿着这个图从当前变量溯源，可以利用链式求导法则计算所有叶子结点的梯度。
## `torch.autograd.gradcheck` (数值梯度检查)
在编写好自己的autograd function后，可以利用`gradckeck`和`gradgradcheck`接口，对数值算得的梯度和求导算得的梯度进行比较
