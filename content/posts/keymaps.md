+++
date = '2025-06-04T13:35:28+08:00'
draft = false
title = 'Keymaps'
categories = ["笔记"]
tags = ["备忘"]
+++

## VSCode Neovim 快捷键配置使用文档

这份文档介绍了您在 VSCode 中使用 Neovim 扩展时配置的快捷键。这些快捷键旨在提高您的编码效率，结合了 Vim 的强大编辑能力和 VSCode 的现代功能。

### 基础配置

- **Leader 键**: 空格键 (`<Space>`)

### Vim 基础操作

#### 缩进操作
- `<` (可视模式): 减少缩进并保持选择
- `>` (可视模式): 增加缩进并保持选择

#### 文本移动
- `J` (可视模式): 将选中文本下移一行
- `K` (可视模式): 将选中文本上移一行
- `J` (可视块模式): 将选中文本下移一行
- `K` (可视块模式): 将选中文本上移一行

#### 粘贴操作
- `p` (可视模式): 粘贴时保留原始复制内容

#### 搜索
- `<Esc>` (普通模式): 退出并清除搜索高亮

### VSCode 集成命令

#### 常用操作
- `<leader>w`: 保存文件
- `<leader>q`: 关闭当前编辑组中的编辑器
- `<leader>-`: 向下拆分编辑器
- `<leader>\`: 向右拆分编辑器
- `<leader>t`: 切换终端
- `<leader>d` (普通模式): 插入调试打印语句
- `<leader>a`: 快速修复
- `<leader>sp`: 查看问题面板
- `<leader>cn`: 清除所有通知
- `<leader>ff`: 快速打开文件
- `<leader>cp`: 显示命令面板
- `<leader>pr`: 运行代码
- `<leader>fd`: 格式化文档

#### 分屏导航
- `<leader>h`: 聚焦左侧编辑组
- `<leader>l`: 聚焦右侧编辑组
- `<leader>k`: 聚焦上方编辑组
- `<leader>j`: 聚焦下方编辑组

#### 标签页切换
- `L` (普通/可视模式): 切换到下一个编辑器
- `H` (普通/可视模式): 切换到上一个编辑器

### Harpoon 插件快捷键
Harpoon 是一个快速导航到常用文件的插件。

- `<leader>oa`: 添加当前编辑器到 Harpoon
- `<leader>oo`: 打开 Harpoon 快速选择
- `<leader>oe`: 编辑 Harpoon 编辑器列表
- `<leader>o1` 到 `<leader>o9`: 快速跳转到 Harpoon 中的第 1-9 个文件

### 项目管理快捷键
- `<leader>pa`: 保存当前项目
- `<leader>po`: 在新窗口中列出项目
- `<leader>pe`: 编辑项目列表

### 调试辅助
- `<leader>d`: 插入调试打印语句（在普通模式下）

### 使用技巧

1. **高效导航**：使用 `H` 和 `L` 快速在标签页间切换，使用 `<leader>h/j/k/l` 在分屏间导航。

2. **快速访问常用文件**：利用 Harpoon 插件（`<leader>o` 系列快捷键）标记和快速访问常用文件。

3. **项目管理**：使用 `<leader>p` 系列快捷键管理您的项目。

4. **调试辅助**：需要调试时，使用 `<leader>d` 快速插入调试打印语句。

5. **高效编辑**：利用 Vim 的文本移动功能（`J` 和 `K`）快速调整代码位置。

这些快捷键配置旨在提供一个高效的编码环境，结合了 Vim 的编辑效率和 VSCode 的现代功能。您可以根据个人习惯在 <mcfile name="vscode_keymaps.lua" path="c:\Users\1\AppData\Local\nvim\lua\user\vscode_keymaps.lua"></mcfile> 文件中进一步自定义这些快捷键。

## Tmux 配置使用文档

### 前缀键

前缀键已从默认的 `Ctrl+b` 更改为 `Ctrl+s`。

### 基本操作

#### 退出和重连会话(不会杀死会话)

- `Ctrl+s d` 从当前 tmux 会话中 分离，但会话仍然在后台运行。
- `tmux attach -t <会话名或ID>` 重新连接到指定会话 
- `tmux attach` 重新连接最近的会话 

#### 终止tmux会话

- `Ctrl + d` 关闭当前会话。
- `tmux kill-server`  这会直接关闭 tmux 服务器，所有会话都会被终止。
- `tmux kill-session -t 1` 关闭指定序号(序号为1)的会话


#### 会话管理
- `Ctrl+s Ctrl+c` - 创建新会话
- `Ctrl+s r` - 重新加载配置文件

#### 窗口管理
- `Alt+o` - 在当前路径创建新窗口
- `Alt+O` - 将当前面板分离到新窗口
- `Alt+Q` - 关闭当前面板
- `Ctrl+s Ctrl+p` - 切换到上一个窗口
- `Ctrl+s Ctrl+n` - 切换到下一个窗口
- `Alt+[1-9]` - 切换到指定编号的窗口
- `Alt+!@#$%^&*(` - 将当前面板合并到指定编号的窗口

