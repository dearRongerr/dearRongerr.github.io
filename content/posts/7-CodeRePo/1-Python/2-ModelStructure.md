---
date: 2025-05-06T21:59:57+08:00
draft: false
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
title: Python 查看模型参数
math: true
toc: true
tags: ['Python']
categories:
  - '⛄好用的代码片'
---

model init 结束以后就可以 查看模型参数了

```python
model = Model(args)

summary(model, input_data=[x_enc, x_mark_enc, x_dec, x_mark_dec])
print(model)
total_params = sum(p.numel() for p in model.parameters())
print(f"模型总参数量: {total_params:,}")

outputs = model(x_enc,x_mark_enc,x_dec,x_mark_dec)
print(outputs.shape)
```

