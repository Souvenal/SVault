# JSON & JSONL

JSON Lines 格式与 `$schema` 声明机制。

---

## JSONL 格式

JSONL（JSON Lines）文件中每行一个 JSON 对象。

**用途：**
- 存储长数据
- 天生支持流式读取

---

## $schema 声明

`$schema` 是 JSON Schema 标准的特殊关键字，用于声明 JSON 文件遵循的结构规范。

**作用：**
- **验证** — 检查 JSON 是否符合预期结构和类型
- **自动补全** — 编辑器根据 Schema 提供属性提示
- **文档** — 说明每个字段的含义和类型

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "keybinds": {
    "input_newline": "ctrl+j"
  }
}
```

`$schema` 指向远程 Schema 文件，告诉编辑器按该规范验证和提示。

**支持 $schema 的编辑器：**
- VS Code（内置）
- JetBrains 系列 IDE
- Neovim（jsonls LSP）
- Vim（coc-json 等插件）

> VS Code 中需在设置中搜索 `json.schema`，添加信任域名。
> 详见 [[../VSCode/Builtins|VSCode 内置功能]]。
