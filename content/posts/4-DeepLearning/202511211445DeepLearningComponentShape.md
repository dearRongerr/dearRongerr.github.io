---
slug: 0303e758a3
draft: false
lightgallery: false
password:
message:
repost:
  enable: false
  url:
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg # 可能报错
license:
comment: true
weight: 0
hiddenFromHomePage: false
hiddenFromSearch: false
hiddenFromRelated: false
hiddenFromFeed: false
resources:
  - name: featured-image
    src: featured-image.jpg
  - name: featured-image-preview
    src: featured-image-preview.jpg
toc: true
math: true
title: 深度学习组件的使用
subtitle:
date: 2025-11-21T17:19:31+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github BugFix
tags:
  - '伪代码'
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点'
categories:
  - '🧚🏻‍♀️深度学习'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

> 记得问自己：
>
> 1. 了解每个层为什么会导致形状变化，背后的原理是什么



参数量对比：**输入** (32,96,7) ；**输出**：(32,96,512)


| 组件          | 参数量 | 理论定义 | 实际应用\|for TS |
| -------------- | ------ | -------------- | -------------- |
| GRU |800256| 处理最后一维 | 处理特征维度 |
|Linear         | 4096   | 处理最后一维 | 处理特征维度 |
| CrossAttention | 12800  | 不变形状，返回加权的 query | 给时间步维度加权 |
| Conv1d         | 10752 | 处理中间维度 | 处理时间步维度、特征维度 |


----

**思考: 对比 GRU & CrossAttention，为什么都是三个线性层，而 GRU 的参数量会那么多。** 

GRU 参数量，细说是六个线性层，GRU 准确的来说是三个门，每个门是两个线性层，从输入>>隐藏层，隐藏层>>隐藏层

> $ 3 \times \text{output\_dim} \times (\text{input\_dim} + \text{output\_dim} + 2)$   

CrossAttention 是实打实的三个线性层，每个线性层都是单独对 query、key 或者 value 操作

> $(\text{input\_query\_dim} + \text{input\_key\_dim} + \text{input\_value\_dim} + 3) \times \text{output\_dim}$  
>
> 注意力机制的形状定义会更丰富
>
> <u>key&value同源；嵌入维度对齐</u>
>
> > ```bash
> > CrossAttention(
> >   (query_proj): Linear(in_features=512, out_features=512, bias=True)
> >   (key_proj): Linear(in_features=512, out_features=512, bias=True)
> >   (value_proj): Linear(in_features=512, out_features=512, bias=True)
> > )
> > Input query shape: torch.Size([32, 96, 512])
> > Input value shape: torch.Size([32, 48, 512])
> > Input key shape: torch.Size([32, 48, 512])
> > Output weighted_query shape: torch.Size([32, 96, 512])
> > Attention matrix shape: torch.Size([32, 8, 96, 48])
> > Total parameters: 787968
> > ```
>
> 更灵活的形状变换：key&value 不同源；嵌入维度不对齐(属于更底层的理解，一般用不到)
>
> > ```bash
> > CrossAttention(
> >   (query_proj): Linear(in_features=7, out_features=512, bias=True)
> >   (key_proj): Linear(in_features=7, out_features=512, bias=True)
> >   (value_proj): Linear(in_features=8, out_features=512, bias=True)
> > )
> > Input query shape: torch.Size([32, 96, 7])
> > Input value shape: torch.Size([32, 48, 8])
> > Input key shape: torch.Size([32, 48, 7])
> > Output weighted_query shape: torch.Size([32, 96, 512])
> > Attention matrix shape: torch.Size([32, 8, 96, 48])
> > 
> > Total parameters: 12800
> > ```
>
> 

Linear就没啥好说的了

> $\text{output\_dim} \times (\text{input\_dim} + 1)$ 

卷积参数量

> $\text{input\_dim}  \times \text{output\_dim} \times \text{kernel\_size}$

---

## 不变形状的小部件

