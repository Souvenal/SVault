# BibTeX

参考文献处理工具，专门管理文献引用。

---

## 工作流程

```latex
% 在 .tex 文件中
\cite{zhang2023}           % 引用
\bibliography{references}  % 引用 .bib 文件

% references.bib 内容示例
@article{zhang2023,
author  = {张三 and 李四},
title   = {某研究},
journal = {计算机学报},
year    = {2023}
}
```

---

## 编译流程

需要四次编译：

```bash
xelatex 论文.tex   # 生成辅助文件
bibtex 论文        # 处理参考文献
xelatex 论文.tex   # 插入引用
xelatex 论文.tex   # 确定页码
```

---

## 相关工具

- [[LaTeX-引擎]] — XeTeX/LuaTeX 等引擎均支持 BibTeX
- [[xeCJK]] — 中文文档配合 xeCJK 使用
