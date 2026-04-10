# pinentry

pinentry 是 GnuPG 的密码输入程序，由 gpg-agent 调用来获取私钥的 passphrase。

---

## 工作流程

```
git commit -S
    ↓
gpg-agent（守护进程）
    ↓ (密钥不在缓存中)
    → spawn pinentry（子进程）
    → pinentry 显示密码对话框
    → 密码通过 Assuan 协议返回给 agent
    → agent 解锁私钥并缓存
```

核心安全模型：密码永远不在 shell 环境变量中传递，而是在独立进程间通过管道通信。

---

## 平台差异

| 平台 | 典型 pinentry | 原因 |
|------|---------------|------|
| Linux 服务器 | pinentry-tty | 直接读写 /dev/tty，无 GUI |
| Linux 桌面 | pinentry-gtk/qt | X11 环境，有原生对话框 |
| macOS | pinentry-mac | 无 X11，无 TTY，但有 Keychain |

> macOS 为什么需要 pinentry-mac？
> 1. 没有 X11 — GPG 需要 native Cocoa UI
> 2. 没有虚拟 TTY — 无法用 curses 从终端读取
> 3. 有 Keychain — pinentry-mac 直接集成 macOS Keychain 缓存 passphrase（Linux 版本没有）

---

## 实现对比

| 实现 | UI 类型 | 依赖 |
|------|---------|------|
| pinentry-tty | 纯 TTY，无颜色 | 无 |
| pinentry-curses | ncurses 文本界面 | libncurses |
| pinentry-gtk-2 | GTK+2 GUI | GTK+2 |
| pinentry-qt | Qt GUI | Qt |
| pinentry-mac | Cocoa/AppKit GUI + Keychain | macOS 框架 |
| pinentry-bemenu | bemenu | Sway/wm |

---

## 相关

- [[Git/Git-GPG签名]] — Git 中配置 GPG 签名
