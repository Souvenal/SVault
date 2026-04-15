# VSCode macOS 磁盘清理

## 可安全删除目录

| 目录 | 路径 | 作用 | 影响 |
|------|------|------|------|
| workspaceStorage | `~/Library/Application Support/Code/User/workspaceStorage/*` | 项目状态、插件缓存、历史记录 | 丢失布局/历史，不影响代码/配置 |
| CachedExtensionVSIXs | `~/Library/Application Support/Code/CachedExtensionVSIXs/*` | 插件 .vsix 安装包缓存 | 可完全删除；下次更新插件时自动重下 |
| WebStorage | `~/Library/Application Support/Code/WebStorage/*` | Copilot、Chat 等 Webview 组件的本地存储 | 可完全删除；仅丢失 Webview 临时会话 |

## 清理命令

```bash
# workspaceStorage
rm -rf ~/Library/Application\ Support/Code/User/workspaceStorage/*

# CachedExtensionVSIXs
rm -rf ~/Library/Application\ Support/Code/CachedExtensionVSIXs/*

# WebStorage
rm -rf ~/Library/Application\ Support/Code/WebStorage/*
```

## 建议

workspaceStorage 建议优先清理，可释放较多空间且无副作用。