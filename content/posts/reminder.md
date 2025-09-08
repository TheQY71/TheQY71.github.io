+++
date = '2025-06-17T09:47:08+08:00'
draft = false
title = 'Reminder'
description = '个人备忘录'
weight = 1
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
   

## 2.linux

### (1).指定只使用服务器的第一张卡跑代码：

```shell
CUDA_VISIBLE_DEVICES=0 python main_stage1.py
```

### (2).压缩和解压缩文件夹

压缩
```shell
tar -czvf 压缩文件名.tar.gz 文件夹名
```

解压缩
```shell
tar -xzvf 压缩文件名.tar.gz
```

# 二、keymaps

## VSCode Neovim 快捷键配置使用文档

在 VSCode 中使用 Neovim 扩展时配置的快捷键。

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

# 三、DeBug

好的，没问题。这是一份为你整理的关于本次 `ModuleNotFoundError` 问题排查与解决的 Markdown 笔记。

-----

## Python `ModuleNotFoundError` 问题排查与解决方案笔记

### 一、问题现象

在运行或调试 Python 项目时，明明模块文件真实存在于项目目录中，但解释器依然抛出 `ModuleNotFoundError` 异常。

**本次案例：**

  * **报错信息**: `ModuleNotFoundError: No module named 'utils.logger'`
  * **项目结构**:
    ```
    /home/ubuntu/qy/LlamaGen/
    ├── tokenizer/
    │   └── tokenizer_image/
    │       └── vq_train_sigle.py   <-- 运行的脚本
    └── utils/
        ├── __init__.py
        └── logger.py             <-- 尝试导入的模块
    ```
  * **触发代码** (位于 `vq_train_sigle.py`): `from utils.logger import create_logger`

### 二、根本原因分析

该问题的核心是 **Python 的模块搜索路径 (`sys.path`) 机制**。

当 Python 执行一个脚本文件时（例如 `.../vq_train_sigle.py`），它默认会将**该脚本所在的目录**（即 `.../tokenizer/tokenizer_image/`）添加到 `sys.path` 列表中。

当代码执行 `from utils.logger ...` 时，Python 会遍历 `sys.path` 中的所有路径，去寻找一个名为 `utils` 的包或模块。因为它只在 `.../tokenizer/tokenizer_image/` 目录下寻找，而 `utils` 目录位于项目根目录 `/home/ubuntu/qy/LlamaGen/`，所以自然找不到，导致报错。

**结论：** 问题的根源不在于文件是否存在，而在于**项目根目录没有被添加到 Python 的模块搜索路径中**。

### 三、解决方案

#### 方案一：配置 IDE 调试器的工作目录 (推荐)

对于在 VS Code 等 IDE 中进行调试的场景，这是最规范、最推荐的方案。

  * **操作**: 在项目根目录的 `.vscode/launch.json` 文件中，为你的调试配置项添加 `"cwd": "${workspaceFolder}"`。

  * **示例 `launch.json` 配置**:

    ```json
    {
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Debug Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal",
                "cwd": "${workspaceFolder}" // 关键配置：将当前工作目录(CWD)设置为项目根目录
            }
        ]
    }
    ```

  * **原理**: `cwd` (Current Working Directory) 设置强制调试器在执行时，将工作目录切换到项目根目录 (`${workspaceFolder}` 指代的就是这个根目录)。Python 启动时会自动将当前工作目录加入 `sys.path`，问题因此解决。

#### 方案二：使用 `-m` 参数将脚本作为模块运行 (好习惯)

这是一种更符合 Python 工程化思想的执行方式，尤其适合在命令行中运行。

  * **操作**: 从项目根目录启动，使用 `python -m <module.path>` 命令。

  * **示例命令**:

    ```bash
    # 1. 确保在项目根目录
    cd /home/ubuntu/qy/LlamaGen

    # 2. 使用 -m 参数执行，文件路径用点(.)分隔，并去掉.py后缀
    python -m tokenizer.tokenizer_image.vq_train_sigle
    ```

  * **原理**: `-m` 参数会告诉 Python 解释器，你要运行的是一个“模块”而不是一个“脚本文件”。它会主动将**当前所在的目录**（即项目根目录）添加到 `sys.path` 的首位，从而让所有相对导入都能正确工作。

