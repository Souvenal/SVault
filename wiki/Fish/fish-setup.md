# fish-setup

Fish shell 安装与配置。

---

## 设置为默认终端

```bash
sudo apt install fish -y
chsh -s $(which fish)
```

---

## 环境变量

Fish 使用 `set` 命令设置变量，与传统 shell 的 `VARIABLE=value` 语法不同。

### 作用域

| 命令 | 作用域 |
|------|--------|
| `set` | 当前作用域 |
| `set -g` | 全局变量 |
| `set -x` | 导出为环境变量 |
| `set -gx` | 全局环境变量 |

局部变量具有函数作用域，全局变量在所有作用域可访问，环境变量可在子进程访问。

### 配置文件

在 `~/.config/fish/config.fish` 中添加：

```fish
set -gx A B
```

### 添加 PATH

```fish
fish_add_path /your/path
```

---

## 使用 bash 脚本

bass 让 Fish 能 source bash 脚本：

```bash
git clone https://gitcode.com/gh_mirrors/ba/bass.git
cd bass
make install
```

使用：

```bash
bass source /path/to/script
```

---

## Conda 集成

```shell
conda init fish
```