#### 面板分割
- `Ctrl+s k` - 在上方创建新面板
- `Ctrl+s j` - 在下方创建新面板
- `Ctrl+s h` - 在左侧创建新面板
- `Ctrl+s l` - 在右侧创建新面板
- `Alt+f` - 最大化/还原当前面板

#### 面板导航
- `Ctrl+s [1-9,0]` - 选择当前窗口中的指定面板
- `Alt+h` - 选择左侧面板
- `Alt+j` - 选择下方面板
- `Alt+k` - 选择上方面板
- `Alt+l` - 选择右侧面板
- `Ctrl+s >` - 向下交换面板
- `Ctrl+s <` - 向上交换面板
- `Ctrl+s |` - 交换面板
- `Alt+Space` - 切换下一个布局

#### 面板选择与移动
- `Ctrl+s W` - 选择窗口/面板
- `Ctrl+s S` - 选择并垂直移动面板
- `Ctrl+s V` - 选择并水平移动面板

#### 面板大小调整
- `Alt+H` - 向左调整面板大小
- `Alt+J` - 向下调整面板大小
- `Alt+K` - 向上调整面板大小
- `Alt+L` - 向右调整面板大小

#### 复制模式
- `Alt+v` - 进入复制模式
- 复制模式中的快捷键:
  - `v` - 开始选择
  - `Ctrl+v` - 切换矩形选择
  - `h/j/k/l` - 光标移动
  - `w` - 移动到下一个词尾
  - `K/J` - 上/下移动5行
  - `H/L` - 移动到行首/行尾
  - `Y` - 复制到行尾
  - `y` - 复制选择内容并退出复制模式
  - `=` - 重复上次搜索

#### 剪贴板操作
- `Ctrl+s b` - 列出缓冲区
- `Ctrl+s p` - 粘贴缓冲区内容

#### 特殊功能
- `Ctrl+s Ctrl+g` - 切换同步输入模式（在多个面板中同时输入）
- `Ctrl+s s` - 切换状态栏显示/隐藏
- `Alt+w` - 使用 fzf 打开面板选择器

### 其他特性

- 鼠标支持已启用
- 历史记录限制为10000行
- 使用 vi 模式进行复制操作
- 使用 emacs 键绑定进行命令输入
- 集成了 tmux-powerline 状态栏主题

### 状态栏

状态栏显示了会话信息、窗口列表以及系统信息，使用 tmux-powerline 提供更丰富的视觉效果。

### 注意事项

- 此配置文件依赖于 `~/.config/tmux/fzf_panes.tmux` 和 `~/.config/tmux/tmux-powerline` 目录中的脚本
- 配置文件路径为 `~/.tmux.conf`

## potplayer
- F6 	playlist on/off
- f12 	file Navigator
- 5	Screen Size:Maximum
- PgUp	Play Previous File
- PgDn	Play Next File
- Enter 	Fullscreen
- C	Speed Faster
- X	Speed Slower