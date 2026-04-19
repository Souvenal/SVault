# LLM-Wiki Operation Log

---

## Operations Log

## [2026-04-20] MIGRATE | windows-proxy-settings.md | 5 pages created

**Created**:
- `wiki/Protocol/WPAD.md` — 网络代理自动发现协议（DHCP/DNS 查询、安全风险）
- `wiki/Protocol/PAC.md` — 代理自动配置脚本（FindProxyForURL 规范、跨平台支持）
- `wiki/Windows-Use/代理设置.md` — Windows 三种代理模式对比、PAC 脚本配置、环境变量代理
- `wiki/Clash/系统代理模式.md` — 系统代理开关原理、PAC 模式 vs 全局模式、TUN 模式
- `wiki/Network/常见IP段.md` — 回环地址、RFC 1918 私有地址、链路本地地址及用途

**Cross-links**:
- WPAD.md ↔ PAC.md
- PAC.md ↔ Windows-Use/代理设置.md
- PAC.md ↔ Clash/系统代理模式.md
- PAC.md ↔ Network/常见IP段.md
- Windows-Use/代理设置.md ↔ Clash/系统代理模式.md
- Windows-Use/代理设置.md ↔ Network/常见IP段.md

**Topic Logs**: [[Protocol/_log]], [[Windows-Use/_log]], [[Clash/_log]], [[Network/_log]]

---

## [2026-04-16] MIGRATE | 环境变量的坑.md | 1 page created

**Created**:
- `wiki/Windows-Use/环境变量继承.md` — 子进程继承父进程环境变量快照，长驻程序需重启才能刷新

**Cross-links**:
- 环境变量继承.md ↔ PowerShell/mklink.md

**Topic Log**: [[Windows-Use/_log]]

---

## [2026-04-16] QUERY | 大代码库开发防造轮子调研 | 1 page created

**Created**:
- `wiki/Agent-Memory/大代码库开发防造轮子.md` — AI Agent 避免造轮子的五大策略与用户级规则模板

**Cross-links**:
- 大代码库开发防造轮子.md ↔ 记忆功能对比.md
- 大代码库开发防造轮子.md ↔ 用户级配置指南.md
- 大代码库开发防造轮子.md ↔ Claude-Code/Claude-Code.md

**Topic Log**: [[Agent-Memory/_log]]

---

## [2026-04-16] QUERY | Agent 记忆功能调研 | 2 pages created

**Created**:
- `wiki/Agent-Memory/记忆功能对比.md` — 六款工具记忆功能横向对比分析
- `wiki/Agent-Memory/用户级配置指南.md` — 跨项目个人偏好注入实践

**Cross-links**:
- 记忆功能对比.md ↔ Claude-Code/Claude-Code.md
- 记忆功能对比.md ↔ OpenCode/OpenCode.md
- 用户级配置指南.md ↔ 记忆功能对比.md

**Topic Log**: [[Agent-Memory/_log]]

---

## [2026-04-16] MIGRATE | agent-browser.md | 2 pages created

**Created**:
- `wiki/Agent-Browser/Agent-Browser.md` — 工具概述、架构、Snapshot+Refs 核心工作流、AI 集成、方案对比
- `wiki/Agent-Browser/命令参考.md` — 50+ 命令分类参考

**Cross-links**:
- Agent-Browser.md ↔ Claude-Code/Claude-Code.md

**Topic Log**: [[Agent-Browser/_log]]

---

## [2026-04-15] COMPILE | 豆包对话 lldb-dap | 1 page created

**Created**:
- `wiki/LLDB/lldb-dap.md` — LLDB 的 DAP 实现：从 lldb-vscode 到 lldb-dap 演进、DAP 工作流程、DAP 与 LSP 关系

**Topic Log**: [[LLDB/_log]]

---

## [2026-04-14] MIGRATE | git-crlf-note.md | 1 page migrated

