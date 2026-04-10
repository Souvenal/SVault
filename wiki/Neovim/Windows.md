# Neovim Windows 配置

Windows 平台下 Neovim 的特殊配置。

---

## 配置路径

Windows 配置路径不同于 macOS/Linux：

| 平台 | 配置路径 |
|------|----------|
| macOS/Linux | `~/.config/nvim/init.lua` |
| Windows | `AppData\Local\nvim\init.lua` |

---

## 系统剪贴板

Windows 使用系统剪贴板需要两步：

1. **配置 init.lua**

```lua
vim.opt.clipboard:append("unnamedplus")
```

2. **安装 win32yank**

win32yank 是 Windows 剪贴板工具，Neovim 依赖它与系统剪贴板交互。

---

## See Also

- [[Vim-Basics]] — 寄存器与剪贴板
