# SSDAN-another-way
Paper share：Sequence-to-Sequence Domain Adaptation Network for Robust Text Image Recognition

SSDAN主要解决了包含序列信息的图片，训练样本和测试样本style不一致的问题。针对英文和数字的字符级别识别，应用到中文汉字可能有些困难。

关于Domain Adaptation，可以参考知乎优秀回答：https://www.zhihu.com/question/41979241/answer/247421889

　　我们使用迁移学习，大多是进行模型迁移（将之前在源域中通过大量数据训练好的模型应用到目标域上进行预测）。本文的迁移学习，属于`特征迁移`（假设源域和目标域含有一些共同的交叉特征，通过特征变换，将源域和目标域的特征变换到相同空间，使得该空间中源域数据与目标域数据具有相同分布的数据分布）。

### Introduction

SSDAN可以在不同的场景下，解决训练集和测试集分布不一致产生的domain shift问题。

![](https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan1.jpg)

SSDAN的目标就是，利用unlabeled target text images，通过对齐源域数据与目标域数据的特征分布（feature distribution），来减缓domain shift。通过最小化measure of domain shift来训练，进行领域自适应。

然而，现在的measure of domain shift，如MMD、CORAL、adversarial loss都是针对固定的特征维度，不适合处理不定长的文字序列。而SSDAN可以自动专注最相关的字符区域，不用强行把源序列信息压缩进固定长度向量，同时，使用gated attention similarity (GAS) 在字符级别的特征空间（character-level feature space），对齐源域数据与目标域数据的分布。其中，GAS使用了一种无监督的字符级别的相似性损失。

### three contributions

1. 可以应用到ocr的不同领域，如自然场景、手写体、数学公式
2. 提出新的GAS单元，可以自适应地做fine-grained（细密纹理的）字符级别的知识迁移
3. 使用无监督的序列数据来减少domain shift

### Method

![](https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan2.jpg)

SSDAN is an attention-based sequence encoder-decoder network. 

#### Attentive Text Recognition

`CNN Encoder`：输入是来自源域或者目标域的图片x，输出是D维的特征向量，每一维度有L个元素，`L = H * W`,如图
<div align=center><img src="https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan3.jpg"/></div>

`Attention`：在CNN encoder和GRU decoder之间，由一个attention model连接。作用是，学习与解码字符最相关的文字图片区域。
attention是有T步的过程，在第k个time-step，将encoding feature map F(x)中最能代表字符![](http://latex.codecogs.com/gif.latex?y_k)的相关部分表示为内容向量![](http://latex.codecogs.com/gif.latex?c_k)：
<div align=center><img src="https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan4.jpg"/></div>

其中![](http://latex.codecogs.com/gif.latex?\alpha_k_,_i)是注意力权重：
<div align=center><img src="https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan5.jpg"/></div>

![](http://latex.codecogs.com/gif.latex?s_k_,_i)是注意力得分，表示在解码第k个字母时，注意力在第i个子区域的概率。
<div align=center><img src="https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan6.jpg"/></div>

`GRU Decoder`：使用GRU作为decoder预测字符。

关于GRU知识可以参考：https://towardsdatascience.com/understanding-gru-networks-2ef37df6c9be <\br>
中文译文：https://www.jiqizhixin.com/articles/2017-12-24

在第k个time step，当前隐状态可以表示为
<div align=center><img src="https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan7.jpg"/></div>

下一步计算当前预测字符![](http://latex.codecogs.com/gif.latex?y_k)的概率：
<div align=center><img src="https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan8.jpg"/></div>
其中g是softmax激活函数。通过上式求出的每个label的预测概率，就可以得到序列y的预测概率：
<div align=center><img src="https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan9.jpg"/></div>
其中<img src="https://github.com/cassie1728/SSDAN-another-way/raw/master/ssdan10.jpg"/></div>可以看做输入图片x的attended character-level features的序列。

#### Gated Attention Similarity Unit


