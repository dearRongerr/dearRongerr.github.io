---
date: 2025-05-05T22:29:34+08:00
lastmod: 2025-05-05T22:29:34+08:00
draft: false
title: TimesNet 论文再读
math: true
toc: true
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
categories:
  - '📜文献阅读'
---

- 知乎专栏: [https://zhuanlan.zhihu.com/p/606575441](https://zhuanlan.zhihu.com/p/606575441)
- 期刊: ICLR2023
- 作者团队: 清华大学软件学院机器学习组官方公众号(THUML-LAB)
- 作者列表：`吴海旭*，胡腾戈*，刘雍*，周航，王建民，龙明盛`

- 开源代码: [https://github.com/thuml/TimesNet](https://github.com/thuml/TimesNet)
- 原文地址: [https://openreview.net/pdf?id=ju_Uqw384Oq](https://openreview.net/pdf?id=ju_Uqw384Oq)

- up 主讲解: [论文研读之时序分析通用基础模型：TimesNet 预测/插补/分类/异常检测](https://www.bilibili.com/video/BV1Ka4y1k7XY/?spm_id_from=333.337.search-card.all.click&vd_source=ddd7d236ab3e9b123c4086c415f4939e)
- 之前的[阅读笔记](https://dearrongerr.github.io/Rongerr.github.io/literature/TSP/3_TimesNet/)

> 没有使用 注意力机制,是 CONV 系的方法

这里只看方法

> 动机: 看完代码,看怎么写的

## 3 TIMESNET

As aforementioned, based on the multi-periodicity of time series, we propose the TimesNet with a  modular architecture to capture the temporal patterns derived from different periods. For each period,  to capture the corresponding **intraperiod- and interperiod-variations**, we design a TimesBlock within  the TimesNet, which can transform the 1D time series into 2D space and simultaneously model the  two types of variations by a parameter-efficient inception block.

如前所述，基于时间序列的多周期性，我们提出了TimesNet，它具有模块化架构，用于捕捉来自不同周期的时间模式。对于每个周期，为了捕捉相应的周期内和周期间的变异，我们在TimesNet内部设计了一个TimesBlock，它可以将一维时间序列转换为二维空间，并通过一个参数高效的起始块（inception block）同时对这两种类型的变异进行建模。

## 3.1 TRANSFORM 1D-VARIATIONS INTO 2D-VARIATIONS(1 维转 2 维)

As shown in Figure 1, each time point involves two types of temporal variations with its adjacent area  and with the same phase among different periods simultaneously, namely intraperiod- and interperiodvariations. 

However, this original 1D structure of time series can only present the variations among  adjacent time points. 

To tackle this limitation, we explore the two-dimension structure for temporal  variations, which can explicitly present variations within and between periods, thereby with more  advantages in representation capability and benefiting the subsequent representation learning.

如图1所示，每个时间点都涉及到与其相邻区域的两种时间变化，以及在不同周期中相同阶段的同时变化，即周期内变化和周期间变化。然而，这种原始的一维时间序列结构只能展示相邻时间点之间的变化。

为了解决这一限制，我们探索了时间变化的二维结构，它可以明确地展示周期内和周期间的变化，因此在表示能力和对后续表示学习的好处方面具有更多的优势。

Concretely, for the length-$T$ time series with $C$ recorded variates, the original 1D organization is $\mathbf{X}_{1D} \in \mathbb{R}^{T \times C}$. To represent the interperiod-variation, we need to discover periods first. Technically, we analyze the time series in the frequency domain by Fast Fourier Transform (FFT) as follows:

$$
\mathbf{A} = \text{Avg}\left(\text{Amp}\left(\text{FFT}(\mathbf{X}_{1D})\right)\right), \{f_1, \cdots, f_k\} = \arg \text{Topk}_{f_* \in \{1, \cdots, \lfloor \frac{T}{2} \rfloor\}} (\mathbf{A}),\\ p_i = \left\lceil \frac{T}{f_i} \right\rceil, i \in \{1, \cdots, k\}.
$$

具体来说，对于长度为 $T$ 的时间序列，其中包含 $C$ 个记录变量，原始的一维组织形式是 $\mathbf{X}_{1D} \in \mathbb{R}^{T \times C}$。为了表示周期间的变异，我们首先需要发现周期。技术上，我们通过快速傅里叶变换（FFT）在频域中分析时间序列，具体步骤如下：
$$
\mathbf{A} = \text{Avg}\left(\text{Amp}\left(\text{FFT}(\mathbf{X}_{1D})\right)\right), \{f_1, \cdots, f_k\} = \arg \text{Topk}_{f_* \in \{1, \cdots, \lfloor \frac{T}{2} \rfloor\}} (\mathbf{A}), \\ p_i = \left\lceil \frac{T}{f_i} \right\rceil, i \in \{1, \cdots, k\}.
$$


```python
def FFT_for_Period(x, k=2):
    # 输入x形状为[B, T, C]=[32, 96, 7]
    # B=32批次大小，T=96时间步长度，C=7特征维度
    
    # 对时间维度(dim=1)进行实数快速傅里叶变换
    xf = torch.fft.rfft(x, dim=1)
    # xf形状为[32, 49, 7]，对长度为96的序列，rfft返回96//2+1=49个频率点
    # 包含复数值，表示各频率的振幅和相位
    
    # 计算频率幅度谱，并在批次和特征维度上平均
    # mean(0)：沿批次维度取平均 -> [49, 7]
    # mean(-1)：沿特征维度取平均 -> [49]
    frequency_list = abs(xf).mean(0).mean(-1)
    
    # 将0频率分量(直流分量)置零，因为我们关注周期性波动而非常数偏移
    frequency_list[0] = 0
    
    # 找出k(默认2)个幅度最大的频率点索引
    _, top_list = torch.topk(frequency_list, k)
    # top_list形状为[k]，例如[4, 10]
    
    # 将索引转换为numpy数组以进行整数除法
    top_list = top_list.detach().cpu().numpy()
    
    # 计算周期长度：序列长度除以频率索引
    # 例如序列长度96，频率索引4，周期为96/4=24
    period = x.shape[1] // top_list
    # period形状为[k]，例如[24, 9]
    
    # 返回两个结果：
    # 1. period: 形状为[k]的数组，包含k个主要周期长度
    # 2. abs(xf).mean(-1)[:, top_list]: 形状为[32, k]的张量
    #    表示每个批次样本中k个主要周期的平均幅度，用于后续加权
    return period, abs(xf).mean(-1)[:, top_list]
```

[周期和频率索引的对应关系](http://localhost:1313/p/timesnet%E4%BB%A3%E7%A0%81%E5%A4%8D%E7%8E%B0-%E7%AC%AC%E4%B8%80%E7%AF%87/)

符号说明：

Here, $\text{FFT}(\cdot)$ and $\text{Amp}(\cdot)$ denote the FFT and the calculation of amplitude values. $\mathbf{A} \in \mathbb{R}^T$ represents the calculated amplitude of each frequency, which is averaged from $C$ dimensions by $\text{Avg}(\cdot)$. Note that the $j$-th value $\mathbf{A}_j$ represents the intensity of the frequency-$j$ periodic basis function, corresponding to the period length $\left\lfloor \frac{T}{j} \right\rfloor$. Considering the sparsity of frequency domain and to avoid the noises brought by meaningless high frequencies (Chatfield, 1981; Zhou et al., 2022), we only select the top-$k$ amplitude values and obtain the most significant frequencies $\{f_1, \cdots, f_k\}$ with the unnormalized amplitudes $\{\mathbf{A}_{f_1}, \cdots, \mathbf{A}_{f_k}\}$, where $k$ is the hyper-parameter. These selected frequencies also correspond to $k$ period lengths $\{p_1, \cdots, p_k\}$. 

Due to the conjugacy of frequency domain, we only consider the frequencies within $\{1, \cdots, \left\lfloor \frac{T}{2} \right\rfloor\}$. We summarize Equation 1 as follows:
$$
\mathbf{A}, \{f_1, \cdots, f_k\}, \{p_1, \cdots, p_k\} = \text{Period}(\mathbf{X}_{1D}).
$$

在此，$\text{FFT}(\cdot)$ 和 $\text{Amp}(\cdot)$ 分别表示快速傅里叶变换（FFT）和振幅值的计算。$\mathbf{A} \in \mathbb{R}^T$ 表示每个频率的计算振幅值，该值是通过 $\text{Avg}(\cdot)$ 从 $C$ 个维度平均得到的。请注意，第 $j$ 个值 $\mathbf{A}_j$ 表示频率为 $j$ 的周期基函数的强度，对应于周期长度 $\left\lfloor \frac{T}{j} \right\rfloor$。考虑到频域的稀疏性以及避免由无意义的高频带来的噪声（Chatfield, 1981; Zhou et al., 2022），我们仅选择前 $k$ 个振幅值，并获取具有未归一化振幅 $\{\mathbf{A}_{f_1}, \cdots, \mathbf{A}_{f_k}\}$ 的最显著频率 $\{f_1, \cdots, f_k\}$，其中 $k$ 是超参数。这些选定的频率还对应于 $k$ 个周期长度 $\{p_1, \cdots, p_k\}$。

由于频域的共轭性，我们只考虑 $\{1, \cdots, \left\lfloor \frac{T}{2} \right\rfloor\}$ 内的频率。我们将方程 1 总结如下：
$$
\mathbf{A}, \{f_1, \cdots, f_k\}, \{p_1, \cdots, p_k\} = \text{Period}(\mathbf{X}_{1D}).
$$


Based on the selected frequencies $\{f_1, \cdots, f_k\}$ and corresponding period lengths $\{p_1, \cdots, p_k\}$, we can reshape the 1D time series $\mathbf{X}_{1D} \in \mathbb{R}^{T \times C}$ into multiple 2D tensors by the following equations:

$$
\mathbf{X}_{2D}^i = \text{Reshape}_{p_i, f_i}(\text{Padding}(\mathbf{X}_{1D})), \, i \in \{1, \cdots, k\},
$$

where $\text{Padding}(\cdot)$ is to extend the time series by zeros along temporal dimension to make it compatible for $\text{Reshape}_{p_i, f_i}(\cdot)$, where $p_i$ and $f_i$ represent the number of rows and columns of the transformed 2D tensors respectively. Note that $\mathbf{X}_{2D}^i \in \mathbb{R}^{p_i \times f_i \times C}$ denotes the $i$-th reshaped time series based on frequency-$f_i$, whose columns and rows represent the intraperiod-variation and interperiod-variation under the corresponding period length $p_i$ respectively. Eventually, based on the selected frequencies and estimated periods, we obtain a set of 2D tensors $\{\mathbf{X}_{2D}^1, \cdots, \mathbf{X}_{2D}^k\}$, which indicates $k$ different temporal 2D-variations derived by different periods.

基于选定的频率 $\{f_1, \cdots, f_k\}$ 和相应的周期长度 $\{p_1, \cdots, p_k\}$，我们可以通过以下方程将一维时间序列 $\mathbf{X}_{1D} \in \mathbb{R}^{T \times C}$ 重塑为多个二维张量：

$$
\mathbf{X}_{2D}^i = \text{Reshape}_{p_i, f_i}(\text{Padding}(\mathbf{X}_{1D})), \, i \in \{1, \cdots, k\},
$$

其中 $\text{Padding}(\cdot)$ 是指沿时间维度通过零扩展时间序列，使其与 $\text{Reshape}_{p_i, f_i}(\cdot)$ 兼容，其中 $p_i$ 和 $f_i$ 分别表示转换后的二维张量的行数和列数。请注意，$\mathbf{X}_{2D}^i \in \mathbb{R}^{p_i \times f_i \times C}$ 表示基于频率 $f_i$ 的第 $i$ 个重塑时间序列，其列和行分别表示在相应周期长度 $p_i$ 下的周期内变异和周期间变异。最终，基于选定的频率和估计的周期，我们获得了一组二维张量 $\{\mathbf{X}_{2D}^1, \cdots, \mathbf{X}_{2D}^k\}$，这表明了由不同周期派生出的 $k$ 种不同的时间二维变异。

It is also notable that, this transformation brings two types of localities to the transformed 2D tensors, that is localities among adjacent time points (columns, intraperiod-variation) and adjacent periods (rows, interperiod-variation). Thus, the temporal 2D-variations can be easily processed by 2D kernels.

同样值得注意的是，这种转换为转换后的二维张量带来了两种类型的局部性，即相邻时间点之间的局部性（列，周期内变异）和相邻周期之间的局部性（行，周期间变异）。因此，时间二维变异可以很容易地通过二维核进行处理。

```python
def forward(self, x):
    # 获取输入张量的维度 [B, T, N] = [32, 96, 512]
    # B=32批次大小，T=96时间步，N=512特征维度
    B, T, N = x.size()
    
    # 使用FFT分析找出主要周期(例如[24, 12, 48, 8, 16])和对应的权重
    # period_list形状为[k]，例如k=5时为[24, 12, 48, 8, 16]
    # period_weight形状为[B, k]，例如[32, 5]
    period_list, period_weight = FFT_for_Period(x, self.k)

    # 初始化结果列表，用于存储不同周期处理后的输出
    res = []
    
    # 循环处理每个主要周期
    for i in range(self.k):
        # 当前循环处理的周期，例如第一次循环时period=24
        period = period_list[i]
        
        # 检查序列长度(96)是否是周期(24)的整数倍
        # 96 % 24 = 0，是整数倍，不需要填充
        if (self.seq_len + self.pred_len) % period != 0:
            # 计算需要填充到的长度，使其成为周期的整数倍
            # 例如：如果序列长度为100，周期为24，则length = ((100 // 24) + 1) * 24 = 120
            length = (((self.seq_len + self.pred_len) // period) + 1) * period
            
            # 创建填充张量并拼接到原始序列
            padding = torch.zeros([x.shape[0], (length - (self.seq_len + self.pred_len)), x.shape[2]]).to(x.device)
            out = torch.cat([x, padding], dim=1)
        else:
            # 当序列长度是周期的整数倍时，不需要填充
            # 对于96和周期24，走这个分支
            length = (self.seq_len + self.pred_len)  # length = 96
            out = x  # 原样保留
            
        # 将序列重塑为二维表示，以便使用2D卷积
        # 对于周期24，length=96:
        # 1. reshape: [32, 96, 512] → [32, 96/24=4, 24, 512]
        #    将序列分割为4个周期，每个周期包含24个时间步
        # 2. permute: [32, 4, 24, 512] → [32, 512, 4, 24]
        #    调整维度顺序，将特征维度移到第二位，符合卷积层输入格式
        out = out.reshape(B, length // period, period, N).permute(0, 3, 1, 2).contiguous()
        # 输出形状为[32, 512, 4, 24]，可以看作是4×24的"图像"，每个像素有512个通道
```



## 3.2 TIMESBLOCK

As shown in Figure 3, we organize the TimesBlock in a residual way (He et al., 2016). Concretely, for the length-$T$ 1D input time series $\mathbf{X}_{1D} \in \mathbb{R}^{T \times C}$, we project the raw inputs into the deep features $\mathbf{X}_{1D}^0 \in \mathbb{R}^{T \times d_{\text{model}}}$ by the embedding layer $\mathbf{X}_{1D}^0 = \text{Embed}(\mathbf{X}_{1D})$ at the very beginning. For the $l$-th layer of TimesNet, the input is $\mathbf{X}_{1D}^{l-1} \in \mathbb{R}^{T \times d_{\text{model}}}$ and the process can be formalized as:

$$
\mathbf{X}_{1D}^l = \text{TimesBlock} \left( \mathbf{X}_{1D}^{l-1} \right) + \mathbf{X}_{1D}^{l-1}.
$$

As shown in Figure 3, for the $l$-th TimesBlock, the whole process involves two successive parts: capturing temporal 2D-variations and adaptively aggregating representations from different periods.



如图3所示，我们以残差方式组织TimesBlock（He et al., 2016）。具体来说，对于长度为 $T$ 的一维输入时间序列 $\mathbf{X}_{1D} \in \mathbb{R}^{T \times C}$，我们在最开始通过嵌入层 $\mathbf{X}_{1D}^0 = \text{Embed}(\mathbf{X}_{1D})$ 将原始输入投影到深层特征 $\mathbf{X}_{1D}^0 \in \mathbb{R}^{T \times d_{\text{model}}}$。对于TimesNet的第 $l$ 层，输入是 $\mathbf{X}_{1D}^{l-1} \in \mathbb{R}^{T \times d_{\text{model}}}$，该过程可以形式化为：

$$
\mathbf{X}_{1D}^l = \text{TimesBlock} \left( \mathbf{X}_{1D}^{l-1} \right) + \mathbf{X}_{1D}^{l-1}.
$$

如图3所示，对于第 $l$ 个TimesBlock，整个过程包括两个连续的部分：捕获时间二维变异和自适应地聚合来自不同周期的表示。

- 两层的目的, 浅层捕获底层语义信息
- 深层捕捉 高级抽象语义信息

**Capturing temporal 2D-variations**

Capturing temporal 2D-variations Similar to Equation 1, we can estimate period lengths for deep features $\mathbf{X}_{1D}^{l-1}$ by $\text{Period}(\cdot)$. Based on estimated period lengths, we can transform the 1D time series into 2D space and obtain a set of 2D tensors, from which we can obtain informative representations by parameter-efficient inception block conveniently. The process is formalized as follows:

$$
\mathbf{A}^{l-1}, \{f_1, \cdots, f_k\}, \{p_1, \cdots, p_k\} = \text{Period}(\mathbf{X}_{1D}^{l-1}),
$$

$$
\mathbf{X}_{2D}^{l,i} = \text{Reshape}_{p_i, f_i}(\text{Padding}(\mathbf{X}_{1D}^{l-1})), \, i \in \{1, \cdots, k\}
$$

$$
\widehat{\mathbf{X}}_{2D}^{l,i} = \text{Inception} \left( \mathbf{X}_{2D}^{l,i} \right), \, i \in \{1, \cdots, k\},
$$

$$
\widehat{\mathbf{X}}_{1D}^{l,i} = \text{Trunc} \left( \text{Reshape}_1(p_i \times f_i) \left( \widehat{\mathbf{X}}_{2D}^{l,i} \right) \right), \, i \in \{1, \cdots, k\},
$$

where $\mathbf{X}_{2D}^{l,i} \in \mathbb{R}^{p_i \times f_i \times d_{\text{model}}}$ is the $i$-th transformed 2D tensor. After the transformation, we process the 2D tensor by a parameter-efficient inception block (Szegedy et al., 2015) as $\text{Inception}(\cdot)$, which involves multi-scale 2D kernels and is one of the most well-acknowledged vision backbones. Then we transform the learned 2D representations $\widehat{\mathbf{X}}_{2D}^{l,i}$ back to 1D space $\widehat{\mathbf{X}}_{1D}^{l,i} \in \mathbb{R}^{T \times d_{\text{model}}}$ for aggregation, where we employ $\text{Trunc}(\cdot)$ to truncate the padded series with length $(p_i \times f_i)$ into original length $T$.


捕获时间二维变异 与方程 1 类似，我们可以通过 $\text{Period}(\cdot)$ 估计深层特征 $\mathbf{X}_{1D}^{l-1}$ 的周期长度。基于估计的周期长度，我们可以将一维时间序列转换到二维空间并获得一组二维张量，从中我们可以通过参数高效的 inception 块方便地获得信息丰富的表示。该过程形式化如下：

$$
\mathbf{A}^{l-1}, \{f_1, \cdots, f_k\}, \{p_1, \cdots, p_k\} = \text{Period}(\mathbf{X}_{1D}^{l-1}),
$$

$$
\mathbf{X}_{2D}^{l,i} = \text{Reshape}_{p_i, f_i}(\text{Padding}(\mathbf{X}_{1D}^{l-1})), \, i \in \{1, \cdots, k\}
$$

$$
\widehat{\mathbf{X}}_{2D}^{l,i} = \text{Inception} \left( \mathbf{X}_{2D}^{l,i} \right), \, i \in \{1, \cdots, k\},
$$

$$
\widehat{\mathbf{X}}_{1D}^{l,i} = \text{Trunc} \left( \text{Reshape}_1(p_i \times f_i) \left( \widehat{\mathbf{X}}_{2D}^{l,i} \right) \right), \, i \in \{1, \cdots, k\},
$$


- 公式后面接 符号说明


其中 $\mathbf{X}_{2D}^{l,i} \in \mathbb{R}^{p_i \times f_i \times d_{\text{model}}}$ 是第 $i$ 个转换后的二维张量。转换之后，我们通过一个参数高效的 inception 块 (Szegedy et al., 2015) 处理二维张量，即 $\text{Inception}(\cdot)$，它涉及多尺度二维核，是最受认可的视觉骨干网络之一。然后我们将学习到的二维表示 $\widehat{\mathbf{X}}_{2D}^{l,i}$ 转换回一维空间 $\widehat{\mathbf{X}}_{1D}^{l,i} \in \mathbb{R}^{T \times d_{\text{model}}}$ 进行聚合，其中我们使用 $\text{Trunc}(\cdot)$ 截断长度为 $(p_i \times f_i)$ 的填充序列到原始长度 $T$。

```python
# 2D conv: from 1d Variation to 2d Variation
out = self.conv(out)
# 对重塑后的二维表示应用卷积操作
# 输入形状[32, 512, 4, 24]，输出形状保持不变
# self.conv包含两个带多尺寸卷积核的Inception Block，
# 可以有效捕获局部和全局的时空依赖关系

# reshape back
out = out.permute(0, 2, 3, 1).reshape(B, -1, N)
# 1. permute(0, 2, 3, 1): [32, 512, 4, 24] -> [32, 4, 24, 512]
#    调整维度顺序，为重塑回序列形式做准备
# 2. reshape(B, -1, N): [32, 4, 24, 512] -> [32, 4*24, 512] = [32, 96, 512]
#    将二维表示重塑回原始的一维序列格式

# 提取原始长度序列并添加到结果列表
res.append(out[:, :(self.seq_len + self.pred_len), :])
# 若有填充(即length > self.seq_len + self.pred_len)，只保留有效部分
# 对于例子中的周期24，序列长度96，这里截取[32, 96, 512]
# 将当前周期处理的结果添加到res列表，等待后续加权聚合
```

这里是 InceptionV1 的处理

init

```python
# parameter-efficient design
self.conv = nn.Sequential(
    Inception_Block_V1(configs.d_model, configs.d_ff,
                       num_kernels=configs.num_kernels),
    nn.GELU(),
    Inception_Block_V1(configs.d_ff, configs.d_model,
                       num_kernels=configs.num_kernels)
)
```

具体类的实现

```python
class Inception_Block_V1(nn.Module):
    def __init__(self, in_channels, out_channels, num_kernels=6, init_weight=True):
        super(Inception_Block_V1, self).__init__()
        self.in_channels = in_channels
        self.out_channels = out_channels
        self.num_kernels = num_kernels
        kernels = []
        for i in range(self.num_kernels):
            kernels.append(nn.Conv2d(in_channels, out_channels, kernel_size=2 * i + 1, padding=i))
        self.kernels = nn.ModuleList(kernels)
        if init_weight:
            self._initialize_weights()

    def _initialize_weights(self):
        for m in self.modules():
            if isinstance(m, nn.Conv2d):
                nn.init.kaiming_normal_(m.weight, mode='fan_out', nonlinearity='relu')
                if m.bias is not None:
                    nn.init.constant_(m.bias, 0)

    def forward(self, x):
        res_list = []
        for i in range(self.num_kernels):
            res_list.append(self.kernels[i](x))
        res = torch.stack(res_list, dim=-1).mean(-1)
        return res
```

具体的参数

```python
Sequential(
  (0): Inception_Block_V1(
    (kernels): ModuleList(
      (0): Conv2d(512, 2048, kernel_size=(1, 1), stride=(1, 1))
      (1): Conv2d(512, 2048, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
      (2): Conv2d(512, 2048, kernel_size=(5, 5), stride=(1, 1), padding=(2, 2))
      (3): Conv2d(512, 2048, kernel_size=(7, 7), stride=(1, 1), padding=(3, 3))
      (4): Conv2d(512, 2048, kernel_size=(9, 9), stride=(1, 1), padding=(4, 4))
      (5): Conv2d(512, 2048, kernel_size=(11, 11), stride=(1, 1), padding=(5, 5))
    )
  )
  (1): GELU(approximate='none')
  (2): Inception_Block_V1(
    (kernels): ModuleList(
      (0): Conv2d(2048, 512, kernel_size=(1, 1), stride=(1, 1))
      (1): Conv2d(2048, 512, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
      (2): Conv2d(2048, 512, kernel_size=(5, 5), stride=(1, 1), padding=(2, 2))
      (3): Conv2d(2048, 512, kernel_size=(7, 7), stride=(1, 1), padding=(3, 3))
      (4): Conv2d(2048, 512, kernel_size=(9, 9), stride=(1, 1), padding=(4, 4))
      (5): Conv2d(2048, 512, kernel_size=(11, 11), stride=(1, 1), padding=(5, 5))
    )
  )
)

```



Note that benefiting from the transformation of 1D time series, the 2D kernels in the inception block can aggregate the multi-scale intraperiod-variation (columns) and interperiod-variation (rows) simultaneously, covering both adjacent time points and adjacent periods. Besides, we adopt a shared inception block for different reshaped 2D tensors $\{\mathbf{X}_{2D}^{l,1}, \cdots, \mathbf{X}_{2D}^{l,k}\}$ to improve parameter efficiency, which can make the model size invariant to the selection of hyper-parameter $k$.

请注意，得益于一维时间序列的转换，inception 块中的二维核可以同时聚合多尺度的周期内变异（列）和周期间变异（行），涵盖相邻时间点和相邻周期。此外，我们为不同的重塑二维张量 $\{\mathbf{X}_{2D}^{l,1}, \cdots, \mathbf{X}_{2D}^{l,k}\}$ 采用共享的 inception 块来提高参数效率，这可以使模型大小与超参数 $k$ 的选择无关。

**共享参数的体现** 

```python
# 初始化部分 - 只创建一个卷积模块
def __init__(self, configs):
    # ...
    # parameter-efficient design
    self.conv = nn.Sequential(
        Inception_Block_V1(configs.d_model, configs.d_ff,
                           num_kernels=configs.num_kernels),
        nn.GELU(),
        Inception_Block_V1(configs.d_ff, configs.d_model,
                           num_kernels=configs.num_kernels)
    )

# 前向传播部分 - 同一个卷积模块处理所有周期
def forward(self, x):
    # ...
    for i in range(self.k):
        # 处理不同周期的数据...
        
        # 关键部分：对不同周期的二维表示使用同一个卷积模块
        out = self.conv(out)
        
        # ...
```

- 不管选择多少个主要周期(k值)，模型都只维护一套卷积参数，大大减少了参数量
- 不共享情况对比：如果不共享参数，代码应该是这样：

```python
# 非共享参数（假设代码）
self.convs = nn.ModuleList([
    nn.Sequential(...) for _ in range(self.k)
])

# 在forward中
out = self.convs[i](out)  # 每个周期使用不同的卷积模块
```

**Adaptive aggregation** Finally, we need to fuse $k$ different 1D-representations $\{\widehat{\mathbf{X}}_{1D}^{l,1}, \cdots, \widehat{\mathbf{X}}_{1D}^{l,k}\}$ for the next layer. Inspired by Auto-Correlation (Wu et al., 2021), the amplitudes $\mathbf{A}$ can reflect the relative importance of selected frequencies and periods, thereby corresponding to the importance of each transformed 2D tensor. Thus, we aggregate the 1D-representations based on the amplitudes:
$$
\widehat{\mathbf{A}}_{f_1}^{l-1}, \cdots, \widehat{\mathbf{A}}_{f_k}^{l-1} = \text{Softmax} \left( \mathbf{A}_{f_1}^{l-1}, \cdots, \mathbf{A}_{f_k}^{l-1} \right)
$$

$$
\mathbf{X}_{1D}^l = \sum_{i=1}^k \widehat{\mathbf{A}}_{f_i}^{l-1} \times \widehat{\mathbf{X}}_{1D}^{l,i}.
$$

Since the variations within and between periods are already involved in multiple highly-structured 2D tensors, TimesBlock can fully capture multi-scale temporal 2D-variations simultaneously. Thus, TimesNet can achieve a more effective representation learning than directly from 1D time series.

**自适应聚合** 最后，我们需要融合 $k$ 种不同的一维表示 $\{\widehat{\mathbf{X}}_{1D}^{l,1}, \cdots, \widehat{\mathbf{X}}_{1D}^{l,k}\}$ 以供下一层使用。受自相关（Wu et al., 2021）的启发，振幅 $\mathbf{A}$ 可以反映所选频率和周期的相对重要性，从而对应于每个转换后的二维张量的相对重要性。因此，我们基于振幅对一维表示进行聚合：
$$
\widehat{\mathbf{A}}_{f_1}^{l-1}, \cdots, \widehat{\mathbf{A}}_{f_k}^{l-1} = \text{Softmax} \left( \mathbf{A}_{f_1}^{l-1}, \cdots, \mathbf{A}_{f_k}^{l-1} \right)
$$

$$
\mathbf{X}_{1D}^l = \sum_{i=1}^k \widehat{\mathbf{A}}_{f_i}^{l-1} \times \widehat{\mathbf{X}}_{1D}^{l,i}.
$$

由于周期内和周期间的变异已经包含在多个高度结构化的二维张量中，TimesBlock 可以同时完全捕获多尺度的时间二维变异。因此，TimesNet 能够实现比直接从一维时间序列中更有效的表示学习。

```python
# 将各周期处理结果堆叠到新的维度
res = torch.stack(res, dim=-1)
# res列表中每个元素形状为[32, 96, 512]
# 堆叠后形状变为[32, 96, 512, k]，如k=5时为[32, 96, 512, 5]

# 对周期权重应用softmax使其和为1
period_weight = F.softmax(period_weight, dim=1)
# period_weight原始形状为[32, 5]
# softmax确保每个样本的5个周期权重总和为1

# 扩展权重维度以便与结果张量进行广播乘法
period_weight = period_weight.unsqueeze(1).unsqueeze(1).repeat(1, T, N, 1)
# unsqueeze两次: [32, 5] -> [32, 1, 1, 5]
# repeat复制: [32, 1, 1, 5] -> [32, 96, 512, 5]
# 确保每个时间步和特征维度都有相应的周期权重

# 自适应加权聚合不同周期的结果
res = torch.sum(res * period_weight, -1)
# 逐元素乘法: [32, 96, 512, 5] * [32, 96, 512, 5]
# 沿周期维度求和: 结果形状为[32, 96, 512]
# 实现了基于周期重要性的加权组合

# 添加残差连接
res = res + x
# 将原始输入直接加到转换后的特征上
# 帮助梯度传播并保留原始信息

# 返回最终结果
return res
# 形状为[32, 96, 512]的时间序列表示
```

- 识别到的不同的周期信息的融合 通过 `period_weight` 进行加权
- 单个周期通过 Inception 提取多尺度信息 , 融合多尺度信息用的是 mean 取平均

(相当于给出未来可能得方向)Generality in 2D vision backbones Benefiting from the transformation of 1D time series into  temporal 2D-variations, we can choose various computer vision backbones to replace the inception  block for representation learning, such as the widely-used ResNet (He et al., 2016) and ResNeXt  (Xie et al., 2017), advanced ConvNeXt (Liu et al., 2022b) and attention-based models (Liu et al.,  2021b). Thus, our temporal 2D-variation design also bridges the 1D time series to the booming 2D  vision backbones, making the time series analysis take advantage of the development of computer  vision community. In general, more powerful 2D backbones for representation learning will bring  better performance. Considering both performance and efficiency (Figure 4 right), we conduct the  main experiments based on the parameter-efficient inception block as shown in Equation 5.

二维视觉骨干网络的通用性 得益于将一维时间序列转换为时间二维变异，我们可以选择各种计算机视觉骨干网络来替代 inception 块进行表示学习，例如广泛使用的 ResNet（He et al., 2016）和 ResNeXt（Xie et al., 2017），先进的 ConvNeXt（Liu et al., 2022b）以及基于注意力的模型（Liu et al., 2021b）。因此，我们的时间二维变异设计也架起了一维时间序列与蓬勃发展的二维视觉骨干网络之间的桥梁，使时间序列分析能够利用计算机视觉社区的发展成果。一般来说，更强大的二维骨干网络用于表示学习将带来更好的性能。考虑到性能和效率（图4右），我们主要基于参数高效的 inception 块进行实验，如方程 5 所示。

(原文方法部分结束)

##  结论 CONCLUSION AND FUTURE WORK

本文提出了TimesNet，作为一种通用的基础模型，用于时间序列分析。受多周期性的启发，TimesNet能够通过模块化架构解析复杂的时间变化，并在二维空间中通过参数高效的inception块捕获周期内和周期间的变异。在实验中，TimesNet在五个主流分析任务中展现出了卓越的通用性和性能。未来，我们将进一步探索时间序列中的大规模预训练方法，这些方法利用TimesNet作为骨干网络，并能够普遍惠及广泛的下游任务。

本文提出了TimesNet，作为一种通用的基础模型，用于时间序列分析。受多周期性的启发，TimesNet能够通过模块化架构解析复杂的时间变化，并在二维空间中通过参数高效的inception块捕获周期内和周期间的变异。在实验中，TimesNet在五个主流分析任务中展现出了卓越的通用性和性能。未来，我们将进一步探索时间序列中的大规模预训练方法，这些方法利用TimesNet作为骨干网络，并能够普遍惠及广泛的下游任务。

## 摘要

Time series analysis is of immense importance in extensive applications, such as  weather forecasting, anomaly detection, and action recognition. 

This paper focuses  on temporal variation modeling, which is the common key problem of extensive  analysis tasks. 

Previous methods attempt to accomplish this directly from the 1D  time series, which is extremely challenging due to the intricate temporal patterns.  

Based on the observation of multi-periodicity in time series, we ravel out the complex temporal variations into the multiple intraperiod- and interperiod-variations.  

To tackle the limitations of 1D time series in representation capability, we extend  the analysis of temporal variations into the 2D space by transforming the 1D time  series into a set of 2D tensors based on multiple periods. 

This transformation can  embed the intraperiod- and interperiod-variations into the columns and rows of  the 2D tensors respectively, making the 2D-variations to be easily modeled by 2D  kernels. 

Technically, we propose the TimesNet with TimesBlock as a task-general  backbone for time series analysis. 

TimesBlock can discover the multi-periodicity  adaptively and extract the complex temporal variations from transformed 2D tensors by a parameter-efficient inception block. 

Our proposed TimesNet achieves  consistent state-of-the-art in five mainstream time series analysis tasks, including  short- and long-term forecasting, imputation, classification, and anomaly detection.  Code is available at this repository: https://github.com/thuml/TimesNet.

时间序列分析在广泛的应用中具有极大的重要性，例如天气预报、异常检测和动作识别。本文集中于时间变化建模，这是众多分析任务的共同关键问题。先前的方法尝试直接从一维时间序列中完成这一任务，由于复杂的时间模式，这极具挑战性。基于对时间序列多周期性的观察，我们将复杂的时间变化分解为多个周期内和周期间的变异。为了解决一维时间序列在表示能力上的局限性，我们通过将一维时间序列转换为基于多个周期的一组二维张量，将时间变化的分析扩展到二维空间。这种转换可以将周期内和周期间的变异分别嵌入到二维张量的列和行中，使二维变异能够被二维核轻松建模。技术上，我们提出了TimesNet，其中包含TimesBlock，作为时间序列分析的通用骨干网络。TimesBlock可以自适应地发现多周期性，并通过参数高效的inception块从转换后的二维张量中提取复杂的时间变化。我们提出的TimesNet在五个主流的时间序列分析任务中实现了一致的最新水平，包括短期和长期预测、插补、分类和异常检测。代码可在以下仓库获得：[TimesNet GitHub 仓库](https://github.com/thuml/TimesNet)。

## 贡献

1. Motivated by multi-periodicity and complex interactions within and between periods, we  find out a modular way for temporal variation modeling. By transforming the 1D time series  into 2D space, we can present the intraperiod- and interperiod-variations simultaneously.受多周期性和周期内及周期间的复杂交互所驱动，我们找到了一种模块化的时间变化建模方法。通过将一维时间序列转换到二维空间，我们可以同时呈现周期内和周期间的变异。
2. We propose the TimesNet with TimesBlock to discover multiple periods and capture temporal  2D-variations from the transformed 2D tensors by a parameter-efficient inception block.  我们提出了TimesNet及其中的TimesBlock，以发现多个周期，并利用参数高效的inception块从转换后的二维张量中捕获时间二维变异。
3. As a task-general foundation model, TimesNet achieves the consistent state-of-the-art in five  mainstream time series analysis tasks. Detailed and insightful visualizations are included.作为一个通用的基础模型，TimesNet在五个主流的时间序列分析任务中实现了一致的最新水平。文中包含了详细且富有洞察力的可视化结果。

## 补充一点讲解

- embedding
- 并行解码的处理  `self.predict_linear`

```python
# embedding
enc_out = self.enc_embedding(x_enc, x_mark_enc)  # [B,T,C]
enc_out = self.predict_linear(enc_out.permute(0, 2, 1)).permute(0, 2, 1)  # align temporal dimension
# self.predict_linear 的 定义
self.predict_linear = nn.Linear(self.seq_len, self.pred_len + self.seq_len)
```

over
