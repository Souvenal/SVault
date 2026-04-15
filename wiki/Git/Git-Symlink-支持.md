# Git Symlink 支持

*Git 原生支持符号链接，可实现跨平台文件共享与同步。*

---

## 核心原理

Git **原生支持符号链接（symbolic link）**。当 git 遇到 symlink 时：

- **存储时**：将 symlink 目标路径作为普通文件内容存入对象库，文件模式标记为 `120000`
- **检出时（checkout/clone）**：
  - Unix 系统（macOS/Linux）：自动还原为真实 symlink
  - Windows：取决于 Git 配置和系统权限（见下文）

这意味着 symlink 可以像普通文件一样被 `commit`、`push`、`pull`，实现多端同步。

---

## 使用方法

### 创建并提交 Symlink

```bash
# 1. 创建符号链接（使用相对路径，确保可移植性）
ln -s ../target-dir link-name

# 2. 查看状态（symlink 显示为特殊文件类型）
git status

# 3. 添加并提交
git add link-name
git commit -m "Add symlink to target-dir"
```

### 验证 Symlink

```bash
# 查看 git 如何存储 symlink
git ls-files -s link-name
# 输出示例：120000 blob abc123...	link-name
#          ^^^^^^^ 模式 120000 表示 symlink

# 查看符号链接指向
ls -la link-name
# 输出：link-name -> ../target-dir
```

---

## 跨平台行为

### macOS / Linux

原生支持，`git clone` 后 symlink 自动还原。

### Windows

Windows 默认限制非管理员创建 symlink，需要配置：

#### 方案 A：开启开发者模式（推荐）

1. 设置 → 隐私和安全性 → 开发者选项
2. 开启 **开发人员模式**
3. 确认 Git 配置：`git config --global core.symlinks true`

完成后普通终端即可创建和使用 symlink。

#### 方案 B：以管理员身份运行终端

右键 Git Bash / PowerShell → 以管理员身份运行，可临时获得 symlink 创建权限。

#### 验证 Windows Symlink

```powershell
# PowerShell 查看 symlink
Get-ChildItem | Where-Object {$_.LinkType -ne $null}

# Git Bash 查看
ls -la link-name
```

---

## 实际应用场景

### 案例：单一代码源 + 多工具兼容

问题：项目中有两处需要相同的配置文件，但工具 A 只认 `.agents/`，工具 B 只认 `.codebuddy/`。

方案：
```bash
# 维护一份实际文件
.agents/skills/
  ├── skill-1/
  └── skill-2/

# 创建符号链接
ln -s ../agents/skills .codebuddy/skills

# 提交到 git
git add .codebuddy/skills
git commit -m "Add symlink .codebuddy/skills -> .agents/skills"
```

效果：
- 只需维护 `.agents/skills/` 一份文件
- 两个工具都能正确读取
- `git push/pull` 后多端自动同步

---

## 注意事项

1. **使用相对路径**：symlink 目标应使用相对路径（如 `../target`），避免绝对路径导致的多端失效
2. **Git 配置**：Windows 用户需确认 `core.symlinks = true`
3. **权限问题**：Windows 需要开发者模式或管理员权限
4. **回退方案**：如果 Windows 环境受限，可用 git post-checkout hook 自动拷贝文件，但不够优雅

---

## 相关链接

- [[Git-技巧]] — file modes、多身份配置等实用技巧
- [[CRLF-换行符配置]] — 跨平台换行符同步策略