**Created**:
- `wiki/Git/CRLF-换行符配置.md` — core.autocrlf 三种模式与跨平台同步策略

**Topic Log**: [[Git/_log]]

---

## [2026-04-14] MIGRATE | git-crlf-note.md | 1 page migrated

**Created**:
- `wiki/Git/CRLF-换行符配置.md` — core.autocrlf 三种模式与跨平台同步策略

**Topic Log**: [[Git/_log]]

---

## [2026-04-14] CREATE | Character-Encoding topic | 1 page created

**Created**:
- `wiki/Character-Encoding/换行符.md` — CR、LF、CRLF 换行符定义与系统差异（从 Git/CRLF-换行符配置.md 拆出）

**Updated**:
- `wiki/Git/CRLF-换行符配置.md` — 移除换行符基础概念，改为链接到 Character-Encoding

**Cross-links**:
- CRLF-换行符配置.md ↔ 换行符.md

**Topic Logs**: [[Character-Encoding/_log]], [[Git/_log]]

---

## [2026-04-11] MIGRATE | ISA.md | 1 page migrated

**Created**:
- `wiki/ISA/PAUSE.md` — spin lock 中的 PAUSE 指令

**Topic Log**: [[ISA/_log]]

---

## [2026-04-11] MIGRATE | System/Android/ | 5 pages migrated

**Created**:
- `wiki/Android-Use/APK-安装.md` — ADB install 安装与覆盖安装
- `wiki/Android-Use/APK-信息查看.md` — Android Studio Analyze APK 分析
- `wiki/Android-Use/Property-Service.md` — setprop/getprop 系统属性管理
- `wiki/Android-Use/Settings-命令对比.md` — settings 与 setprop 核心区别
- `wiki/Android-Dev/Android-Gradle.md` — Android Gradle 构建系统简介

**Cross-links**:
- APK-安装.md ↔ APK-信息查看.md
- Property-Service.md ↔ Settings-命令对比.md

**Topic Logs**: [[Android-Use/_log]], [[Android-Dev/_log]]

---

## [2026-04-11] MIGRATE | Linux/ | 6 pages migrated

**Created**:
- `wiki/Linux-Use/Linux-常用命令.md` — CPU、Disk、Network、sudo、chown 等常用命令
- `wiki/Linux-Use/XDG-目录规范.md` — XDG Base Directory 规范
- `wiki/Linux-Use/systemd-系统管理.md` — systemd 系统和服务管理器
- `wiki/Linux-Use/WSL-使用笔记.md` — Windows Subsystem for Linux 配置
- `wiki/Linux-Dev/SELinux.md` — Security-Enhanced Linux 安全模块
- `wiki/Linux-Dev/动态链接库.md` — ldconfig 动态链接库管理

**Cross-links**:
- Linux-常用命令.md ↔ 动态链接库.md
- XDG-目录规范.md ↔ Linux-常用命令.md
- systemd-系统管理.md ↔ Linux-常用命令.md
- WSL-使用笔记.md ↔ systemd-系统管理.md
- SELinux.md ↔ 动态链接库.md

**Topic Logs**: [[Linux-Use/_log]], [[Linux-Dev/_log]]

---

## [2026-04-11] CREATE | System topic framework | 9 topics created

**Created topics**:
- `wiki/Android-Dev/` — Android 平台开发
- `wiki/Android-Use/` — Android 系统使用
- `wiki/Linux-Dev/` — Linux 平台开发
- `wiki/Linux-Use/` — Linux 系统使用
- `wiki/Mac-Dev/` — macOS 平台开发
- `wiki/Mac-Use/` — macOS 系统使用
- `wiki/Windows-Dev/` — Windows 平台开发
- `wiki/Windows-Use/` — Windows 系统使用

