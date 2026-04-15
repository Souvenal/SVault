# lldb-dap：LLDB 的 Debug Adapter Protocol 实现

lldb-dap 是 LLDB 调试器对 DAP（Debug Adapter Protocol）的实现，用于在 IDE/编辑器（如 VS Code、Neovim）与 LLDB 之间建立标准化通信桥梁。前身是 lldb-vscode，2023 年 10 月重命名为 lldb-dap。

---

## 核心概念

- **lldb-dap** 是中间翻译层：把 IDE 的通用 DAP 请求翻译成 lldb 的 SB API 调用，再把结果转回 DAP 格式
- **DAP 不是调试器**，是调试操作的标准化通信协议
- **IDE 接入调试 = 实现 DAP 客户端**：不用管底层是 lldb 还是 gdb，统一交互即可
- 核心类比：**lldb-dap : lldb = clangd : clang**

---

## 时间线：从 lldb-vscode 到 lldb-dap

### lldb-vscode 阶段（前身）

| 时间 | 事件 |
|------|------|
| 2018 年 2 月 | Adrian Prantl 在 lldb-dev 邮件列表宣布 lldb-vscode，基于 LLDB SB API 实现 DAP |
| 2018 年 8 月 17 日 | 提交 r339911 合并到 LLVM 主分支 |
| 2018 年 12 月 | 随 LLVM 8.0.0 发布首个稳定版本 |

### 重命名为 lldb-dap 阶段

| 时间 | 事件 |
|------|------|
| 2023 年 10 月 13 日 | Jonas Devlieghere 发起 RFC，提议将 lldb-vscode 重命名为 lldb-dap |
| 2023 年 10 月下旬 | PR #69264 合并，完成全面重命名 |
| 2024 年 3 月 | 随 LLVM 18.0.0 发布首个 lldb-dap 稳定版本 |

---

## 重命名原因

1. **名称与功能不匹配**：lldb-vscode 实际被用于 Emacs、Vim/Neovim、CLion、Qt Creator 等多种 IDE，远超 VS Code 范畴，名称导致用户误解
2. **社区共识与技术趋势**：DAP 已成为调试标准化的事实标准，重命名是 LLDB 社区对 DAP 通用理念的全面拥抱
3. **不影响现有 VS Code 用户**：保留 VS Code 所需的 manifest 文件与配置选项，原有扩展可无缝切换

---

## DAP 工作流程

lldb-dap 作为 IDE 与 lldb 之间的桥梁，典型调试会话流程：

```
IDE (DAP Client)  ←— JSON-RPC —→  lldb-dap (DAP Server)  ←— SB API —→  lldb
```

1. **启动/附加**：IDE 发送 `launch`/`attach` 请求 → lldb-dap 调用 `SBTarget::Launch()`/`Attach()`
2. **设置断点**：IDE 发送 `setBreakpoints` → lldb-dap 调用 `SBTarget::BreakpointCreateByName()`
3. **继续执行**：IDE 发送 `continue` → lldb-dap 调用 `SBProcess::Continue()`
4. **断点命中**：lldb 检测到断点 → lldb-dap 发送 `stopped` 事件（`reason: "breakpoint"`）→ IDE 高亮代码行、刷新变量面板
5. **步进操作**：IDE 发送 `stepIn`/`stepOver`/`stepOut` → lldb-dap 调用 `thread.StepIn()` 等
6. **查看变量**：IDE 发送 `variables` 请求 → lldb-dap 调用 `frame.GetVariables()`

---

## DAP 与 LSP 的关系

DAP 就是**调试领域的 LSP**，两者是同一设计思想、同一发明者（微软）、同一协议格式：

| 维度 | LSP（语言服务） | DAP（调试服务） |
|------|----------------|----------------|
| 统一内容 | 补全、定义、诊断、引用、重命名 | 断点、步进、堆栈、变量、watch |
| 实例 | clangd（C/C++）、rust-analyzer（Rust） | lldb-dap（LLDB）、gdb-dap（GDB） |
| 异步通知 | `publishDiagnostics`（少量） | `stopped`、`exited`、`breakpoint`（频繁） |
| 核心模式 | 请求/响应为主 | 请求/响应 + 大量异步事件 |

**关键区别**：LSP 面向静态代码，以 request/response 为主；DAP 面向运行时进程，天生高度异步、事件驱动——断点命中、程序跑飞、异常抛出等都是调试器主动推送给 IDE 的事件。

**终极类比**：
- **DAP = 调试版 LSP**
- **lldb-dap = 调试领域的 clangd**

对 LSP 的所有理解可以无缝迁移到 DAP 上。

---

## 参考

- [RFC: Rename lldb-vscode to lldb-dap](https://discourse.llvm.org/t/rfc-rename-lldb-vscode-to-lldb-dap/)（2023-10-13）
- [PR #69264: Rename lldb-vscode to lldb-dap](https://github.com/llvm/llvm-project/pull/69264)
