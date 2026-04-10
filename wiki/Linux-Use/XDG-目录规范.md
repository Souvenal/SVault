# XDG Base Directory Specification

freedesktop.org 制定的规范，定义 Linux/Unix 应用程序文件存放位置。

## 核心变量

| 变量 | 默认值 | 用途 |
|------|--------|------|
| XDG_CONFIG_HOME | ~/.config | 用户配置文件 |
| XDG_DATA_HOME | ~/.local/share | 用户数据文件（插件、日志等） |
| XDG_CACHE_HOME | ~/.cache | 非持久性缓存 |
| XDG_STATE_HOME | ~/.local/state | 状态/历史数据 |
| XDG_RUNTIME_DIR | /run/user/$UID | 运行时临时文件 |

## 规范背景

**以前的问题：** 每个应用都在 home 目录下放隐藏文件：

```
~/.vimrc
~/.bashrc
~/.gitconfig
~/.npmrc
~/.zsh_history
...
```

Home 目录变成垃圾堆，备份时难以区分配置和重要数据。

**现在的做法：** 分类存放

```
~/.config/          # 所有配置集中
    nvim/
    git/

~/.local/share/     # 数据文件
    nvim/lazy/      # 插件
    applications/   # .desktop 文件

~/.cache/           # 缓存，可随时删除
    nvim/
    npm/
```

## 系统级配置

```
/etc/xdg/           # 系统默认配置
/usr/share/         # 系统数据文件
```

搜索优先级：XDG_CONFIG_HOME > XDG_CONFIG_DIRS

## 使用示例

```bash
# 设置自定义位置
export XDG_CONFIG_HOME="$HOME/.dotfiles/config"
export XDG_CACHE_HOME="$HOME/.tmp/cache"

# Neovim 会尊重这些变量
# 配置从 ~/.dotfiles/config/nvim 读取
# 缓存放到 ~/.tmp/cache/nvim
```

## 兼容性

- **遵循规范**：Neovim、Git（部分）、VS Code、Docker、现代工具
- **未遵循**：传统工具 Vim（~/.vimrc）、SSH（~/.ssh/）

通过符号链接兼容老工具：

```bash
ln -s ~/.config/nvim/init.vim ~/.vimrc
```

## 相关链接

- [[Linux-常用命令]] — chown 等文件操作命令