- 设计原则："先让梯度飞（残差），再让幅度稳（Norm），最后让神经元睡觉（Dropout）"

  > Residual → Norm → Dropout，设计/实现都按这个顺序写，训练最稳。

- 有哪些？

  - 激活层：nn.ReLU()
  - 丢弃层：nn.Dropout()
  - 残差连接
  - 标准化层：Norm
  - 归一化层：RevIN
  - 

## 封装组件

- nn.Sequential()
- FFN=nn.Sequence(Linear,ReLU,Dropout,Linear)
- 1×1 卷积 $\iff$ Linear
- 如果想要设计一个bottleNeck，特别是对于 Linear 和 Conv 来说，需要明确指定输入维度和输出维度时，隐藏层维度的设计可以非常灵活，写成固定的值，或者输入维度的倍数，这样就不用重复定义层，做到参数共享

## 形状变化小部件

重点理解使用场景

- view：
  - 内存连续时使用
  - 如果前面用了transpose/permute，要加contiguous()；transpose(1,2).contiguous().view(B,-1)
  - 如果确定前面没有用transpose/permute，大胆的 view
- transpose：两轴互换
- permute：各种轴互换
- reshape：拿不准就 reshape，不出错，而且可以拆维度&合并维度；就是会偷偷copy，导致涨内存
- squeeze（挤，减），unsqueeze（吹，加） 一个维度=1 的轴；啥时候用，想用的操作算子要求两维或者三维，但是手里的数据维度不够。



## GRU单元

- 输入形状：(batch_size, seq_len, input_dim)

- 输出形状：(batch_size, seq_len, hidden_dim)
- 参数量：$\text{总参数量} = 3 \times \text{hidden\_dim} \times (\text{input\_dim} + \text{hidden\_dim} + 2)$ 

- 使用场景：聚合到当前时间点的信息，序列建模

① 从数学公式的角度来说，同样是嵌入到高维，GRU 单元所需要的参数量 $>>$ 线性层

② 从最通俗的角度来说，GRU 和 Linear 都是对输入数据的最后一维从低维空间映射到高维空间

③ 从结构的角度出发，原因是因为 GRU 内部是三个门，每个门都是线性层（输入到隐藏层的权重、隐藏层到隐藏层的权重、偏置），因此参数量是线性层的 3 倍。



```python
import torch
import torch.nn as nn

class Model(nn.Module):
    def __init__(self, input_dim, hidden_dim):
        super(Model, self).__init__()
        # 初始化 GRU 单元
        self.gru = nn.GRU(input_dim, hidden_dim, batch_first=True)
        # 调用初始化权重的方法
        self._init_weights()

    def forward(self, x):
        # 前向传播
        # x 的形状为 (batch_size, seq_len, input_dim)
        # 不需要手动初始化隐藏状态，PyTorch 会自动初始化为零
        out, _ = self.gru(x)  # GRU 的输出
        return out

    def _init_weights(self):
        # GRU初始化
        for name, param in self.gru.named_parameters():
            if 'weight_ih' in name:
                nn.init.xavier_uniform_(param)  # 初始化输入到隐藏层的权重
            elif 'weight_hh' in name:
                nn.init.orthogonal_(param)  # 初始化隐藏层到隐藏层的权重
            elif 'bias' in name:
                nn.init.constant_(param, 0)  # 初始化偏置为0

# 参数设置
input_dim = 7  # 输入特征的维度
hidden_dim =  512  # 隐藏层的维度
batch_size = 32  # 批量大小
seq_len = 96  # 序列长度

# 创建模型实例
model = Model(input_dim, hidden_dim)

# 创建输入数据
# 输入数据的形状为 (batch_size, seq_len, input_dim)
batch_x = torch.randn(batch_size, seq_len, input_dim)

# 前向传播
output = model(batch_x)
print("Input shape:", batch_x.shape) # 输入的形状 (batch_size, seq_len, input_dim)
print("Output shape:", output.shape) # 输出的形状 (batch_size, seq_len, hidden_dim)

# 自动计算参数量
total_params = sum(p.numel() for p in model.parameters() if p.requires_grad)

# 打印每个参数的形状和数量
for name, param in model.named_parameters():
    if param.requires_grad:
        print(f"Parameter name: {name}, shape: {param.shape}, num_elements: {param.numel()}")

# 打印总参数量
print("Total parameters:", total_params)  # 总参数量



'''
GRU 的参数量计算公式如下：

对于一个 GRU 单元，假设：
- 输入特征维度为 input_dim。
- 隐藏层维度为 hidden_dim。

GRU 的参数包括：

- 输入到隐藏层的权重 (weight_ih)：
- 大小为 (3 * hidden_dim, input_dim)，因为 GRU 有三个门（更新门、重置门、候选隐藏状态）。
- 参数量为 3 * hidden_dim * input_dim。
- 隐藏层到隐藏层的权重 (weight_hh)：

- 大小为 (3 * hidden_dim, hidden_dim)，同样因为 GRU 有三个门。
- 参数量为 3 * hidden_dim * hidden_dim。
- 偏置项 (bias_ih 和 bias_hh)：

- 每个门有一个偏置项，大小为 (3 * hidden_dim)。
- 两个偏置项（bias_ih 和 bias_hh），总参数量为 2 * 3 * hidden_dim。

总参数量公式：
- 总参数量 = 3 * hidden_dim * input_dim + 3 * hidden_dim * hidden_dim + 2 * 3 * hidden_dim
'''
```



