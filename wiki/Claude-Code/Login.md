# Login

首次登录绕过方法。

---

## 问题

最新 Claude Code 会检查登录时的 token 格式（官方 token 是 `sk-ant-xxx`），也会检查 key 和 base url。

## 解决

修改 `~/.claude.json` 中的 `hasCompletedOnboarding` 字段为 `true`。

该字段标记是否完成了 Claude Code 的初始新手引导。
