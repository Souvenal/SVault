# Neovim Lua 架构

Neovim 内嵌 Luajit 解释器，无需单独安装 Lua 即可执行 `:lua` 命令。

---

## 配置目录结构

```
~/.config/nvim/
├── plugin/        " 启动时自动执行的脚本
├── lua/           " Lua 模块
├── ftplugin/      " 文件类型特定配置
├── colors/        " 配色方案
└── after/         " 延迟加载的脚本
```

nvim 在 `package.loaders` 中添加加载器，扫描 runtimepath 查找模块。

---

## vim 全局变量

nvim 启动 Lua 解释器时注入 `vim` 全局变量：

| 变量 | 用途 |
|------|------|
| `vim.api` | Neovim API 函数（nvim_* 的 Lua 绑定） |
| `vim.fn` | Vimscript 函数接口（`vim.fn.getcwd()` 等） |
| `vim.loop` | libuv 异步 IO（旧版本） |
| `vim.uv` | libuv 异步 IO（0.10+） |
| `vim.opt` | 选项设置接口 |
| `vim.o` | 全局选项简写 |
| `vim.g` | 全局变量 |
| `vim.b` | 缓冲区变量 |

```lua
-- 直接 require Lua 模块
require('lspconfig').setup{}

-- 访问 Neovim API
vim.api.nvim_set_keymap('n', '<leader>ff', '<cmd>Telescope find_files<cr>', {})
```

相比 Vimscript，Lua 更快、表达能力更强。

---

## 远程插件 (RPC)

Neovim 通过 msgpack-RPC 与外部进程通信，支持多语言插件：

- Python (pynvim)
- Node.js (neovim npm 包)
- Ruby、Go 等

---

## 内置功能模块

| 模块 | 用途 |
|------|------|
| `vim.lsp` | LSP 客户端 |
| `vim.treesitter` | 语法树解析 |
| `vim.ui` | 选择器/输入框 |
| `vim.diagnostic` | 诊断显示 |
| `vim.snippet` | 代码片段 |

---

## 事件系统 (autocmd)

```lua
vim.api.nvim_create_autocmd('BufWritePre', {
  pattern = '*.go',
  callback = function() vim.lsp.buf.format() end
})
```

---

## 插件管理器

社区主流方案：

- **lazy.nvim** — 目前主流，支持延迟加载
- **mini.deps** — 极简方案

这些管理器本质上是把 Git 仓库放到 runtimepath，按依赖顺序执行 Lua 脚本。

---

## See Also

- [[Vim-Basics]] — Vim 通用知识
- [[Plugins]] — 插件列表
