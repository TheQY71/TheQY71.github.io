+++
date = '2025-06-12T10:30:13+08:00'
draft = false
title = 'Windows的一些配置'
categories = ["配置"]
tags = ["config","windows"]
+++


# 键位

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

# 参考链接
- [AutoHotkey](https://www.autohotkey.com/)

---

如有更多好用的 Windows 配置技巧，欢迎补充！
