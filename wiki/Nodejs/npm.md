# npm

Node.js 默认包管理器。

## 核心功能

| 功能 | 命令 |
|------|------|
| 安装到项目 | `npm install foo` |
| 全局安装 | `npm install -g bar` |
| 运行脚本 | `npm run build` |
| 发布包 | `npm publish` |

## 依赖管理

- **package.json** — 项目信息和依赖声明
- **package-lock.json** — 锁定版本，确保一致性
- **node_modules/** — 存放下载的包

## 常用命令

```shell
npm init          # 初始化新项目
npm install       # 安装所有依赖
npm update        # 更新包
npm uninstall foo # 卸载包
```

## 换源

```shell
npm config get registry  # 查看当前源
npm config set registry https://registry.npmmirror.com  # 切换为淘宝源
```
