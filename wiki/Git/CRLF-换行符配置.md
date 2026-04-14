# CRLF 换行符配置

Git 跨平台换行符处理：`core.autocrlf` 三种模式与多设备同步策略。

---

## core.autocrlf 配置

换行符基础概念见 [[Character-Encoding/换行符]]。

| 值 | 提交时 | 检出时 | 适合谁 |
|---|---|---|---|
| `true` | CRLF → LF | LF → CRLF | Windows 用户（默认值） |
| `input` | CRLF → LF | 不转换 | Linux/macOS 用户 |
| `false` | 不转换 | 不转换 | 个人仓库 / 不想管换行符 |

---

## 多设备跨平台同步

通过 GitHub 在不同系统间同步仓库时，按系统分别设置，保证仓库内统一存 LF：

| 设备 | 建议设置 | 原因 |
|------|---------|------|
| Windows | `core.autocrlf = true` | 检出自动转 CRLF，本地编辑体验好；提交时转回 LF，仓库统一 |
| macOS/Linux | `core.autocrlf = input` | 仓库保证 LF，本地本来就是 LF，无需检出转换 |

```bash
# Windows 上
git config core.autocrlf true

# macOS/Linux 上
git config core.autocrlf input
```

Windows 上设 `true` 时，`git diff` 可能出现 LF/CRLF 转换警告，这是正常现象，说明转换在正常工作。如果觉得警告烦，可以设 `false`，但部分 Windows 编辑器（如旧版记事本）可能无法正确显示 LF 换行。

---

## 相关

- [[Git-基础操作]] — 通用 Git 配置
- [[Character-Encoding/换行符]] — CR、LF、CRLF 定义与系统差异
