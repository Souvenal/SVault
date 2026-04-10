# Git 基础操作

通用配置、分支操作、diff、reflog 等日常工作流。

---

## 通用配置

```shell
# rebase 策略
git config --global pull.rebase true
git config --global rebase.autoStash true

# 长路径支持 (Windows)
git config --system core.longpaths true

# 查看某项设置
git config --get [--global] xxx.yyy
```

---

## fetch & switch

```shell
git fetch origin

git switch -c dev/feature origin/dev/feature
```

---

## branch 操作

**改名：**

```shell
git branch -m new-branch-name
```

**查看上游跟踪：**

```shell
git branch -vv
```

---

## checkout

`git checkout` 切换当前分支（或 commit），并将对应版本的文件写到工作目录中。

当 checkout 一个本地不存在的分支（如 dev）时，git 会自动执行：
- 创建本地 dev 分支
- 让它跟踪远程 origin/dev
- 拉取远程分支

**拉取并切换到新分支：**

```shell
git fetch origin
git checkout -b feature/new origin/feature/new
# 等价形式
git switch -c feature/new origin/feature/new
```

---

## diff

```shell
git diff commit-a commit-b [--name-only] [-- selected-path]
```

---

## reflog

查看本地 HEAD 移动历史，用于恢复误删的 commit 或分支。

```shell
git reflog -n
```

---
