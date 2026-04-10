# npx

npm 包执行器，npm 内置程序。

## 主要用途

### 1. 执行本地包

```bash
npx eslint .    # 等同于 ./node_modules/.bin/eslint .
```

### 2. 临时执行远程包

无需全局安装，用完即删：

```bash
npx create-react-app my-app   # 临时下载执行，执行完删除
```

| 方式 | 命令 | 结果 |
|------|------|------|
| 全局安装 | `npm install -g cowsay` | 永久占用磁盘空间 |
| npx | `npx cowsay` | 临时使用，自动清理 |

### 3. 运行指定版本

```bash
npx cowsay@1.4.0 "hello"    # 运行特定版本
```

### 4. 执行 GitHub 代码

```bash
npx github:user/repo
```
