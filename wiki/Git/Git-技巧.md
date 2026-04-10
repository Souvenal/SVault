# Git 技巧

file modes、多身份配置等实用技巧。

---

## File Modes

git 会跟踪文件的可执行权限。在 Unix/Linux/macOS 上 `chmod` 后，`git status` 会显示变更，提交后权限会被保存。

---

## 同机器多身份

在公司提交项目用公司邮箱和自己名字，需要全局配置；做自己项目时用项目级别配置覆盖。

```shell
git config --global user.name "Company Name"
git config --global user.email "company@example.com"

# 项目级别覆盖
cd ~/personal-project
git config user.name "Personal Name"
git config user.email "personal@example.com"
```

---
