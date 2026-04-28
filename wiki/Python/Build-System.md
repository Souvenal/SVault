# Build System

Python 打包构建体系：egg-info、dist-info、pyproject.toml 构建后端选择。

## .egg-info

setuptools 生成的包元数据目录，遵循 PEP 241 规范，记录包名、版本、依赖、入口点等信息。

### 触发方式

- `python setup.py egg_info` — 手动生成
- `python setup.py develop` — 开发模式安装
- `python setup.py install` — 传统安装
- `pip install .` 在没有 `[build-system]` 时回退到旧模式，也会触发

### 典型文件

| 文件 | 作用 |
|------|------|
| PKG-INFO | 核心元数据（名称、版本、作者等） |
| SOURCES.txt | 包含的所有源文件列表 |
| requires.txt | 依赖列表 |
| entry_points.txt | 控制台脚本 / 插件入口配置 |
| top_level.txt | 包的顶级模块名称 |

> .egg-info 可以安全删除，构建时会重新生成。现代项目如果完全按新规范打包，不会出现这个目录。

## .dist-info

PEP 376 定义的现代包元数据标准格式，替代 `.egg-info`。pip 安装 wheel 后在 site-packages 中生成。

与 `.egg-info` 的区别：标准化程度更高、文件结构更规范（如 `METADATA`、`RECORD`、`WHEEL`、`entry_points.txt`），是 wheel 格式的配套元数据。现代打包工具（setuptools / Poetry / Flit / Hatch）统一生成 `.dist-info`。

## pyproject.toml [build-system]

`[build-system]` 是 PEP 517/518 定义的构建配置段，告诉 pip / `python -m build` 用什么工具来打包：

```toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]  # 构建时需要先安装的包
build-backend = "setuptools.build_meta"    # 构建入口
```

两个字段：
- `requires`：构建前自动安装的依赖（构建工具本身）
- `build-backend`：Python 模块路径，负责执行打包逻辑

> `[build-system]` 是**构建时**的工具，和项目**运行时**的依赖完全分开。运行依赖写在 `[project].dependencies`（setuptools）或 `[tool.poetry.dependencies]`（Poetry）。

## 四大构建后端

| 后端 | `build-backend` | 特点 |
|------|----------------|------|
| setuptools | `setuptools.build_meta` | 官方推荐，生态兼容性最好 |
| Poetry | `poetry.core.masonry.api` | 一站式依赖管理 + 打包 |
| Flit | `flit_core.buildapi` | 轻量，适合纯 Python 小项目 |
| Hatch | `hatchling.build` | 现代，插件丰富 |

只要按 PEP 517 标准配置，pip 都能正确安装，不关心具体用哪个后端。

### 构建流程

执行 `python -m build` 时：

1. 读取 `[build-system]`
2. 自动安装 `requires` 里的包
3. 调用 `build-backend` 指定的入口执行构建
4. 输出 `.dist-info` + wheel / sdist

## 旧模式回退

如果项目没有 `pyproject.toml`，或者没有 `[build-system]` 段：

构建工具自动回退到旧 setuptools 模式 → 执行 `setup.py` 逻辑 → 生成 `.egg-info`。

这就是为什么有些项目目录下会出现 `.egg-info` 目录——要么是旧项目，要么是混用了新旧配置。

## 相关链接

- [[Setup-Py]] — setup.py 作用、setuptools 核心功能
- [[pip]] — pip 包管理器、wheel vs sdist、editable install
