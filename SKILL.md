---
name: latex-resume
description: Create and refine professional LaTeX resumes/CVs, especially bilingual Chinese/English academic CVs for tech internships. Use this skill when the user asks to create, edit, format, or improve a LaTeX resume — including font changes, layout adjustments, section rewriting, XeLaTeX/xeCJK configuration, or VS Code LaTeX Workshop setup. Also trigger when users mention resume formatting, CV styling, STAR principle for project descriptions, bilingual document layout, or academic CV best practices.
---

# LaTeX Resume Builder

A skill for creating and iteratively refining professional LaTeX resumes, with deep support for bilingual (Chinese/English) academic CVs targeting tech/AI internships.

## Core Workflow

Resume editing is inherently iterative. The typical loop is:

1. **Read** the current `.tex` file before any edit
2. **Edit** specific sections (prefer `Edit` tool over full rewrite)
3. **Compile** with `xelatex -interaction=nonstopmode <file>.tex 2>&1 | tail -5` to verify
4. **Show the user** a summary of changes

Always compile after edits to catch errors early. If compilation fails, check the log for the actual error line — don't guess.

## XeLaTeX + Chinese Font Setup

For any resume needing Chinese text, use XeLaTeX with xeCJK. This replaces the old pdfLaTeX + CJKutf8 approach and enables real bold fonts (no `\fakebold` hacks).

```latex
% !TEX program = xelatex
% !TEX recipe = xelatex (single run)
\documentclass[10pt,a4paper]{article}

\usepackage{fontspec}
\usepackage{xeCJK}

% Sans-serif fonts — clean and modern for resumes
\setmainfont{Calibri}
\setCJKmainfont[BoldFont=Microsoft YaHei Bold]{Microsoft YaHei}
\setCJKsansfont[BoldFont=Microsoft YaHei Bold]{Microsoft YaHei}
```

For English-only resumes, drop `xeCJK` and just use `fontspec` + `\setmainfont{Calibri}`.

Before configuring fonts, verify availability with `fc-list | grep -i "font name"`. Common safe choices:
- **Chinese sans-serif**: Microsoft YaHei, Noto Sans SC, SimHei
- **English sans-serif**: Calibri, Segoe UI, Arial
- **English serif**: Times New Roman, Cambria

## VS Code LaTeX Workshop Configuration

Two levels of config are needed — **workspace** `.vscode/settings.json` overrides **user** settings.

### Workspace settings (`.vscode/settings.json`)

```json
{
    "latex-workshop.latex.tools": [
        {
            "name": "pdflatex",
            "command": "pdflatex",
            "args": ["-synctex=1", "-interaction=nonstopmode", "-file-line-error", "%DOC%.tex"]
        },
        {
            "name": "xelatex",
            "command": "xelatex",
            "args": ["-synctex=1", "-interaction=nonstopmode", "-file-line-error", "%DOC%.tex"]
        },
        {
            "name": "bibtex",
            "command": "bibtex",
            "args": ["%DOCFILE%"]
        }
    ],
    "latex-workshop.latex.recipes": [
        {
            "name": "pdflatex -> bibtex -> pdflatex x2",
            "tools": ["pdflatex", "bibtex", "pdflatex", "pdflatex"]
        },
        {
            "name": "pdflatex (single run)",
            "tools": ["pdflatex"]
        },
        {
            "name": "xelatex (single run)",
            "tools": ["xelatex"]
        },
        {
            "name": "xelatex -> bibtex -> xelatex x2",
            "tools": ["xelatex", "bibtex", "xelatex", "xelatex"]
        }
    ],
    "latex-workshop.latex.recipe.default": "first",
    "latex-workshop.latex.build.forceRecipeUsage": false,
    "latex-workshop.latex.outDir": "%DIR%"
}
```

### Magic comments for per-file compiler selection

Add to the first lines of `.tex` files that need xelatex:
```latex
% !TEX program = xelatex
% !TEX recipe = xelatex (single run)
```

Both lines are needed for reliable detection. `% !TEX recipe` directly matches a recipe by name and is more reliable than `% !TEX program` alone.

## Font Hierarchy

Keep it simple — 3 levels for body content is enough.

```
% Header (outside the 3-level system):
%   \Large\bfseries — Name only
%   \normalsize — Contact info, target position

% Body content (3 levels):
%   Level 1: \normalsize\bfseries — Project titles, school names
%   Level 2: \normalsize — Description text
%   Level 3: \normalsize — Citations (only bold your own name)
```

