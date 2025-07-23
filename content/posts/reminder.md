+++
date = '2025-06-17T09:47:08+08:00'
draft = false
title = 'Reminder'
description = '个人备忘录'
+++

# 一、命令

## 1.python

### (1).清华源

```shell
-i https://pypi.tuna.tsinghua.edu.cn/simple
```

### (2).tensorboard

通过SSH将远程服务器的TensorBoard端口映射到本地，实现本地浏览器访问。

1. **在本地终端**执行端口映射命令：
   ```bash
   ssh -L 本地端口:localhost:远程端口 -p 远程服务器端口 用户名@远程服务器IP
   ```
   - 例如：`ssh -L 6006:localhost:6006 -p 25532 user@192.168.1.100`
   - 含义：将远程服务器的6006端口（TensorBoard默认端口）映射到本地的6006端口

2. **在远程服务器上**启动TensorBoard：
   登录远程服务器后，进入日志文件所在目录，运行：
   ```bash
   tensorboard --logdir=你的日志目录 --port=6006
   ```
   （确保端口与步骤1中的“远程端口”一致）

3. **在本地浏览器**访问：
   打开 `http://localhost:6006` 即可查看远程服务器上的TensorBoard日志。