## Linear

- 输入形状：(batch_size, seq_len, input_dim)
- 输出形状：(batch_size, seq_len, output_dim)
- 参数量：$input\_dim * output\_dim + output\_dim$

- 使用场景：Linear 层可以处理各种形状（二维、三维、四维）的输入数据，本质是对最后一层进行维度变换

```python
import torch
import torch.nn as nn

# 参数设置
input_dim = 7  # 输入特征的维度
output_dim = 512  # 输出特征的维度
batch_size = 32  # 批量大小
seq_len = 96  # 序列长度

# 创建 Linear 层
linear = nn.Linear(input_dim, output_dim)

# 创建三维输入数据
# 输入数据的形状为 (batch_size, seq_len, input_dim)
x = torch.randn(batch_size, seq_len, input_dim)

# 前向传播
# Linear 层会自动应用到输入张量的最后一个维度
y = linear(x)

# 打印输入和输出的形状
print("Input shape:", x.shape)  # 输入形状：(batch_size, seq_len, input_dim)
print("Output shape:", y.shape) # 输出形状：(batch_size, seq_len, output_dim)

# 自动计算参数量
total_params = sum(p.numel() for p in linear.parameters() if p.requires_grad)

# 打印参数量 input_dim * output_dim + output_dim | 权重+偏置
print("Total parameters:", total_params)  # 总参数量
```

output：

```bash
Input shape: torch.Size([32, 96, 7])
Output shape: torch.Size([32, 96, 512])
Total parameters: 4096
```



## Cross Attention

$$
QK^TV=B_QL_QD_Q \cdot  B_KD_QL_V \cdot B_VL_VD_V
$$



- 输入：query、key、value
  - query ：$(\text{batch\_size}, \text{query\_len}, \text{query\_dim})$
  - key ：$(\text{batch\_size},\text{value\_len}, \text{query\_dim})$
  -  value ： $(\text{batch\_size}, \text{value\_len}, \text{value\_dim})$



- 输出：
  - **weighted_query**（重要） ：$(\text{batch\_size}, \text{query\_len}, \text{embed\_dim})$   
  - 注意力矩阵：$(\text{batch\_size}, \text{query\_len}, \text{value\_len})$ 

- 参数量：
  - query 层 ：( $\text{query\_dim} \times \text{embed\_dim} + \text{embed\_dim}$ ) 
  - value 层： ( $\text{value\_dim} × \text{embed\_dim} + \text{embed\_dim}$ ) 
  - key 层： ( $\text{query\_dim} × \text{embed\_dim} + \text{embed\_dim}$) 



