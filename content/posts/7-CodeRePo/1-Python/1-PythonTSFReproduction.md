---
date: '2025-05-02T23:16:39+08:00'
lastmod: '2025-05-02T23:16:39+08:00'
draft: false
title: '逐步 | 时间序列模型单独摘出来'
math: true
# 'Hugo'  ,  'Python'  ,  'DeepLearning'  ,  'Statistics','VSCode'
# categories: ['代码复现']
# series: [""]  # 添加此行，用于系列文章
# series_order: 1  # 添加此行，用于指定文章在系列中的顺序
# '注意力机制'  ,  '入门'  ,  'Sticks':注意事项,  'TODO'
tags: ['Python','SOP']
categories:
  - '⛄好用的代码片'
toc: true
---

动机 : 逐步复现>>摘出模型文件

## 从哪里开始?  TSF 时序库有

> TIME-SERIES-LIBRARY >> models >> TimesMixer.py

## 新建虚拟环境, 激活虚拟环境

```python
conda create -n dave python==3.8 -y
conda activate dave
pip install torchinfo
```

- 注意, 要 ==切换左下角 VSCode 的运行环境== , 保证点击小虫子,调试 run.py 时 使用的是 想要的是虚拟环境
- VScode 虚拟环境的管理 GUI 和 命令行 是不一样的
- **一句话** : 命令行运行命令, 使用的就是命令行的环境; GUI 点击, 使用的就是 GUI 的激活环境

## 调试环境

① run.py 右上角 $>>$ 小虫子 $>>$ 使用 launch.json 进行调试 $>>$ 添加 `"justMyCode": true`

```
        {
            "name": "Python 调试程序: 当前文件",
            "type": "debugpy",
            "request": "launch",
            "program": "${file}",
            "justMyCode": true,
            "console": "integratedTerminal"
        }
```

② 点击 左上角 小虫子 , 调试运行 即可

## 新建 项目文件夹

- 新建 文件夹 `250506-TimeMixer`

### **现在看 250506-TimeMixer** 

-  新建 `custom_repr.py`    ([点击查看填写的内容](https://github.com/dearRongerr/TSF_Reproduction/blob/main/custom_repr.py))  ,
-  复制  `run.py`   
-  复制 `TimesMixer.py`  :  `TIME-SERIES-LIBRARY >> models >> TimesMixer.py` 到 `250506-TimeMixer`

### **现在看 TimeMixer.py** 

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
from layers.Autoformer_EncDec import series_decomp
from layers.Embed import DataEmbedding_wo_pos
from layers.StandardNorm import Normalize
```

① 新建 layers 文件夹

② 需要什么 去 layers 中找什么 , 粘贴什么, 并检查没有使用自定义类了

### **现在看 run.py** 

(1) 导入 

```python
from TimeMixer import Model
import custom_repr
from torchinfo import summary
```

(2) 删除 `print_args(args)` 后面的所有内容

```python
    if args.use_gpu and args.use_multi_gpu:
        args.devices = args.devices.replace(' ', '')
        device_ids = args.devices.split(',')
        args.device_ids = [int(id_) for id_ in device_ids]
        args.gpu = args.device_ids[0]

    print('Args in experiment:')
    print_args(args)
```

(3) 后面添加新的内容, 对照[官方仓库ETTh1脚本](https://github.com/kwuking/TimeMixer/blob/main/scripts/long_term_forecast/ETT_script/TimeMixer_ETTh1_unify.sh), 传入自定义参数 , 类似这种 , 在两个 print 之间添加, 保正打印的是正在使用的参数

```python
    print('Args in experiment:')
    args.model = 'TimeMixer'
    args.seq_len = 96
    args.label_len =  0
    args.pred_len = 720
    args.enc_in = 7
    args.c_out  = 7

    args.is_training = 1 
    args.features =  "M"
    
    args.e_layers=2
    args.des  = 'Exp'
    args.itr = 1
    args.down_sampling_layers=3
    args.down_sampling_window=2
    args.down_sampling_method = "avg "
    args.learning_rate=0.01
    args.d_model=16
    args.d_ff=32
    args.train_epochs=10
    args.patience=10
    args.batch_size=16
    args.learning_rate=0.01
    print_args(args)
```

在  `print_args(args)`  后面 继续添加以下内容:

```python
args.batch_size=16
args.learning_rate=0.01
print_args(args)

x_enc = torch.randn(args.batch_size,args.seq_len,args.enc_in)
x_mark_enc = torch.randn(args.batch_size,args.seq_len,4)
x_dec = torch.randn(args.batch_size,args.pred_len,args.enc_in)
x_mark_dec = torch.randn(args.batch_size,args.pred_len,4)

model = Model(args)
summary(model, input_data=[x_enc, x_mark_enc, x_dec, x_mark_dec])
print(model)
outputs = model(x_enc,x_mark_enc,x_dec,x_mark_dec)
print(outputs.shape)
```

(4)  **运行**

① run.py 右上角 $>>$ 小虫子 $>>$ 使用 launch.json 进行调试

② 后面可以 直接 点击 左上角 小虫子运行

③ torchinfo summary 执行一次即可, 需要执行一次完整的前向传播过程

- 还是会有一些小错误,缝缝补补, 比如自定义库的调用

```python
import argparse
import os
import torch
import torch.backends
# from exp.exp_long_term_forecasting import Exp_Long_Term_Forecast
# from exp.exp_imputation import Exp_Imputation
# from exp.exp_short_term_forecasting import Exp_Short_Term_Forecast
# from exp.exp_anomaly_detection import Exp_Anomaly_Detection
# from exp.exp_classification import Exp_Classification
# from utils.print_args import print_args
import random
import numpy as np
```

- 提示错误 : 以下参数是必填参数 `required`

```python
run.py: error: the following arguments are required: --task_name, --is_training, --model_id, --model, --data
```

- [x] Solutions, 一个个 `command + F`  删除 `required=True`

```python
parser.add_argument('--task_name', type=str, required=True, default='long_term_forecast',
                help='task name, options:[long_term_forecast, short_term_forecast, imputation, classification, anomaly_detection]')
```

- [x] 或者 command + F 查找 `required=True` ,一个个删除, 必填参数都在附近

- 我遇到好离谱的错误, 多打了个空格报错

```python
args.down_sampling_method = "avg"
```

## summary

新建 `summary.log` 存储模型一次前向传播的过程

然后注意掉即可

## model

model.log & 参数数量