**Topic Logs**: [[Android-Dev/_log]], [[Android-Use/_log]], [[Linux-Dev/_log]], [[Linux-Use/_log]], [[Mac-Dev/_log]], [[Mac-Use/_log]], [[Windows-Dev/_log]], [[Windows-Use/_log]]

---

## [2026-04-11] RENAME | C++/Initialization.md → 内存布局.md | merged with alignas

**Created**:
- `wiki/C++/内存布局.md` — 初始化类型、对齐控制 alignas

**Deleted**:
- `wiki/C++/Initialization.md` — 已合并

**Topic Log**: [[C++/_log]]

---

## [2026-04-11] MIGRATE | Obsidian/Archive/Render/Engine/Unity | 3 pages migrated

**Created**:
- `wiki/Unity/GfxDevice.md` — 底层渲染架构：GfxDeviceXXX 层次结构、资源管理机制
- `wiki/Unity/Compute-Shader.md` — Compute Shader：HLSL 入口声明、C# 调用、资源绑定
- `wiki/Unity/Graphics-API-Fallback.md` — 图形 API 回退：Vulkan 校验层导致的 OpenGL ES 回退

**Topic Log**: [[Unity/_log]]

---

## [2026-04-11] MIGRATE | PolySpatial.md | 1 page migrated

**Created**:
- `wiki/Unity/PolySpatial.md` — Vision Pro 混合现实：环境配置、Play to Device、功能模块一览

**Topic Log**: [[Unity/_log]]

---

## [2026-04-11] MIGRATE | Vulkan/ | 9 pages migrated

**Created**:
- `wiki/Vulkan/Depth-Testing.md` — 深度测试不写入的 8 种场景
- `wiki/Vulkan/Rendering-Pipeline.md` — 渲染管线总览与阶段依赖
- `wiki/Vulkan/Fixed-Function-Stages.md` — Input Assembler、Viewport、Rasterizer、Blending
- `wiki/Vulkan/Render-Pass.md` — Attachment、Subpass、Dynamic Rendering
- `wiki/Vulkan/Memory-Management.md` — VMA、Aliasing、Host Coherent
- `wiki/Vulkan/Swapchain.md` — Swap Chain 创建与重建
- `wiki/Vulkan/Shader-Compilation.md` — libshaderc、SPIR-V 编译
- `wiki/Vulkan/Texture-Sampler.md` — Mipmap、MSAA、Comparison Function
- `wiki/Vulkan/Synchronization.md` — Semaphore、Stage、Fence 同步原语

**Topic Log**: [[Vulkan/_log]]

---

## [2026-04-11] MIGRATE | Vulkan/Depth-Testing + Fixed-Function-Stages → Rendering-Pipeline | 3 pages migrated

**Created**:
- `wiki/Rendering-Pipeline/Depth-Testing.md` — 深度测试不写入的渲染场景（透明物体、粒子、体积渲染）
- `wiki/Rendering-Pipeline/Fixed-Function-Stages.md` — 固定功能阶段概念（IA、Viewport、Rasterizer、Blending、Tessellation、Geometry Shader）
- `wiki/Rendering-Pipeline/Rendering-Pipeline.md` — 管线阶段总览与坐标系统

**Topic Log**: [[Rendering-Pipeline/_log]]

---

## [2026-04-11] MIGRATE | Obsidian/Archive/Render/Vulkan | 7 pages migrated

**Created**:
- `wiki/Vulkan/Descriptor.md` — Descriptor Set 生命周期与 Bindless
- `wiki/Vulkan/Ray-Tracing.md` — 加速结构与 Shader Binding Table
- `wiki/Vulkan/Extension.md` — Instance/Device 扩展
- `wiki/Vulkan/ICD.md` — ICD 架构
- `wiki/Vulkan/Android-Development.md` — Android Vulkan 开发
- `wiki/Vulkan/Validation.md` — 校验层错误处理
- `wiki/Vulkan/Pitfalls.md` — 踩坑记录