> - key 的形状是紧跟着 query 和 value 的形状的
>
> - key_dim = query_dim；key_len = value_len
>
> - 多头注意力机制的参数量也是 三个线性层，注意力机制的计算反而都是一些矩阵运算，而不是新增的参数量
>
> - 线性层变换可以对齐维度
>
> - 我总觉得这里 可以打开思路，随便的 Q，KV 同源，只要线性变换以后，使得 key_dim=query_dim，key_len=value_len就行 ，注意力矩阵的核心计算公式 $QK^TV $ 
>
>   关键在这几行：
>
>   ```python
>   self.query_proj = nn.Linear(query_dim, embed_dim)
>   self.key_proj = nn.Linear(query_dim, embed_dim)
>   self.value_proj = nn.Linear(value_dim, embed_dim)
>   ```
>
>   容易搞错的是，后面要切分多头，容易出错，要保证嵌入维度能被头数整除



```python
import torch
import torch.nn as nn
import torch.nn.functional as F

class CrossAttention(nn.Module):
    def __init__(self, query_dim,value_dim,embed_dim, num_heads):
        super(CrossAttention, self).__init__()

        self.query_dim = query_dim
        self.value_dim = value_dim
        self.embed_dim = embed_dim
        self.num_heads = num_heads
        self.head_dim = embed_dim // num_heads

        # 确保嵌入维度可以被头数整除
        assert self.head_dim * num_heads == self.embed_dim, "embed_dim must be divisible by num_heads"

        # 查询、键、值的线性变换
        self.query_proj = nn.Linear(query_dim, embed_dim)
        self.key_proj = nn.Linear(query_dim, embed_dim)
        self.value_proj = nn.Linear(value_dim, embed_dim)

        # 缩放因子
        self.scaling = self.head_dim ** -0.5

    def forward(self, query, key, value, mask=None):
        """
        query: (batch_size, target_seq_len, embed_dim)
        key: (batch_size, source_seq_len, embed_dim)
        value: (batch_size, source_seq_len, embed_dim)
        mask: (batch_size, target_seq_len, source_seq_len) or (target_seq_len, source_seq_len)
        """
        batch_size, target_seq_len, query_dim = query.size()

        source_seq_len = key.size(1)

        # 线性变换
        # (batch,query_len,query_dim)->(batch,query_len,embed_dim)->(batch,num_heads,query_len,head_dim)
        query = self.query_proj(query).view(batch_size, target_seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        # key (batch_size, value_len, query_dim)->(batch,value_len,embed_dim)->(batch,num_heads,value_len,head_dim)
        key = self.key_proj(key).view(batch_size, source_seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        # value (batch_size, value_len, value_dim)->(batch,value_len,embed_dim)->(batch,num_heads,value_len,head_dim)
        value = self.value_proj(value).view(batch_size, source_seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        # print(query.shape,key.shape,value.shape)
        # 计算注意力分数
        attention_scores = torch.matmul(query, key.transpose(-2, -1)) * self.scaling

        # 应用掩码（如果有的话）
        if mask is not None:
            attention_scores = attention_scores.masked_fill(mask == 0, float('-inf'))

        # 计算注意力权重
        attention_weights = F.softmax(attention_scores, dim=-1)

        # 应用注意力权重
        attention_output = torch.matmul(attention_weights, value)

        # 调整形状
        attention_output = attention_output.transpose(1, 2).contiguous().view(batch_size, target_seq_len, -1)

        return attention_output, attention_weights

# 示例用法
if __name__ == "__main__":
    # 参数设置
    embed_dim = 512
    num_heads = 8
    batch_size = 32

    query_len = 96
    value_len = 48
    key_len = value_len

    query_dim = 7
    key_dim = query_dim
    value_dim = 8

    # 创建模型实例
    cross_attention = CrossAttention(query_dim,value_dim,embed_dim, num_heads)
    print(cross_attention)
    '''
        CrossAttention(
            (query_proj): Linear(in_features=7, out_features=512, bias=True)
            (key_proj): Linear(in_features=8, out_features=512, bias=True)
            (value_proj): Linear(in_features=8, out_features=512, bias=True)
        )    
    '''
    # 创建输入数据
    # query (batch_size, query_len, query_dim)
    query = torch.randn(batch_size, query_len, query_dim)
    # key & value 同源 
    # key (batch_size, value_len, query_dim)
    key = torch.randn(batch_size, value_len, query_dim)
     # value (batch_size, value_len, value_dim)
    value = torch.randn(batch_size, value_len, value_dim)

    # 前向传播
    attention_output, attention_weights = cross_attention(query, key, value)

    print("Input query shape:", query.shape)  # (batch_size, query_len, query_dim)
    print("Input value shape:", value.shape) # (batch_size, value_len, value_dim)
    print("Input key shape:", key.shape) # (batch_size, value_len, query_dim)

    
    print("Output weighted_query shape:", attention_output.shape)  # (batch_size, query_len, embed_dim)
    # (batch_size, num_heads, query_len, value_len)
    print("Attention matrix shape:", attention_weights.shape)  

    # 打印每个参数的名称和数量
    for name, param in cross_attention.named_parameters():
        print(f"{name}: {param.numel()}")



    # 自动计算参数量
    total_params = sum(p.numel() for p in cross_attention.parameters() if p.requires_grad)


    # 打印参数量 
    print("Total parameters:", total_params)  # 总参数量

'''
交叉注意力的参数量就是三个线性层的参数量之和：

后面都是矩阵运算，没有额外的参数。

query_proj 参数量 = 输入维度 × 输出维度 + 偏置项 = query_dim × embed_dim + embed_dim = 7 × 512 + 512 = 4096

value_proj 参数量 = 输入维度 × 输出维度 + 偏置项 = value_dim × embed_dim + embed_dim = 8 × 512 + 512 = 4608

key_proj 参数量 = 输入维度 × 输出维度 + 偏置项 = query_dim × embed_dim + embed_dim = 7 × 512 + 512 = 4096

总参数量 = query_proj 参数量 + key_proj 参数量 + value_proj 参数量
         = 4096 + 4608 + 4096
         = 12800
'''
```

