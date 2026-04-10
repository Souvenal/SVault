# Git Worktree

单目录下反复切换分支每次都要重新编译，或者每次 `git stash -a` 也很麻烦；clone 多个仓库管理起来复杂，还会出现重复的 `.git`。

`git worktree` 使用 git 内置功能关联多个工作目录，git 记录只保留在主目录中。

---

## 创建 worktree

```shell
# --track 指定跟踪远程分支
# -b 指定本地分支名
git worktree add ../my-feature -b feature --track origin/feature
```

> 不设置 `--track` 会是 detached HEAD 状态。

---

## 管理 worktree

```shell
# 查看所有 worktree
git worktree list

# 删除 worktree
git worktree remove ../my-project-another
```

---
