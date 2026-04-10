# Claude-Code

Anthropic 官方终端 AI 编程助手。

---

## 使用

### 继续对话

```bash
claude -c
```

### 图片输入

Mac 上使用 `ctrl + v` 粘贴图片。

---

## 配置层级

Claude Code 配置按仓库 (repository) 组织：

| 层级 | 位置 | 作用范围 |
|------|------|----------|
| 用户级 | ~/.claude/settings.json | 所有项目 |
| 项目级 | .claude/settings.json | 当前仓库（所有协作者） |
| 本地级 | .claude/settings.local.json | 当前仓库（仅你自己，git 忽略） |
| 命令行 | 启动参数 | 当前会话 |

---

## 历史记录

本地会话历史存储在：
- `~/.claude/history.jsonl`
- `~/.claude/projects/`

通过 `cleanupPeriodDays` 设置自动清理天数。