Avoid `\footnotesize` or `\scriptsize` in resumes — they're too small. Reduce margins or spacing instead.

## Spacing Parameters

```latex
\usepackage[left=1.0cm,right=1.0cm,top=0.7cm,bottom=0.7cm]{geometry}
\linespread{1.25}  % or 1.3 for more breathing room

\titlespacing*{\section}{0pt}{16pt}{8pt}

% Between projects: \vspace{12pt}
% Between education entries: \vspace{6pt}
% List items:
\begin{itemize}[leftmargin=1.2em, itemsep=3pt, parsep=1pt, topsep=2pt]
```

Two pages is fine for substantial research output. Don't compress at the cost of readability.

## Hyperlink Styling

Black text with underline — cleaner than colored links on printed resumes:

```latex
\usepackage[colorlinks=true,urlcolor=black,linkcolor=black]{hyperref}
\let\oldhref\href
\renewcommand{\href}[2]{\oldhref{#1}{\underline{#2}}}
```

## The `\project` Command

```latex
\newcommand{\project}[3]{%
  \vspace{1pt}
  {\textbf{#1}} {\textbf{(#3)}} \hfill {\color{lightgray}#2}\par\vspace{0pt}
}
% Usage: \project{Title}{Date}{First Author | Journal}
```

## STAR Principle for Project Descriptions

Every project description should follow STAR in 3-4 concise sentences:

- **S (Situation)**: What problem exists? Why does it matter?
- **A (Action)**: What did you design/build/propose? Technical approach.
- **R (Result)**: Quantitative outcomes — accuracy, scale, downloads, impact.

**Good example:**
```
大型视觉语言模型能识别丰富视觉元素，但其输出与人类偏好存在系统性偏差，
现有对齐方法依赖大量标注数据与GPU算力。[S]
提出免训练后置概念瓶颈流水线：自动发现可解释评价维度，通过多智能体链
从冻结VLM提取概念分数，在混合流形上经岭回归校准至人类评分。[A]
达到72.2%准确率，超越有监督基线+15.1pp。[R]
```

**Bad example (no structure, just piling techniques):**
```
采用7个并行分析Agent与三Agent写作流水线，支持12+种大模型动态切换，
集成ChromaDB RAG检索增强，基于Streamlit构建，7000+行代码。
```

The reader can't understand *why* this exists or *what problem* it solves.

## Citation Formatting

Only bold your own name — the rest stays normal weight:

```latex
{论文：\textbf{Zhang Y}, Zhao R, Long Y*. Title Here. Journal, 2025.}\par

% Co-first author with dagger:
{论文：\textbf{Zhang Y}$^{\dag}$, Zhao H$^{\dag}$, Long Y*. Title. Journal, 2025.}\par

% Non-first author:
{论文：Shi C, Han X, \textbf{Zhang Y}, Niu D. Title. AAAI, 2025.}\par
```

Use full journal names (not abbreviations like "JAG") — they carry more weight with non-specialist readers. Prefix with "论文：" (CN) or "Paper:" (EN).

## Header Layout with Photo

```latex
\noindent
\begin{minipage}[c]{0.84\textwidth}
  \centering
  {\Large\textbf{Name}}\\[5pt]
  {Contact info}\\[3pt]
  {\textbf{Target position}}
\end{minipage}%
\hfill
\begin{minipage}[c]{0.13\textwidth}
  \includegraphics[width=\linewidth, trim=0 2cm 0 0, clip]{photo.png}
\end{minipage}
```

Use `trim=0 Xcm 0 0, clip` to crop photo bottom whitespace.

## Bilingual Sync

When maintaining CN and EN versions, every edit must be mirrored:
- Content, formatting, structural changes, hyperlinks
- Edit both files in the same tool call when possible to avoid drift

## Common Pitfalls

1. **`\fakebold` with XeLaTeX**: Not needed — XeLaTeX supports real `\textbf` for CJK. Remove when migrating from pdfLaTeX.
2. **`\begin{CJK}` with XeLaTeX**: Not needed — xeCJK handles everything. Remove CJK wrappers.
3. **File modified between read and edit**: VS Code auto-save can cause this. Re-read before editing.
4. **Workspace settings override user settings**: Check `.vscode/settings.json` if config seems ignored.
5. **Chinese paths break latexmk**: On Windows, Perl can't handle Chinese path characters. Use direct `xelatex`/`pdflatex` tools instead of latexmk.
