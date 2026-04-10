# Bitwarden 主题操作日志

*本主题所有操作的按时间顺序记录。*

---

## [2026-04-10] COMPILE | buffer/Obsidian/Archive/Applications/bitwarden/Session.md | 2

### 来源
`buffer/Obsidian/Archive/Applications/bitwarden/Session.md`

### 创建/修改的页面
- 创建 `CLI-工作流程.md` — 解析三层架构流程图与命令
- 创建 `会话管理.md` — BW_SESSION 令牌机制说明

### 关键洞察
- Bitwarden CLI 采用严格的分层解密架构：云端只存密文，本地只缓存密文，内存中才解密
- `bw login` 和 `bw unlock` 是两个独立的步骤，前者下载数据，后者解密数据
- BW_SESSION 是临时会话令牌，用于避免重复输入主密码

---

*日志格式: `## [YYYY-MM-DD] ACTION | <Source> | <Number of pages affected>`*
