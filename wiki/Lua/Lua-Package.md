# Lua Package 系统

Lua 模块加载与路径解析机制。

---

## require

`require` 是 Lua 的模块加载函数，返回模块提供的接口：

```lua
require("lazy").setup({ ... })
```

| 步骤                | 说明                             |
| :---------------- | :----------------------------- |
| `require("lazy")` | 加载 `lazy.nvim` 模块，返回一个表（table） |
| `.setup({ ... })` | 调用返回表中的 `setup` 函数             |
| `{ ... }`         | 传给 `setup` 的配置参数（Lua 表/字典）     |

---

## package.loaders

由 `require` 使用的加载函数表。`require` 会依次调用这些函数，直到找到可用的模块。例如 `loader_Lua` 会根据 `package.path` 中的路径查找 .lua 文件。

---

## package.path

`package.path` 是 Lua 的模块搜索路径，告诉 `require` 去哪里找文件：

- 一个字符串，包含多个路径模板，用分号 `;` 分隔
- 每个模板里的 `?` 会被替换为 `require` 的模块名

`print(package.path)` 输出类似：

```
./?.lua;./?/init.lua;/usr/share/lua/5.4/?.lua
```

这意味着 `require("foo.bar")` 会依次尝试：

1. `./foo/bar.lua`
2. `./foo/bar/init.lua`
3. `/usr/share/lua/5.4/foo/bar.lua`
