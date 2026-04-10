# nvm (Node Version Manager)

Node.js 版本管理器。

## 核心特性

| 特性 | 说明 |
|------|------|
| 多版本安装 | 同时安装 v16、v18、v20 等多个版本 |
| 快速切换 | 全局默认版本 + 项目局部版本 |
| 版本隔离 | 每个版本的全局包独立存储 |
| 无权限问题 | 所有操作基于 `~/.nvm`，无需 sudo |

## Ubuntu 安装

若 apt 已安装 node、npm，先移除：

```shell
sudo apt remove nodejs npm
```

下载脚本：https://github.com/nvm-sh/nvm/

> 为什么不用 apt 安装 Node.js？
> - Ubuntu 官方仓库的 Node.js 版本较旧，apt 无法直接升级
> - apt 安装的 npm 是系统级的，全局包安装需要 sudo
