# LaTeX Resume Skill for Claude Code

A Claude Code skill for creating and iteratively refining professional LaTeX resumes, with deep support for **bilingual Chinese/English academic CVs** targeting tech/AI internships.

## Features

- **XeLaTeX + xeCJK** Chinese sans-serif font setup (Microsoft YaHei + Calibri)
- **VS Code LaTeX Workshop** dual-compiler configuration (pdflatex default + xelatex via magic comments)
- **3-level font hierarchy** for clean visual structure
- **STAR principle** templates for project descriptions (Situation → Action → Result)
- **Hyperlink styling** — black text with underline, not colored
- **Citation formatting** — bold only your name, use full journal names
- **Photo header layout** with minipage
- **Bilingual sync** workflow for CN/EN parallel editing
- **Common pitfalls** reference (fakebold removal, CJK wrapper cleanup, Chinese path issues)

## Install

```bash
claude skills add https://github.com/24kchengYe/latex-resume-skill
```

## When does it trigger?

This skill activates when you ask Claude Code to:
- Create or edit a LaTeX resume / CV
- Change fonts, spacing, or layout in a `.tex` resume
- Rewrite project descriptions using STAR principle
- Configure XeLaTeX or VS Code LaTeX Workshop
- Work with bilingual (Chinese/English) documents

## Example prompts

```
帮我改简历的字体为无衬线体
Rewrite my research descriptions using STAR principle
配置 VS Code 支持 xelatex 编译
把论文引用的格式统一一下
```

## License

MIT
