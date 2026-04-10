# xeCJK

XeTeX/LuaTeX 的中文支持宏包，推荐的新一代中文排版方案。

---

## CJK 家族对比

| 宏包 | 编译引擎 | 编码 | 字体配置 | 使用难度 | 现代推荐度 |
|------|----------|------|----------|----------|------------|
| CJK | pdfLaTeX | GBK/Big5 | 复杂（需安装） | 困难 | ⭐ |
| CJKutf8 | pdfLaTeX | UTF-8 | 复杂（需安装） | 中等 | ⭐⭐ |
| xeCJK | XeTeX/LuaTeX | 原生 UTF-8 | 简单（系统字体） | 简单 | ⭐⭐⭐⭐⭐ |

---

## xeCJK 特点

- 依赖引擎：XeTeX 或 LuaTeX
- 编码支持：原生 UTF-8，无需额外处理
- 工作方式：利用 XeTeX 的字体机制，直接调用系统字体
- 优点：直接使用系统字体（宋体、黑体等）、配置简单、自动处理中英文混排、支持 OpenType 字体特性、排版效果更好（标点压缩、行首行尾处理等）

---

## 使用示例

```latex
\usepackage{xeCJK}
\setCJKmainfont{SimSun}  % 设置中文字体
\begin{document}
直接输入中文，无需 \begin{CJK} 环境
\end{document}
```

---

## 结论

如果是新文档，直接用 xeCJK + XeTeX；CJK/CJKutf8 主要用于维护旧文档。

---

## 相关工具

- [[LaTeX-引擎]] — XeTeX 和 LuaTeX 均支持 xeCJK
