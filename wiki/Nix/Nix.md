# Nix

函数式包管理器，支持声明式配置、可复现构建。

---

## 安装入门

- 官网下载：https://nixos.org/download/
- 官方教程：https://nix.dev/tutorials/first-steps/

## nix-shell

创建临时 shell 环境，退出后环境消失。

```shell
# 创建 shell 并安装工具
nix-shell -p git nvim

# 执行单条命令
nix-shell -p git --run "git --version"
```

首次使用需下载所有依赖，耗时较长。

包查找：[search.nixos.org](https://search.nixos.org/packages)

## 清理

```shell
nix-collect-garbage
```

---

相关：[[Nix-language]] — Nix 语言：表达式、函数、惰性求值