### 看这里！MHCA

多头注意力机制(经 Linear 之后，只需要考虑计算时维度的对齐即可)

$QK^TV = B L D \cdot B (S D)^T \cdot B S d
\\= LD \cdot DS \cdot Sd
\\=Ld$  其中 $attn = LS$

$$
dim_{key}=dim_{query} 
$$

$$
len_{key}=len_{value}
$$

> value的嵌入维度就是 weighted_query的维度

一般来说，KV 同源（ $ dim_{key}=dim_{value},\, len_{key}=len_{value} $  ），所以真正需要考虑的是 Q 和 KV 的嵌入维度相等即可

$QK^TV = B L D \cdot B (S D)^T \cdot B S D
\\= LD \cdot DS \cdot SD
\\=LD$

在 QKV 三者嵌入维度相等的情况下，只需要满足 KV 同源，就可以进行自注意力机制的计算

> 算法 : 多头注意力机制的计算过程
>
> 输入 : $Q \in R^{B\times L \times D}$  、$KV \in R^{B\times S \times d}$ 
>
> 输出 :  $weighted\_Q \in R^{B \times L_Q \times D}$
>
> 步骤 : 
>
> 1. Linear：Q | ( $B\times L \times D \to B\times L \times embed\_Q$ )
> 1. Linear：KV | ( $B\times S \times D \to B\times S \times embed\_KV \to B\times S \times embed\_Q$ )
> 1. $softmax(\frac{QK^T}{\sqrt{embed\_Q}}) V$ | $B\times L \times embed\_Q$ 

所以在实际使用中，最常见的情况：① Q 和 KV 不同源 ② K和 V 同源 $\to$ 只要经过 Linear 以后，使得Q 与 KV 的嵌入维度相同就可以进行注意力机制运算。

## 卷积嵌入

