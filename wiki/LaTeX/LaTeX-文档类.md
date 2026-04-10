# LaTeX-文档类

.cls 文件是 LaTeX 文档类（Class）文件，决定了文档的整体格式。

---

## 标准文档类

| 文档类 | 用途 |
|--------|------|
| article | 基础文章排版（推荐） |
| report | 报告（支持章节） |
| book | 书籍 |
| letter | 信函 |

---

## 文档类控制的内容

当写 `\documentclass{article}` 时，LaTeX 加载 article.cls，里面定义了：

| 功能 | 例子 |
|------|------|
| 页面设置 | 纸张大小、页边距、行距 |
| 标题格式 | 章节标题几号字、怎么编号 |
| 字体设置 | 正文字体、标题字体 |
| 页眉页脚 | 奇偶页不同、页码位置 |
| 环境定义 | 摘要、关键词、定理环境的样式 |

---

## LoadClass 命令

.cls 中的命令，用于加载另一个文档类作为基础，继承其所有功能：

```latex
\LoadClass[a4paper, 10.5pt,twocolumn,twoside,journal]{article}
```

`[]` 里的选项传递给底层文档类。

---

## 相关概念

- [[LaTeX-宏包]] — .sty 宏包文件
