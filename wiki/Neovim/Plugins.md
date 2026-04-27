# Neovim 插件

Neovim 原先本身没有内置插件管理，需要先安装插件管理器（如 lazy.nvim）。

PS：从 0.12.0 版本（2026.3.31 正式发布）开始，neovim 引入了内置插件管理器 `vim.pack`。

---

## LazyVim 插件管理

在 `~/.config/nvim/lua/plugins/` 目录下添加的所有 .lua 文件都会被自动加载，无需手动 require，配置形式：

```lua
return {
    { "ellisonleao/gruvbox.nvim" }, -- 基础插件声明
    {
        "nvim-telescope/telescope.nvim", -- 带配置的插件
        opts = {
            defaults = {
                layout_strategy = "horizontal"
            }
        }
    }
}
```

单个文件可以包含多个插件配置，

## 常用插件

### which-key

按键提示插件，显示可用快捷键。

### telescope

模糊查找器，用于文件搜索、grep 等。

### leap.nvim

快速跳转插件。

```
按 s → 输入搜索字符 → 高亮匹配 → 显示字母标签 → 输入标签跳转
```

### mini.animate

光标跳转动画效果。

---

## See Also

- [[Lua-Architecture]] — 插件管理器原理