- 输入：($batch\_size, seq\_len, input\_dim$) 
- 输出：($batch\_size, seq\_len, output\_dim$)
- 参数量：$\text{input\_dim}  \times \text{output\_dim} \times \text{kernel\_size}$
- 使用场景：在时序中用于特征嵌入



```python
import torch
import torch.nn as nn

class TokenConvModel(nn.Module):
    def __init__(self, c_in, d_model, kernel_size):
        super(TokenConvModel, self).__init__()
        # 定义 1D 卷积层
        padding = 1 if torch.__version__ >= '1.5.0' else 2
        self.tokenConv = nn.Conv1d(in_channels=c_in, out_channels=d_model,
                                   kernel_size=kernel_size, padding=padding, padding_mode='circular', bias=False)

    def forward(self, x):
        # 输入 x 的形状为 (batch_size, seq_len, c_in)
        # 调整维度顺序以适应 Conv1d 的输入要求 (batch_size, c_in, seq_len)
        # 应用 1D 卷积
        x = self.tokenConv(x.permute(0, 2, 1)).transpose(1, 2)
        # 调整维度顺序回 (batch_size, seq_len, d_model) 
        return x

# 参数设置
c_in = 7  # 输入通道数
d_model = 512  # 输出通道数
kernel_size = 3  # 卷积核大小
batch_size = 32  # 批量大小
seq_len = 96  # 序列长度

# 创建模型实例
model = TokenConvModel(c_in, d_model, kernel_size)

# 创建输入数据
# 输入数据的形状为 (batch_size, seq_len, c_in)
x = torch.randn(batch_size, seq_len, c_in)

# 前向传播
output = model(x)

# 打印输入和输出的形状
print("Input shape:", x.shape)  # 输入形状：(batch_size, seq_len, c_in)
print("Output shape:", output.shape)  # 输出形状：(batch_size, seq_len, d_model)

# 自动计算参数量
total_params = sum(p.numel() for p in model.parameters() if p.requires_grad)

# 打印参数量 \text{out_channels} \times \text{in_channels} \times \text{kernel_size}
# bias=False，没有偏置项
print("Total parameters:", total_params)  # 总参数量


'''
    Input shape: torch.Size([32, 96, 7])
    Output shape: torch.Size([32, 96, 512])
    Total parameters: 10752
'''
```



## 各种伪代码

### ==**名称：三维转二维，Linear 伪代码**== 

输入：$input \in R^{B\times L \times D}$

输出：$output \in R^{B\times P \times D}$

步骤：

1. $x \in R^{B\times LD } \gets reshape(input,shape=(B\times L \times D))$ 
2. $y \in R^{B\times PD}\gets Linear(x,shape=(B\times LD );(in\_feature=LD,out\_feature=PD))$ 
3. $output\in R^{B\times P \times D} \gets reshape(y,shape=(B\times PD))$ 

更简洁：

1. $x \in R^{B\times LD } \gets reshape(input;(B,-1)) $ 
2. $y \in R^{B\times PD}\gets Linear(x;(LD,PD))$  
3. $output\in R^{B\times P \times D} \gets reshape(y;(B,P,D)) $  

### **==名称：1D 卷积&Linear 的等价性==**

输入：$input\in R^{B\times L \times D}$

输出：$output\in R^{B\times L \times d}$

Linear 步骤：

1. $output \in R^{B\times L \times d}\gets Linear(input;(D,d)) $

$params=Dd+d$ 

Conv步骤：

1. $ x\in R^{B\times D \times L} \gets transpose(input;(1,2))$
2. $y \gets conv1d(x;(C\_in=D,C\_out=d,kernel\_size=1,bias=True))  $  
3. $ output \in R^{B\times L \times d} \gets transpose(y;(1,2))$

$params=C\_in \times C\_out \times kernel\_size+ C\_out $  





## 参数量公式

- Linear $(in_{dim}+1) \times out_{dim}$
- Conv1d： $C_{in} \times C_{out}\times k+C_{out}$ 
