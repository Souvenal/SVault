# LaTeX-架构

LaTeX 三层架构：引擎负责排版计算，格式定义用户命令，发行版打包分发。

---

## 三层架构

| 层级 | 说明 | 示例 |
|------|------|------|
| 发行版 Distribution | 打包好的软件集合 | TeX Live, MiKTeX |
| 格式 Format | 宏包 + 规范 | LaTeX2e, LaTeX3 |
| 引擎 Engine | 排版内核 | pdfTeX, XeTeX, LuaTeX |

### 类比

| 对比 | 引擎 | 格式 | 发行版 |
|------|------|------|--------|
| 类似 | JVM / .NET CLR | Java 语言规范 | JDK |
| 负责 | 排版计算 | 用户命令定义 | 打包分发 |
| 例子 | pdfTeX, XeTeX | LaTeX2e | TeX Live |

---

## 引擎（Engine）

负责实际排版计算：断行、分页、字形放置、输出文件生成。

| 引擎 | 特点 |
|------|------|
| TeX | 原始引擎，只输出 DVI |
| pdfTeX | 原生 PDF 输出，微排版改进 |
| XeTeX | 支持 Unicode、系统字体（TTF/OTF） |
| LuaTeX | 嵌入 Lua 脚本引擎，最现代 |

---

## 格式（Format）

在引擎之上的一套宏定义和规范。

| 格式 | 说明 |
|------|------|
| plain TeX | 最基础的宏集合 |
| LaTeX2e | 当前标准格式（1994年至今） |
| LaTeX3 | 下一代格式（开发中，部分功能已可用） |
| ConTeXt | 另一个强大的文档格式 |

`\NeedsTeXFormat{LaTeX2e}` 表示"我需要 LaTeX2e 这套宏定义"。

---

## 发行版（Distribution）

打包好的完整系统，包含引擎、格式、数千个宏包。

- TeX Live（跨平台，最常用）
- MiKTeX（Windows）
- MacTeX（Mac，基于 TeX Live）
