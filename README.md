# SSDAN-another-way
Paper share：Sequence-to-Sequence Domain Adaptation Network for Robust Text Image Recognition

SSDAN主要解决了包含序列信息的图片，训练样本和测试样本style不一致的问题。针对英文和数字的字符级别识别，应用到中文汉字可能有些困难。

关于Domain Adaptation，可以参考知乎优秀回答：https://www.zhihu.com/question/41979241/answer/247421889

　　我们使用迁移学习，大多是进行模型迁移（将之前在源域中通过大量数据训练好的模型应用到目标域上进行预测）。本文的迁移学习，属于`特征迁移`（假设源域和目标域含有一些共同的交叉特征，通过特征变换，将源域和目标域的特征变换到相同空间，使得该空间中源域数据与目标域数据具有相同分布的数据分布）。

### Introduction

SSDAN可以在不同的场景下，解决训练集和测试集分布不一致的问题。

![](https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan1.jpg)
