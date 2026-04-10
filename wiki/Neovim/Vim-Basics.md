# Vim 通用知识

Vim/Neovim 通用配置参数和命令。

---

## runtimepath (rtp)

`:echo &rtp` 输出：

```
~/.config/nvim,~/.local/share/nvim/lazy/lazy.nvim,~/.local/share/nvim/lazy/which-key.nvim,...
```

rtp 用于查找：
- `colors/` 配色方案
- `syntax/` 语法高亮
- `ftplugin/` 文件类型插件
- `lua/` Lua 模块（Neovim 特有）

---

## viminfo / shada

`viminfo` 是复合选项，控制历史记录、寄存器等。

### vim vs neovim 冲突

| 编辑器 | 存储格式 | 默认路径 |
|--------|----------|----------|
| vim | viminfo 文本格式 | 用户指定 |
| neovim | shada 二进制格式 | `$XDG_DATA_HOME/nvim/shada/main.shada` |

neovim 中 `&viminfo` 是 shada 的弃用别名。如果 viminfo 设置了非默认路径，vim 和 neovim 会冲突。

---

## echo vs echomsg

```vim
:echo "hello"           " 字符串
:echo 123               " 数字
:echo g:myvar           " 全局变量（需要 g: 前缀）
:echo &rtp              " 选项（需要 & 前缀）
:echo $HOME             " 环境变量（需要 $ 前缀）
:echo 1 + 2             " 表达式
```

- `echo` — 临时输出，不保存到 messages
- `echomsg` — 保存到 messages

```
:messages    " 显示消息历史
```

---

## source vs runtime

```vim
:runtime foo.vim/bar.lua   " 从 rtp 查找并加载
:runtime? bar.lua          " 显示加载路径，不执行
```

- `runtime` — 从 rtp 查找
- `source` — 从相对/绝对路径查找

---

## 寄存器

| 寄存器 | 用途 |
|--------|------|
| `""` | 无名寄存器，最近复制/删除，默认用于 `p` |
| `"0-"9` | 编号寄存器，0=最近复制，1-9=最近删除 |
| `"*` | X11 选择区（鼠标选择） |
| `"+` | 系统剪贴板（Ctrl+C/V） |

### macOS

`"*` 和 `"+` 相同。

### Linux

- `"+` — Ctrl+C/V 剪贴板
- `"*` — X11 鼠标选择

### 使用系统剪贴板

```vim
" vimrc
" unnamed -> *, unnamedplus -> +
set clipboard^=unnamed,unnamedplus
```

---

## See Also

- [[Lua-Architecture]] — Neovim Lua 配置
- [[Windows]] — Windows 平台剪贴板配置
