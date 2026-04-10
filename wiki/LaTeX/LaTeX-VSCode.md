# LaTeX-VSCode

VSCode LaTeX Workshop 插件配置，用于 LaTeX 文档编辑和编译。

---

## 安装

```bash
brew install texlive
```

然后在 VSCode 中安装 LaTeX Workshop 插件。

---

## 推荐编译工具配置

```json
{
    "latex-workshop.latex.autoBuild.run": "never",
    "latex-workshop.showContextMenu": true,
    "latex-workshop.intellisense.package.enabled": true,
    "latex-workshop.latex.tools": [
        {
            "name": "xelatex",
            "command": "xelatex",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "%DOCFILE%"
            ]
        },
        {
            "name": "pdflatex",
            "command": "pdflatex",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "%DOCFILE%"
            ]
        },
        {
            "name": "latexmk",
            "command": "latexmk",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-pdf",
                "-outdir=%OUTDIR%",
                "%DOCFILE%"
            ]
        },
        {
            "name": "bibtex",
            "command": "bibtex",
            "args": [
                "%DOCFILE%"
            ]
        }
    ]
}
```

---

## 推荐编译配方

```json
{
    "latex-workshop.latex.recipes": [
        {
            "name": "XeLaTeX",
            "tools": ["xelatex"]
        },
        {
            "name": "PDFLaTeX",
            "tools": ["pdflatex"]
        },
        {
            "name": "xelatex -> bibtex -> xelatex*2",
            "tools": [
                "xelatex",
                "bibtex",
                "xelatex",
                "xelatex"
            ]
        }
    ]
}
```

xelatex → bibtex → xelatex*2 是处理参考文献的标准流程。

---

## 清理配置

```json
{
    "latex-workshop.latex.clean.fileTypes": [
        "*.aux", "*.bbl", "*.blg", "*.idx", "*.ind",
        "*.lof", "*.lot", "*.out", "*.toc",
        "*.acn", "*.acr", "*.alg", "*.glg", "*.glo",
        "*.gls", "*.ist", "*.fls", "*.log", "*.fdb_latexmk"
    ],
    "latex-workshop.latex.autoClean.run": "onFailed",
    "latex-workshop.latex.recipe.default": "lastUsed",
    "latex-workshop.view.pdf.internal.synctex.keybinding": "double-click"
}
```

---

## 相关工具

- [[LaTeX-引擎]] — XeLaTeX 和 PDFLaTeX
- [[BibTeX]] — 参考文献编译
