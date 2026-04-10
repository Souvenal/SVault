# LaTeX-引擎

LaTeX 引擎发展历程：latex → pdfLaTeX → XeTeX → LuaTeX。

---

## 传统 LaTeX（ latex / dvips）

历史：1980s-1990s

- 输出格式：DVI（Device Independent）
- 工作流程：`.tex → DVI → PS → PDF`（需多次转换）
- 字体：使用 Metafont 或 Type 1 字体，配置复杂
- 编码：ASCII 为主，需借助 inputenc 处理非英文

```bash
latex file.tex   # 生成 DVI
dvips file.dvi   # 转成 PS
ps2pdf file.ps   # 转成 PDF
```

现状：已基本被淘汰，只在维护旧文档时使用。

---

## pdfLaTeX

历史：1990s-2000s（里程碑式改进）

- 输出格式：直接生成 PDF
- 优势：支持 PDF 特性（超链接、书签、表单、多媒体嵌入）、PDF 图片（JPG、PNG、PDF）、微排版（microtype）更好
- 字体：仍使用 TeX 传统字体（TFM/VF）
- 编码：通过 inputenc 支持 UTF-8，但对中文支持有限

```bash
pdflatex file.tex   # 直接生成 PDF
```

现状：英文/西文文档的主流选择，中文支持较弱。

---

## XeTeX（ xelatex ）

历史：2004 年（Jonathan Kew 开发）

- 原生 Unicode 支持（无需 inputenc）
- 直接使用系统字体（通过操作系统字体机制）
- 支持 OpenType、AAT、Graphite 字体
- 字体配置：`\setmainfont{Times New Roman}` 直接使用系统字体
- 中文支持：配合 xeCJK 宏包，效果优秀

```bash
xelatex file.tex
```

优势：配置简单，安装字体无需复杂设置，中文排版效果好。

劣势：某些传统 TeX 字体包不兼容，编译速度稍慢。

---

## LuaTeX（ lualatex ）

历史：2007 年起（当前最现代的引擎）

- 基于 Lua 脚本引擎，可编程性极强
- 原生 Unicode 支持
- 使用 HarfBuzz 文本塑形库（现代字体处理标准）
- 支持 OpenType 字体 + 高级排版特性

```bash
lualatex file.tex
```

优势：更现代的字体渲染（HarfBuzz）、内存管理和性能更好、可通过 Lua 脚本扩展功能、复杂排版（如阿拉伯文、印地文）支持更好。

劣势：某些旧宏包兼容性不如 XeTeX，中文社区文档相对较少。

---

## 引擎对比总结

| 引擎 | 时代 | Unicode | 系统字体 | 中文推荐度 | 英文推荐度 |
|------|------|---------|----------|------------|------------|
| LaTeX | 1980s | ❌ | ❌ | ⭐ | ⭐ |
| pdfLaTeX | 1990s | ⚠️ 需配置 | ❌ | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| XeTeX | 2000s | ✅ | ✅ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| LuaTeX | 2010s+ | ✅ | ✅ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

---

## 推荐选择

**中文文档**：首选 XeTeX (xelatex) 或 LuaTeX (lualatex)，宏包用 xeCJK / ctex。

**英文/西文文档**：首选 pdfLaTeX（最快、最稳定），备选 LuaTeX（需要高级排版特性时）。
