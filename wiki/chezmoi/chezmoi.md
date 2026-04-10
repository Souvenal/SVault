# chezmoi

跨机器同步 dotfiles 的工具，使用 Git 作为后端存储。

---

## 基本工作流

**第一台机器（初始化仓库）：**

```shell
chezmoi init                              # 初始化本地仓库
chezmoi add .bashrc                       # 添加文件到管理
chezmoi edit .bashrc                      # 编辑托管版本
chezmoi diff                              # 查看变更
chezmoi -v apply                          # 应用变更

chezmoi cd                                # 进入仓库目录
git add . && git commit -m "Initial commit"
git remote add origin git@github.com:$GITHUB_USERNAME/dotfiles.git
git branch -M main && git push -u origin main
```

**第二台机器（拉取配置）：**

```shell
chezmoi init git@github.com:$GITHUB_USERNAME/dotfiles.git
```

Dry run 模式：`chezmoi -n -v apply`

---

## 机器间差异处理

每台机器在 `~/.config/chezmoi/chezmoi.toml` 中设置变量：

```toml
[data]
email = "user@example.com"
```

添加模板文件：

```shell
chezmoi add --template ~/.gitconfig
```

模板中使用变量：

```shell
[user]
email = {{ .email }}
```

查看所有可用变量：

```shell
chezmoi data
```

---

## 常用命令速查

| 命令 | 作用 |
|------|------|
| `chezmoi init` | 初始化仓库 |
| `chezmoi add <file>` | 添加文件到管理 |
| `chezmoi add --template <file>` | 添加为模板文件 |
| `chezmoi edit <file>` | 编辑托管版本 |
| `chezmoi diff` | 查看待应用变更 |
| `chezmoi apply` | 应用变更到系统 |
| `chezmoi cd` | 进入仓库目录 |
| `chezmoi data` | 查看模板变量 |
