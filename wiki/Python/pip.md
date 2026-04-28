# pip

pip 是 Python 的包管理器，用于安装和管理 Python 包。

## pip install 演变

| 时代 | pip install 底层 | 是否执行 setup.py | 元数据格式 |
|------|------------------|------------------|-----------|
| 远古时代 | 调用 setuptools + setup.py | ✅ 是 | `.egg-info` |
| 现代主流 | 直接解压 .whl 预编译包 | ❌ 否 | `.dist-info` |
| 最新标准 | 读取 pyproject.toml 构建 | ❌ 否 | `.dist-info` |

三种模式的具体机制见 [[Build-System]]。

## 远古时代流程

当包没有 wheel 时，pip 需要从源码构建：

```
1. 下载源码包（.tar.gz）
2. 解压到临时目录
3. 读取 pyproject.toml 或 setup.py
4. 执行构建：
   - 编译 C 扩展（如有）
   - 运行 setup.py install 或 build_backend
5. 将文件复制到 site-packages/
6. 注册到 easy-install.pth
```

## 现代流程（wheel）

当包有预编译的 wheel 时，pip 直接安装跳过构建步骤：

```
1. 下载 .whl 包（本质是 zip 格式）
2. 解压到临时目录
3. 验证 wheel 元数据
4. 将内容复制到 site-packages/
```

**核心区别**：wheel 是预编译好的分发格式，pip 无需执行任何构建代码，安装更快。

## wheel vs sdist

| | wheel (.whl) | 源码包 (.tar.gz) |
|--|-------------|-----------------|
| 格式 | zip 压缩包 | tar.gz 压缩包 |
| 内容 | 预编译的 .pyc 文件 + 元数据 | 源码 + 构建脚本 |
| 安装 | 直接解压 | 需要构建（编译等） |
| 速度 | 快 | 慢 |

## Editable Install（pip install -e）

`pip install -e .` 以可编辑模式安装当前目录的包，修改源码后即时生效，无需重新安装。

### 命令拆解

- `-e`：`--editable` 的缩写
- `.`：当前目录，pip 从中找 `pyproject.toml` 或 `setup.py`

### 普通安装 vs 可编辑安装

| | `pip install .` | `pip install -e .` |
|--|----------------|-------------------|
| 原理 | 复制文件到 site-packages | 创建链接指向源码目录 |
| 本地修改生效 | ❌ 需重新安装 | ✅ 即时生效 |
| 适用场景 | 生产部署 | 开发调试 |

### 底层机制

pip 在 site-packages 中写入的文件取决于版本：

- **旧版（setuptools develop）**：创建 `.egg-link` 指向源码目录，通过 `.pth` 加载（[[Build-System#.egg-info|egg-info]] 的产物）
- **新版（pip 21.3+）**：使用 `__editable__` finder，更轻量，不依赖 setuptools develop

### 配置文件查找顺序

```
1. 找 pyproject.toml → 找到 → 按现代标准安装
2. 没找到 → 找 setup.py → 按传统方式安装
3. 都没有 → 报错
```

pip 21.1+ 原生支持纯 `pyproject.toml` 项目（无需 setup.py）。

### 常用配套命令

```bash
# 查看已安装的可编辑包
pip list -e
```

## 相关链接

- [[Setup-Py]] — setup.py 打包工具
- [[Build-System]] — .egg-info / .dist-info、pyproject.toml 构建后端
- [[Nodejs/npm|Nodejs/npm]] — npm 包管理对比
