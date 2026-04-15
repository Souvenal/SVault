# Agent-Browser

Vercel Labs 推出的专为 AI Agent 设计的命令行浏览器自动化工具，用 Rust 编写，比 Playwright MCP 减少 93% 上下文占用。

---

## 定位

传统浏览器自动化工具（Puppeteer、Playwright）输出完整的 JSON 或 DOM 树，对 AI Agent 上下文开销巨大。agent-browser 反其道而行：输出紧凑的可访问性树文本，每个元素附带唯一 ref，让 AI 用最少 Token 理解页面并精确操作。

## 架构

```
Rust CLI  ←→  原生守护进程（Rust）  ←→  Chrome（CDP）
                    ↑
              Fallback: Node.js Daemon（原生二进制不可用时降级）
```

- **Rust CLI**：解析命令，与守护进程通信
- **原生守护进程**：通过 CDP 直接管理 Chrome 实例，自动启动并持久运行
- **Fallback**：原生二进制不可用时降级到 Node.js 执行

> 为什么用客户端-守护进程架构？
> 守护进程在命令之间保持持久运行，避免每次命令都重新启动浏览器的开销。

## 核心创新：Snapshot + Refs

```
$ agent-browser snapshot -i

heading "Example Domain" [ref=e1]
link "More information..." [ref=e2]
```

```
$ agent-browser click @e2
```

工作流：`snapshot` 生成可访问性树 + 唯一 ref → 后续操作用 `@ref` 定位元素。

| 优势 | 说明 |
|------|------|
| 上下文高效 | 快照 200-400 Token vs 完整 DOM 3000-5000 Token |
| 确定性 | ref 指向快照中的确切元素，无歧义 |
| 速度快 | 无需重新查询 DOM |
| AI 友好 | LLM 自然解析文本输出 |

## 其他特性

- **50+ 命令**：导航、表单、截图、网络、存储管理 → [[命令参考]]
- **会话隔离**：`--session <name>` 创建独立浏览器实例，各自维护认证信息
- **跨平台**：macOS (ARM64/x64)、Linux (ARM64/x64)、Windows (x64)
- **Headed/Headless**：默认 Headless，`--headed` 切换可视化调试

## 安装

```bash
# npm（全平台）
npm install -g agent-browser

# Homebrew（macOS）
brew install agent-browser

# 下载 Chromium
agent-browser install

# Linux 系统依赖
agent-browser install --with-deps

# 无需安装直接试用
npx agent-browser open example.com
```

## 基本使用

```
打开页面 → 获取快照 → 通过 ref 交互 → 关闭
```

```bash
agent-browser open example.com       # 打开页面
agent-browser snapshot -i            # 可交互元素快照
agent-browser click @e2              # 用 ref 点击
agent-browser fill @e3 "hello@ex.com" # 填充输入框
agent-browser screenshot page.png    # 截图
agent-browser close                  # 关闭
```

## AI 编程助手集成

任何支持 Bash 命令的 AI Agent 都可使用：

- [[Claude-Code/Claude-Code|Claude Code]]
- Cursor
- GitHub Copilot / Codex
- OpenAI Codex
- Google Gemini
- opencode

### 使用示例

向 AI 发送指令："请使用 agent-browser 打开 example.org，点击登录按钮。"

```bash
npx agent-browser open example.org
npx agent-browser snapshot -i        # 获取可交互元素快照
npx agent-browser click @e5          # 根据 ref 点击登录按钮
```

### 最佳实践

- 提示 AI 明确使用 agent-browser，而非其他浏览器工具
- 先 `snapshot -i` 再操作，避免直接用复杂选择器
- 多实例用 `--session` 区分
- `snapshot -i` 只返回可交互元素，进一步减少 Token

## 与替代方案对比

### vs browser-use

| 维度 | agent-browser | browser-use |
|------|--------------|-------------|
| 类型 | CLI（Bash 调用） | Python 库（API 调用） |
| 集成方式 | 任何支持 Shell 的 AI Agent | Python 代码内调用 |
| 适用场景 | AI 编程助手 | Python 自动化脚本 |
| 上下文效率 | 高（Snapshot+Refs） | 中等 |
| 语言 | Rust | Python |

### vs Playwright MCP

| 维度 | agent-browser | Playwright MCP |
|------|--------------|----------------|
| 上下文占用 | 低（200-400 Token） | 高（3000-5000 Token） |
| 输出格式 | 紧凑文本 | 完整可访问性树 JSON |
| 元素定位 | Ref 引用 | CSS 选择器 |
| 配置 | 零配置 | 需配置 MCP Server |

## 适用场景

- AI 辅助开发：AI 编程助手直接操控浏览器
- Web 自动化测试：自动登录、填表、点击、截图
- 数据抓取：打开页面 → 快照 → 提取内容
- 表单自动填写：基于 ref 确定性操作
- 网页截图：保存视觉快照用于报告或调试

ref: https://agent-browser.dev/
