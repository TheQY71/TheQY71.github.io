+++
date = '2025-06-11T15:16:54+08:00'
draft = false
title = 'Server Recovery from Backup Guide'
+++

## 迁移配置文件

将`～/work/server_backup`下的配置文件从本地移动到服务器下`/root/`下

文件如下：
```shell
~/work/server_backup 
❯ tree -a
.
├── .tmux.conf
└── xterm-kitty.terminfo
```

## 配置terminfo

不配置这个的话，当我使用kitty ssh连接服务器，无法使用tmux。

通过下面这行命令安装
```shell
tic -x -o ~/.terminfo xterm-kitty.terminfo
```

## 添加alias

添加下面这几行代码到`~/.bashrc`文件末尾
```
alias c='clear'
alias qy='cd /opt/data/private/qy'
alias t='tmux ls'
```