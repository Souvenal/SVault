# LaTeX-宏包

.sty 文件是 LaTeX 宏包（Style/Package）文件，提供具体的功能扩展。

---

## 常见宏包

| 宏包 | 功能 |
|------|------|
| amsmath.sty | 数学公式排版增强 |
| graphicx.sty | 插入图片 |
| hyperref.sty | 超链接、PDF 书签 |
| caption.sty | 图表标题样式自定义 |
| xeCJK.sty | 中文处理（XeLaTeX） |

---

## 加载方式

```latex
\usepackage{xxx}
```

---

## 与文档类的关系

```
\documentclass{article}     ← 选房子（文档类）
    ↓
\usepackage{amsmath}    ← 装空调（宏包）
\usepackage{graphicx}   ← 装电视
\usepackage{hyperref}   ← 装WiFi
    ↓
\begin{document}
你的内容
\end{document}
```

.sty 文件是可复用的功能模块，不同文档类可以共用同一个宏包。

---

## 相关概念

- [[LaTeX-文档类]] — .cls 文档类文件
- [[xeCJK]] — 中文处理宏包