#### 方案三：在代码中动态修改 `sys.path` (临时方案)

直接在代码的开头修改模块搜索路径，适合临时调试或无法控制执行环境的场景。

  * **操作**: 在出现 `import` 错误的代码文件顶部，添加代码手动将项目根目录插入到 `sys.path`。

  * **示例代码** (添加在 `vq_train_sigle.py` 顶部):

    ```python
    import sys
    import os

    # 计算出项目根目录的绝对路径并添加到 sys.path
    project_root = os.path.dirname(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
    if project_root not in sys.path:
        sys.path.insert(0, project_root)

    # 现在可以正常导入了
    from utils.logger import create_logger
    ```

  * **原理**: 直接操作系统环境变量，将正确的根目录路径告诉 Python 解释器。简单粗暴，但具有代码侵入性，不推荐在正式项目代码中频繁使用。

### 四、总结

| 方案                   | 优点                      | 缺点                         | 适用场景                      |
| :--------------------- | :------------------------ | :--------------------------- | :---------------------------- |
| **配置 `launch.json`** | **一劳永逸，代码零侵入**  | 仅对特定IDE的调试配置生效    | **VS Code 等 IDE 开发和调试** |
| **使用 `-m` 运行**     | Python 官方推荐，通用性强 | 命令稍长，需要注意目录结构   | 命令行执行、自动化脚本、部署  |
| **修改 `sys.path`**    | 快速直接，无需配置        | 侵入代码，可移植性差，不规范 | 快速验证、临时调试            |

**核心思想**：解决 `ModuleNotFoundError` 的关键在于**确保你的项目根目录被正确地加入了 Python 的模块搜索路径 `sys.path` 中**。以上三种方案都是围绕这一核心思想展开的不同实现。

# 四、Windows config

## 键位

作为一名忠实的vim用户，拿到一台新pc，第一件事必须把CapsLock改成Esc。下面介绍使用AutoHotkey更改键位的方法(当然，现在一些键盘也支持直接更改键位，不在本文的讨论范围)

首先，安装AutoHotkey

然后，创建`CapsLockToEsc.ahk`, `C:\Users\user\AppData\Roaming\Microsoft\Windows\StartMenu\Programs\Startup\CapsLockToEsc.ahk`写入下面内容:

```
Capslock::Esc
RControl::CapsLock  

; Esc键映射：短按=Shift，长按=CapsLock
; 长按阈值设置为300毫秒

; 按下Esc时记录时间
Esc::
    EscStartTime := A_TickCount
    KeyWait, Esc  ; 等待Esc键释放
    EscDuration := A_TickCount - EscStartTime
    
    ; 判断按键时长
    if (EscDuration < 300) {
        ; 短按：发送Shift
        Send, {LShift}
    } else {
        ; 长按：检查CapsLock状态并进行相应操作
        if GetKeyState("CapsLock", "T") {
            ; 如果CapsLock已开启，则关闭
            SetCapsLockState, Off
        } else {
            ; 如果CapsLock已关闭，则开启
            SetCapsLockState, On
        }
    }
return
```

之后重启电脑，就会自动运行这个脚本, 做出以下改变：

1. 将CapsLock改成Esc
2. 右Ctrl改成CapsLock
3. Esc短按变成Shift，长按变成CapsLock（可自定义）

这样可以极大提升vim等编辑器的操作效率。


未完待续...

---

## 参考链接
- [AutoHotkey](https://www.autohotkey.com/)

---

如有更多好用的 Windows 配置技巧，欢迎补充！




