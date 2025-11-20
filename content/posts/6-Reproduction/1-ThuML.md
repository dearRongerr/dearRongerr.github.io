---
date: '2025-05-02T18:38:00+08:00'
lastmod: '2025-05-02T18:38:00+08:00'
draft: false
title: 'TSLib时序库代码复现 第一篇'
math: true
# 'Hugo'  ,  'Python'  ,  'DeepLearning'  ,  'Statistics','VSCode'
# categories: ['代码复现']
# series: [""]  # 添加此行，用于系列文章
# series_order: 1  # 添加此行，用于指定文章在系列中的顺序
# '注意力机制'  ,  '入门'  ,  'Sticks':注意事项,  'TODO'
# tags: ['Python','逐步系列','复现之前']
toc: true
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
categories:
  - '⛓️‍💥代码复现'
---

| 仓库         | [https://github.com/thuml/Time-Series-Library](https://github.com/thuml/Time-Series-Library) |
| ------------ | :----------------------------------------------------------: |
| 环境逐步配置 | [https://dearrongerr.github.io/Rongerr.github.io/sticks/5_preReproduction/](https://dearrongerr.github.io/Rongerr.github.io/sticks/5_preReproduction/) |

## 配置运行环境

- git clone

```bash
git clone https://github.com/thuml/Time-Series-Library.git
```

- 新建 Python 环境, 激活,  并安装所需的包,Python3.8 适配的 torch 环境

```python
conda env list
conda create -n TSLib python==3.8 -y
conda activate TSLib
pip install -r requirements.txt
conda install pytorch==2.0.0 torchvision torchaudio pytorch-cuda==11.8 -c pytorch -c nvidia -y
pip install debugpy
```

这个库,居然不给 torch 环境🙂‍↔️一直报错

## 配置调试环境

- 配置调试环境

```
pip install debugpy
```

- 修改 `.sh` 文件

```shell
python -u run.py
# 需要手动点击 调试，开始运行
python -m debugpy --listen 5998 --wait-for-client run.py 
# 可以移除 --wait-for-client 参数，让程序不必等待客户端连接就开始运行
python -m debugpy --listen 5998 run.py \
```

- 修改配置文件 `"configurations"` , 点击小虫子配置

```Python
        {
            "name": "[这里更换为任意名称]",
            "type": "debugpy",
            "justMyCode": true,
            "request": "attach",
            "connect": {
                "host": "localhost",
                "port": 5998
            }
        },
```

- 调试显示形状

```python
import custom_repr
```

- 代码: [https://github.com/dearRongerr/TSF_Reproduction/blob/main/custom_repr.py](https://github.com/dearRongerr/TSF_Reproduction/blob/main/custom_repr.py)

## 启动调试

- `run.py` 打断点	
- 启动 shell 脚本:  `sh 脚本路径`
- 点击小虫子,连接调试

这个仓库复现会报错: RuntimeError No CUDA GPUs are available