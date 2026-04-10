# Taps

为 Homebrew 添加更多 formula 仓库来源。

---

## 基础

`brew tap` 默认假设仓库来源于 Github。

```bash
brew tap <user-name>/<repo-name>
```

## 要求

该用户的仓库中有 `homebrew-<repo-name>`，可以搜索该用户的仓库列表中以 homebrew 开头的仓库。

## 常用 Taps

```bash
brew tap homebrew/core          # 官方 core 仓库
brew tap homebrew/cask          # 官方 cask 仓库
brew tap homebrew/services      # 服务管理工具
```