**Topic Log**: [[Vulkan/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Coding/Languages/Shading-Languages | 5 pages created

**Created**:
- `wiki/Shader-Languages/Thread-Variables.md` — GLSL/HLSL/Slang 线程变量对比表
- `wiki/Shader-Languages/Matrix-Layout.md` — row/column major 矩阵布局、跨平台坑点
- `wiki/SPIR-V/Compilers.md` — HLSLcc、D3DCompiler、DXC 编译器对比
- `wiki/Shader-Layout/Memory-Layouts.md` — std140/std430/scalar 内存布局详解
- `wiki/Ray-Tracing/Inline-Ray-Tracing.md` — Inline Ray Tracing、RayQuery 状态机

**Topic Logs**: [[Shader-Languages/_log]], [[SPIR-V/_log]], [[Shader-Layout/_log]], [[Ray-Tracing/_log]]

---

## [2026-04-10] MIGRATE | pytorch.md | 1 page created

**Created**:
- `wiki/PyTorch/CUDA-Compatibility.md` — RTX 5060 Ti Blackwell 架构 sm_120 CUDA 兼容性

**Topic Log**: [[PyTorch/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Coding/Languages/LaTeX | 7 pages created

**Created**:
- `wiki/LaTeX/LaTeX-架构.md` — 三层架构：引擎、格式、发行版
- `wiki/LaTeX/LaTeX-引擎.md` — pdfLaTeX、XeTeX、 LuaTeX 对比
- `wiki/LaTeX/BibTeX.md` — 参考文献处理工具
- `wiki/LaTeX/xeCJK.md` — 中文支持宏包对比
- `wiki/LaTeX/LaTeX-宏包.md` — .sty 宏包文件
- `wiki/LaTeX/LaTeX-文档类.md` — .cls 文档类文件
- `wiki/LaTeX/LaTeX-VSCode.md` — VSCode LaTeX Workshop 配置

**Topic Logs**: [[LaTeX/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Data/file-format.md | 3 pages created

**Created**:
- `wiki/JSON/JSON-JSONL.md` — JSON Lines 格式与 $schema 声明机制
- `wiki/VSCode/Builtins.md` — JSON Schema 内置支持
- `wiki/VSCode/Extensions.md` — AsciiDoc 扩展

**Topic Logs**: [[JSON/_log]], [[VSCode/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Applications/remote-desktop/ | 1 page created

**Created**:
- `wiki/Remote-Desktop/远程桌面方案.md` — Sunshine/Moonlight 自建方案与 UU 远程商业方案

**Topic Log**: [[Remote-Desktop/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Math/Math.md | 2 pages created

**Created**:
- `wiki/Linear-Algebra/t-SVD分解.md` — Kronecker 积的 10 个性质
- `wiki/Differential-Geometry/参数化曲线.md` — 参数化曲线定义、曲线等价、正则曲线

**Topic Logs**: [[Linear-Algebra/_log]], [[Differential-Geometry/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Applications/shell | 3 pages created

**Created**:
- `wiki/Bash/bash-profile-vs-bashrc.md` — .bash_profile 与 .bashrc 的执行时机、职责分工
- `wiki/Fish/fish-setup.md` — 安装、环境变量配置、bash 脚本兼容、conda 集成
- `wiki/PowerShell/mklink.md` — 在 PowerShell 中使用 mklink 创建软链接

**Topic Logs**: [[Bash/_log]], [[Fish/_log]], [[PowerShell/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Applications/nix | 1 page created

**Created**:
- `wiki/Nix/Nix.md` — 安装入门、nix-shell 临时环境、垃圾清理

**Topic Log**: [[Nix/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Coding/Languages/Nix | 1 page created

**Created**:
- `wiki/Nix/Nix-language.md` — Nix 语言：表达式、函数、惰性求值、非纯函数

**Topic Log**: [[Nix/_log]]

---

## [2026-04-10] MIGRATE | chezmoi.md | 1 page created

**Created**:
- `wiki/chezmoi/chezmoi.md` — 跨机器 dotfiles 同步工具

**Topic Log**: [[chezmoi/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Applications/agents | 3 pages created

**Created**:
- `wiki/OpenCode/OpenCode.md` — 安装、API 配置、键位绑定
- `wiki/Claude-Code/Claude-Code.md` — 使用、配置、历史记录
- `wiki/Claude-Code/Login.md` — 首次登录绕过方法

**Topic Logs**: [[OpenCode/_log]], [[Claude-Code/_log]]

---

## [2026-04-10] COMPILE | Session.md | 2 pages created

**Created**:
- `wiki/Bitwarden/CLI-工作流程.md` — Bitwarden CLI 三层架构与命令流程
- `wiki/Bitwarden/会话管理.md` — BW_SESSION 临时访问令牌机制

**Topic Log**: [[Bitwarden/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Applications/git/ | 3 pages created

**Created**:
- `wiki/Git/Git-基础操作.md` — 通用配置、fetch/switch/branch、checkout、diff、reflog
- `wiki/Git/Git-Worktree.md` — 多工作目录管理
- `wiki/Git/Git-技巧.md` — file modes、多身份配置

**Topic Log**: [[Git/_log]]

---

## [2025-04-10] MIGRATE | Obsidian/Archive/Applications/node | 4 pages created

**Created**:
- `wiki/Nodejs/Nodejs.md` — JavaScript 运行时、调用非 JS 程序
- `wiki/Nodejs/npm.md` — Node Package Manager
- `wiki/Nodejs/npx.md` — npm package runner
- `wiki/Nodejs/nvm.md` — Node Version Manager

**Topic Log**: [[Nodejs/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Applications/docker | 2 pages created

**Created**:
- `wiki/Docker/核心概念.md` — Image、Container、Dockerfile、Repository 四大核心实体
- `wiki/Docker/常用命令.md` — pull、build、run、compose 等日常操作命令

**Topic Log**: [[Docker/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Applications/editors | 5 pages created

**Created**:
- `wiki/VSCode/Key-Bindings.md` — 按键优先级机制
- `wiki/Neovim/Lua-Architecture.md` — Lua 配置架构、模块系统
- `wiki/Neovim/Vim-Basics.md` — Vim 通用知识
- `wiki/Neovim/Plugins.md` — 插件列表
- `wiki/Neovim/Windows.md` — Windows 平台配置

**Topic Logs**: [[VSCode/_log]], [[Neovim/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Applications/gpg | 1 page created

**Created**:
- `wiki/GPG/pinentry.md` — GPG 密码输入程序：工作流程、平台差异、实现对比

**Topic Log**: [[GPG/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Coding/AI/Code-Agent.md | 1 page created

**Created**:
- `wiki/LiteLLM/LiteLLM.md` — 统一调用 100+ LLM API 的 Python SDK 和代理服务器

**Topic Log**: [[LiteLLM/_log]]

---

## [2026-04-10] UPDATE | Git-GPG签名 | 1 page created

**Created**:
- `wiki/Git/Git-GPG签名.md` — 使用 GPG 对提交签名、GitHub 配置

**Cross-links**: Git/Git-GPG签名.md ↔ GPG/pinentry.md

**Topic Log**: [[Git/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Coding/Languages/C++ | 7 pages created

**Created**:
- `wiki/C++/STL-Addressof.md` — std::addressof 获取真实地址
- `wiki/C++/Initialization.md` — C++ 三种初始化类型
- `wiki/C++/Const-Overload.md` — const 修饰符函数重载规则
- `wiki/C++/Tag-Dispatch.md` — 编译期 tag dispatch 技术

- `wiki/cmake/Basics.md` — 编译器、构建类型、生成器
- `wiki/xmake/Commands.md` — xmake 命令

**Topic Logs**: [[C++/_log]], [[cmake/_log]], [[xmake/_log]]

---

## [2026-04-10] MIGRATE | dotnet.md | 1 page created

**Created**:
- `wiki/Dotnet/IDisposable.md` — 显式释放非托管资源的接口

**Topic Log**: [[Dotnet/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Coding/Languages/go/text-template.md | 1 page created

**Created**:
- `wiki/Go/text-template.md` — text/template 包 quote 函数用法

**Topic Log**: [[Go/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Applications/UE5/ | 5 pages created

**Created**:
- `wiki/UE5/线程模型.md` — Game Thread 与 Render Thread 双线程架构
- `wiki/UE5/框架类.md` — UBlueprintFunctionLibrary 等基础框架类
- `wiki/UE5/常见错误.md` — VisualStudioTools 等常见错误解决方案
- `wiki/UE5/RDG-事件作用域.md` — RDG_EVENT_SCOPE GPU 调试标记
- `wiki/UE5/FSceneViewExtensionBase.md` — 渲染流程劫持、View 系列回调

**Topic Log**: [[UE5/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Coding/Languages/Lua | 1 page created

**Created**:
- `wiki/Lua/Lua-Package.md` — require、package.loaders、package.path 模块加载机制

**Topic Log**: [[Lua/_log]]

---

## [2026-04-10] MIGRATE | C++开发踩坑.md | 2 pages created

**Created**:
- `wiki/C++/移动语义.md` — 析构函数中指针释放的移动语义处理
- `wiki/vcpkg/命令.md` — vcpkg x-update-baseline 命令

**Topic Logs**: [[C++/_log]], [[vcpkg/_log]]

---

## [2026-04-10] MIGRATE | Obsidian/Archive/Network/Protocol | 2 pages migrated

**Created**:
- `wiki/Protocol/TLS.md` — TLS 握手流程、SNI 扩展
- `wiki/Web/CORS.md` — 跨域资源共享，浏览器安全机制

**Topic Logs**: [[Protocol/_log]], [[Web/_log]]

---

## [2026-04-10] MIGRATE | Software/Testing.md | 1 pages migrated

**Created**:
- `wiki/Software-Testing/符号执行.md` — 符号执行技术与 KLEE 工具介绍

**Topic Log**: [[Software-Testing/_log]]

---

## [2026-04-12] COMPILE | Xcode Simulator Ghost Runtime | 1 page created

**Created**:
- `wiki/Mac-Use/Xcode-Simulator-Ghost-Runtime.md` — Xcode 15.3+ 幽灵运行时删除问题

**Topic Log**: [[Mac-Use/_log]]

---

## [2026-04-12] COMPILE | VS Code 清理笔记 | 1 page created

**Created**:
- `wiki/VSCode/Cleanup.md` — macOS 磁盘清理

**Topic Log**: [[VSCode/_log]]

---

## [2026-04-11] MIGRATE | Obsidian/Archive/System/Mac.md | 3 pages migrated

**Created**:
- `wiki/Mac-Use/Mac-启动项管理.md` — launchctl、LaunchAgents、LaunchDaemons 启动项管理
- `wiki/Mac-Use/Mole.md` — macOS 磁盘清理工具
- `wiki/Homebrew/Homebrew.md` — 概览页（后续拆分为 4 页）

**Topic Log**: [[Mac-Use/_log]], [[Homebrew/_log]]

---

## [2026-04-11] MIGRATE | Obsidian/Archive/System/Windows/ | 5 pages migrated

**Created**:
- `wiki/Windows-Dev/Windows-rc-资源文件.md` — VERSIONINFO 资源文件结构与字段说明
- `wiki/Windows-Use/AppData-目录结构.md` — AppData 三种目录的数据同步策略与权限级别
- `wiki/Windows-Use/Windows-开始菜单.md` — 开始菜单快捷方式路径与快速访问
- `wiki/Windows-Use/Windows-Defender-排除项.md` — Windows Defender 添加排除项步骤
- `wiki/Windows-Use/Alt-Tab-输入法.md` — Alt+Tab 切换时输入法自动切换问题的解决方案

**Topic Log**: [[Windows-Dev/_log]], [[Windows-Use/_log]]

---

## [2026-04-11] SPLIT | Homebrew.md → 4 pages | 拆分内容

**Created**:
- `wiki/Homebrew/Homebrew.md` — 概览（入口）
- `wiki/Homebrew/Formula-And-Cask.md` — Formula 与 Cask 两种包类型
- `wiki/Homebrew/Keg-Only.md` — 自带软件冲突处理
- `wiki/Homebrew/Taps.md` — 扩展仓库来源

---

## Per-Topic Logs

- [[Agent-Memory/_log]] — Agent Memory 记忆功能调研日志
- [[Homebrew/_log]] — Homebrew 包管理工具日志
- [[LLDB/_log]] — LLDB 调试器操作日志
- [[Mac-Use/_log]] — macOS 系统使用操作日志
- [[Software-Testing/_log]] — Software Testing operation log
- [[C++/_log]] — C++ 语言操作日志
- [[cmake/_log]] — CMake 构建系统日志
- [[xmake/_log]] — xmake 构建工具日志
- [[Bash/_log]] — Bash shell 配置日志
- [[Remote-Desktop/_log]] — 远程桌面方案日志
- [[Bitwarden/_log]] — Bitwarden CLI 使用笔记
- [[Claude-Code/_log]] — Claude Code CLI 操作日志
- [[Docker/_log]] — Docker 容器化操作日志
- [[Fish/_log]] — Fish shell 配置日志
- [[Git/_log]] — Git 版本控制操作日志
- [[GPG/_log]] — GnuPG 加密签名日志
- [[Neovim/_log]] — Neovim 编辑器操作日志
- [[Network/_log]] — 网络基础知识操作日志
- [[Nix/_log]] — Nix 包管理器操作日志
- [[Nodejs/_log]] — Node.js 生态操作日志
- [[OpenCode/_log]] — OpenCode CLI 操作日志
- [[PowerShell/_log]] — PowerShell 配置日志
- [[Protocol/_log]] — 网络协议与通信协议日志
- [[VSCode/_log]] — VSCode 编辑器操作日志
- [[Web/_log]] — Web 相关技术日志
- [[chezmoi/_log]] — chezmoi dotfiles 同步日志
- [[Clash/_log]] — Clash 代理工具操作日志
- [[Differential-Geometry/_log]] — 微分几何操作日志
- [[Linear-Algebra/_log]] — 线性代数操作日志
- [[Dotnet/_log]] — .NET 平台操作日志
- [[Go/_log]] — Go 语言操作日志
- [[LiteLLM/_log]] — LiteLLM 操作日志
- [[JSON/_log]] — JSON 数据格式操作日志
- [[LaTeX/_log]] — LaTeX 排版系统操作日志
- [[Lua/_log]] — Lua 语言操作日志
- [[PyTorch/_log]] — PyTorch 深度学习框架日志
- [[Ray-Tracing/_log]] — Ray-Tracing 光线追踪操作日志
- [[Shader-Languages/_log]] — Shader-Languages 着色器语言操作日志
- [[Shader-Layout/_log]] — Shader-Layout 内存布局操作日志
- [[SPIR-V/_log]] — SPIR-V 编译器工具链日志
- [[UE5/_log]] — Unreal Engine 5 操作日志
- [[Unity/_log]] — Unity 游戏引擎操作日志
- [[Vulkan/_log]] — Vulkan 图形 API 操作日志
- [[vcpkg/_log]] — vcpkg C++ 包管理工具日志

---

*Log format: `## [YYYY-MM-DD] ACTION | Source/Description | Wiki pages created/modified`*
