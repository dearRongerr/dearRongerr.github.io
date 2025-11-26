---
slug: 8945e091f1
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
title:  服务器 + Tensorboard使用
subtitle:
date: 2025-11-25T08:43:20+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github BugFix Screen SOP
tags:
  - Shell
  - Python
  - Screen
  - VsCode
  - SOP
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点' | '草稿'
categories:
  - '⛄好用的代码片'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->



> 使用场景：观察训练集 & 测试集误差 

总体步骤：

1. 安装 tensorboard ：`pip install tensorboard`
2. train函数拿到epoch级的 train_loss 、 vali_loss、test_loss
3. shell 文件最后启动 tensorboard
4. 开启 Screen 会话，跑代码并开启 tensorboard

① screen -S 

② screen -r

③ sh 

④ 开启新窗口  Ctrl(^)+A+C 

⑤ 在新窗口启动 TensorBoard

```bash
tensorboard --logdir ./runs --host 0.0.0.0 --port 6006
```

5. （vscode 会自动转发端口，这步不用管了）本地终端：把服务器上的 8899 端口映射到本地浏览器：

 http://localhost:8899/#timeseries

http://localhost:8899

```bash
ssh -L 6006:localhost:6006 student@210.26.51.126

ssh -L 8899:localhost:6006 student@210.26.51.126
```



6. （vscode 自动弹出，直接点即可）本地电脑，打开 localhost:6006 即可看曲线




## train 函数修改

`exp_main.py`  ，train 函数，拿到epoch的 train_loss 、 vali_loss、test_loss

```python
# ===== 文件顶部 =====
from torch.utils.tensorboard import SummaryWriter
import os

class Exp_Main(Exp_Basic):
    def __init__(self, args):
        super().__init__(args)
        # 只建一次 SummaryWriter
        os.makedirs('./runs', exist_ok=True)
        self.writer = SummaryWriter(log_dir=f'./runs/{args.model_id}')  

    def train(self):
        for epoch in range(epochs):
             # ...
             print("Epoch: {} cost time: {}".format(epoch + 1, time.time() - epoch_time))
            train_loss = np.average(train_loss)
            vali_loss = self.vali(vali_data, vali_loader, criterion)
            test_loss = self.vali(test_data, test_loader, criterion)
            
            # 用的时候直接 self.writer
            # ===== TensorBoard 记录 =====
            self.writer.add_scalar('Loss/Train', train_loss, epoch+1)
            self.writer.add_scalar('Loss/Val', vali_loss, epoch+1)
            self.writer.add_scalar('Loss/Test', test_loss, epoch+1)
            # ...
        self.writer.flush() #清空缓存
        self.writer.close() # 训练结束统一关闭
```

附另外一种写法，画在一张图上

```python
    # 注意是 add_scalars（带 s）        
    self.writer.add_scalars('Loss', {   
                'Train': train_loss,
                'Val':   vali_loss,
                'Test':  test_loss,
            }, epoch)
```

目的：

- 若三条线越靠越近 → 模型稳定；
- 若Train ↓ Val/Test ↑ → 过拟合；
- 若Test 远高于 Val → 分布漂移 / 数据泄漏。



### 脚本最后加

```shell
if [ ! -d "./logs" ]; then
    mkdir ./logs
fi

if [ ! -d "./logs/Basic" ]; then
    mkdir ./logs/Basic
fi
model_name=Basic_DyConv

root_path_name=./dataset/
data_path_name=ETTh1.csv
model_id_name=ETTh1
data_name=ETTh1

d_model=512
dropout=0.5
d_ff=2048

date=$(date +%m%d)
# python -u run_longExp.py
# python -m debugpy --listen 5998 --wait-for-client run_longExp.py
# for pred_len in 96
seq_len=96
for pred_len in 96 192 336 720
do
    python -u run_longExp.py \
      --is_training 1 \
      --root_path $root_path_name \
      --data_path $data_path_name \
      --model_id $model_id_name'_'$seq_len'_'$pred_len \
      --model $model_name \
      --data $data_name \
      --features M \
      --seq_len $seq_len \
      --pred_len $pred_len \
      --seg_len 24 \
      --enc_in 7 \
      --d_model $d_model \
      --dropout $dropout \
      --train_epochs 30 \
      --patience 10 \
      --rnn_type gru \
      --d_ff $d_ff \
      --dec_way pmf \
      --channel_id 1 \
      --loss mae \
      --itr 1 --batch_size 128 --learning_rate 0.001 > logs/Basic/$date'_'$model_name'_'$model_id_name'_'$seq_len'_'$pred_len.log
      # ========= TensorBoard =========
        nohup tensorboard --logdir ./runs \
                        --host 0.0.0.0 \
                        --port 6006 \
                        > tb.log 2>&1 &
        echo "TensorBoard started on port 6006, logdir=./runs"
done
```

### 本地端口映射服务器

本地端口映射服务器需要密码，配置免密登录

```bash
# （1）本地生成密钥：回车两次，生成免密密钥
ssh-keygen -t rsa -C "1939472345@qq.com"
# （2）把公钥拷贝到服务器，第一次仍需输入密码，以后再也不需要
ssh-copy-id xiehr2023@210.26.51.126
# （3）本地端口映射服务器
ssh -L 6006:localhost:6006 xiehr2023@210.26.51.126
```

