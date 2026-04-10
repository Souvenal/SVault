# bash-profile-vs-bashrc

`.bashrc` 和 `.bash_profile` 的核心区别在于**何时被加载**。

---

## 执行时机

| 场景 | 读取的文件 | 典型情况 |
|------|-----------|----------|
| **Login shell** | `~/.bash_profile` | SSH 远程登录、TTY 控制台登录、图形界面登录后的第一个终端 |
| **Interactive non-login shell** | `~/.bashrc` | 图形界面中打开新终端窗口、`Ctrl+Alt+T` |

---

## 职责分工

### `.bash_profile` — 一次性设置

登录时执行一次，适合放：
- 环境变量 (`export PATH`, `export EDITOR`)
- 启动代理 (`ssh-agent`, `gpg-agent`)
- 启动图形程序

### `.bashrc` — 每次开终端都执行

适合放：
- 命令别名 (`alias ll='ls -la'`)
- Shell 函数
- 提示符样式 (`PS1`)
- 自动补全配置
- 颜色主题

---

## 最佳实践

`.bash_profile` 调用 `.bashrc`，确保登录时也能获得交互式配置：

```bash
# 在 ~/.bash_profile 末尾添加
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi
```

---

## 平台差异

| 系统 | 默认行为 | 注意事项 |
|------|---------|----------|
| **Linux/WSL** | 图形终端通常是 non-login shell | 改 `.bashrc` 立即生效 |
| **macOS** | Terminal.app 默认是 login shell | 改 `.bash_profile` |

---

## 检查当前 shell 类型

```bash
# 是否是 login shell
shopt -q login_shell && echo "Login shell" || echo "Non-login shell"

# 是否是 interactive shell
[[ $- == *i* ]] && echo "Interactive" || echo "Non-interactive"
```

环境变量放 `.bash_profile`，别名和函数放 `.bashrc`，前者加载后者。
