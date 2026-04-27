# autocmd

Neovim 中响应事件自动执行命令的机制。

## Lua API

现代用法通过 `vim.api.nvim_create_autocmd`：

```lua
vim.api.nvim_create_autocmd("BufWritePost", {
    pattern = "*.lua",
    callback = function(args)
        print("Saved: " .. args.file)
    end,
})
```

关键字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| `event` | string / table | 事件名，如 `"BufEnter"` 或 `{"BufEnter", "BufWinEnter"}` |
| `pattern` | string / table | 文件匹配模式，如 `"*.lua"` |
| `callback` | function / string | 回调函数或 Vimscript 命令字符串 |
| `group` | string / integer | autocmd group，用于批量管理 |
| `once` | boolean | 只执行一次 |
| `nested` | boolean | 允许嵌套触发其他 autocmd |
| `desc` | string | 描述，用于 `:autocmd` 输出 |

## 常用事件

| 事件 | 触发时机 |
|------|----------|
| `BufReadPost` | 读取文件到 buffer 后 |
| `BufWritePre` | 写入 buffer 到文件前 |
| `BufWritePost` | 写入完成后 |
| `BufEnter` | 进入 buffer 时 |
| `VimEnter` | Neovim 启动完成后 |
| `VimLeavePre` | 退出前 |
| `CursorHold` | 光标停止移动后（`updatetime` 超时） |
| `TextChanged` | 文本修改后（normal 模式） |
| `TextChangedI` | insert 模式下文本修改 |
| `FileType` | 设置 filetype 时 |

## Autocmd Group

用于组织 autocmd，支持清空、替换：

```lua
local group = vim.api.nvim_create_augroup("MyGroup", { clear = true })

vim.api.nvim_create_autocmd("BufWritePost", {
    group = group,
    pattern = "*.py",
    callback = function()
        vim.cmd("!black %")
    end,
})
```

`clear = true` 创建时清空已有命令，避免重复注册。

## 删除 Autocmd

```lua
local id = vim.api.nvim_create_autocmd("BufEnter", { callback = fn })
vim.api.nvim_del_autocmd(id)
```

## 踩坑：Ctrl+C 与 InsertLeave

Ctrl+C (`<C-c>`) 是中断命令，不会触发任何 autocmd。ESC 会正常触发 `InsertLeave`。

> 为什么 im-select 在 Ctrl+C 后不生效？
> im-select 监听 `InsertLeave` 自动切换输入法。Ctrl+C 跳过 autocmd，所以 `InsertLeave` 不会被触发。只能用 ESC 或 `:stopinsert` 退出 insert 模式。

**解决方案：**
- 改用 ESC 退出 insert 模式
- 或在 `imap <C-c> <Esc>` 中显式映射到 ESC

## 对比 Vimscript

Vimscript 写法仍可用但不推荐：

```vim
augroup MyGroup
    autocmd!
    autocmd BufWritePost *.lua lua print("saved")
augroup END
```

Lua API 优势：函数回调（非字符串拼接）、ID 管理、结构化配置。
