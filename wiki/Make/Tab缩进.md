# Tab 缩进

Makefile 中 recipe lines（规则下的命令行）必须以 Tab 字符 `\t` 开头，不能用空格。这是 Make 最经典的坑。

## missing separator

```
Makefile:22: *** missing separator.  Stop.
```

Make 靠行首是否为 Tab 来区分"命令"和其他逻辑（变量定义、条件判断等）。行首是空格就会报 `missing separator`。

### 检查方法

**Vim**：`:set list`，行首 `^I` 是 Tab，`$` 前一堆点或空格则是空格。

**VS Code**：右下角状态栏显示 `Spaces: 4` → 点击 → `Indent Using Tabs` / `Convert Indentation to Tabs`。

## VS Code 配置

`settings.json` 中针对 `makefile` 设置：

```json
"[makefile]": {
    "editor.insertSpaces": false,
    "editor.detectIndentation": false,
    "editor.tabSize": 4,
    "editor.formatOnSave": false
}
```

- `insertSpaces: false` — Tab 键输出真正的 `\t`，不是空格
- `detectIndentation: false` — 禁止 VS Code 自动检测并转成空格
- `tabSize: 4` — Tab 的视觉显示宽度，不影响 make 执行
- `formatOnSave: false` — 防止保存时格式化插件把 Tab 转成空格

关联 `.mk`、`.mak` 后缀：

```json
"files.associations": {
    "*.mk": "makefile",
    "*.mak": "makefile"
}
```

推荐安装扩展 **Makefile Tools**，自动设置正确缩进，还提供语法高亮、错误提示和一键运行。

## 为什么是 Tab？

1976 年 Stuart Feldman 写的 Make，当时 Tab vs 空格的争论还没开始。后来想改也改不动了：

- **歧义问题**：如果支持空格缩进，Make 无法区分命令缩进和普通排版空格。比如 `VAR = value` 前面的空格是命令还是变量定义？
- **向后兼容**：改变规则会导致海量旧 Makefile 报错，全球 CI/CD 流水线崩溃。

> 为什么不改默认值？
> 向后兼容性比"优雅"重要得多。改默认值会让全世界的开源项目编译脚本一夜之间崩掉。

## `.RECIPEPREFIX` 现代方案

GNU Make 3.82+（2010 年）支持自定义命令前缀符：

```makefile
.RECIPEPREFIX := >

all:
> @echo "Hello, World!"
```

默认值仍是 Tab，这个特性只能在新项目中使用，不能指望别人的 Makefile 支持。

---

相关：[[cmake/Basics]] | [[xmake/_index]] | [[VSCode/_index]]
