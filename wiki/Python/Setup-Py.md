# Setup.py

setup.py 是 Python 传统的项目打包、安装与分发的核心配置脚本。核心使命：让代码变成可安装、可导入、可分享的标准包。

## 核心作用

1. **一键安装项目** - `python setup.py install`，将项目安装到 Python 环境
2. **打包分享** - 生成 .tar.gz 源码包或 .whl 二进制包
3. **自动管理依赖** - 声明依赖，安装时自动下载
4. **定义元数据** - 包名、版本、作者、许可证等
5. **指定包含内容** - 明确哪些文件被打包

## 常用命令

```bash
# 安装到当前环境
python setup.py install

# 生成源码压缩包
python setup.py sdist

# 生成 wheel 包（推荐，安装更快）
python setup.py bdist_wheel
```

## 极简示例

```python
from setuptools import setup, find_packages

setup(
    name="my_demo",        # 包名（pip install 时用）
    version="1.0.0",       # 版本号
    author="你的名字",     # 作者
    description="我的第一个Python包", # 描述
    packages=find_packages(), # 自动识别项目里的所有包
    install_requires=[     # 项目依赖
        "requests>=2.25.0",
        "numpy>=1.21.0"
    ]
)
```

## 自定义命令行工具

setup.py 可以配置入口点（entry points），自动生成命令行脚本：

```python
from setuptools import setup

setup(
    name="mytool",
    entry_points={
        'console_scripts': [
            'mytool=mytool.cli:main',  # 安装后直接用 mytool 命令
        ],
    },
)
```

安装后，用户终端输入 `mytool run` 即可调用 `mytool/cli.py` 的 `main` 函数。

**实现原理**：setuptools 会根据平台生成不同的启动脚本：

- **Unix（Linux/macOS）**：生成带 shebang 的 shell 脚本
  ```python
  # 安装后生成的 mytool 脚本
  #!/usr/bin/env python
  # -*- coding: utf-8 -*-
  import re
  import sys
  from mytool.cli import main
  if __name__ == '__main__':
      sys.argv[0] = re.sub(r'(-script\.pyw|\.exe)?$', '', sys.argv[0])
      sys.exit(main())
  ```

- **Windows**：生成 .exe 启动器（本质是引导程序）
  - `Scripts/mytool.exe`（或 `.exe` 文件）
  - Windows 会创建 `Scripts/mytool-script.py`，由 exe 启动器调用 Python 解释器执行

## setuptools 核心功能

setuptools 只专注一件事：把 Python 代码变成标准的、可安装的包。目前仍是 Python 生态最主流的构建后端，详见 [[Build-System#四大构建后端|Build System]]。具体做 5 件事：

1. **扫描代码** - `find_packages()` 自动找到所有要打包的模块
2. **依赖解析** - 读取依赖，安装时自动下载第三方库
3. **打包** - 生成 .tar.gz / .whl 标准分发包
4. **安装** - 把包复制到 Python 环境，让你能 import
5. **生成命令** - 通过 entry_points 自动生成命令行工具（如 pip 命令就是 setuptools 生成的）

## setuptools vs distutils

- **distutils**：Python 标准库内置，最早的打包工具
- **setuptools**：distutils 的增强版，实现上述 5 个功能，目前最常用

通常安装 setuptools 后使用：
```python
from setuptools import setup
```

## pyproject.toml

现代 Python 配置标准（PEP 517/518），替代旧的 `setup.py`，统一项目元数据和构建配置。详见 [[Build-System]]。

```toml
[project]
name = "mypackage"
version = "1.0.0"
dependencies = ["requests>=2.0"]
```

## find_packages() 的两种模式

`find_packages()` 有两种包识别模式，默认 `legacy=True`。

| | `legacy=True`（默认） | `legacy=False` |
|--|----------------------|----------------|
| 识别条件 | 目录中必须有 `__init__.py` | 任意目录都识别为包 |
| 适用 | 兼容 Python 2 和老项目 | Python 3.3+ namespace package |
| 风险 | 可能漏掉无 `__init__.py` 的包 | 可能误打包 tests/、docs/ 等目录 |

```python
# 这两行等价
find_packages()
find_packages(legacy=True)  # 默认值
```

> 为什么默认保留 legacy 模式？
> 避免将 `tests/`、`scripts/`、`docs/` 等不含 `__init__.py` 的普通目录错误打包进分发包。

## find_packages() 的常见坑

### pip install 不直接调用 find_packages()

`find_packages()` 是 setuptools 的可选工具函数，不是 pip 的一部分。调用链：

```
pip → 构建后端（setuptools/poetry/flit...）→ 可选调用 find_packages()
```

- 手动指定 `packages=["my_module"]` → 不调用 `find_packages()`
- 非 setuptools 后端（poetry、flit）→ 完全不使用它
- 纯 `pyproject.toml` 项目（setuptools 后端）→ 底层逻辑等价但不直接调用

### src layout 漏包

```
project/
├── pyproject.toml
└── src/
    └── my_package/
```

`find_packages()` 默认从项目根目录扫描，不会进入 `src/` 子目录，导致 `my_package` 被遗漏。需要在 pyproject.toml 中配置：

```toml
[tool.setuptools.packages.find]
where = ["src"]
```

src layout 和 legacy 模式是两个独立问题，`legacy=False` 无法解决 src layout 漏包。

## 相关链接

- [[pip]] — pip 包管理器
- [[Build-System]] — .egg-info / .dist-info、pyproject.toml 构建后端选择
