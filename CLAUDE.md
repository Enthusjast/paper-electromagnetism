# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build & Compilation

- **Compile the paper**: `cd paper && latexmk -xelatex main.tex` (auto-runs biber and re-runs xelatex as needed)
- **Full manual build**: `cd paper && xelatex main.tex && biber main && xelatex main.tex && xelatex main.tex`
- **Clean auxiliary files**: `cd paper && latexmk -C` (removes all generated files except the PDF)

The paper **requires XeLaTeX** (not pdfLaTeX) because it uses `ctex` for Chinese typesetting and `fontspec` for system font loading. `latexmk`, `xelatex`, and `pdflatex` are available at `/opt/vlab/texlive/2026/bin/x86_64-linux/`.

## Required Fonts

The paper expects these fonts to be installed on the system:
- **Noto Serif CJK SC** — main CJK serif font
- **Noto Sans CJK SC** — CJK sans-serif (also used as bold variant)
- **LXGW WenKai** — CJK italic variant

If compilation fails with font errors, install: `sudo apt install fonts-noto-cjk fonts-lxgw-wenkai`.

## Project Structure

```
paper/                   # The actual paper content
  main.tex               # Paper source (Chinese, electromagnetism)
  main.pdf               # Compiled output PDF
  rho.bib                # Bibliography (biblatex format)
  1.png                  # Embedded figure

rho-class/               # Custom Rho LaTeX document class (v2.0.0)
  rho.cls                # Main class file — layout, title, sections, headers/footers
  rhoenvs.sty            # Custom environments (rhoenv, info, note) and color definitions
  rhobabel.sty           # Multilingual support (English/Spanish) for labels
```

## Architecture: The Rho Document Class

`rho.cls` is a **two-column academic paper template** built on `extarticle`. Key design points:

- **Base class**: `extarticle` (via `\LoadClass[twocolumn]{extarticle}`)
- **Page layout**: A4 paper, 1.25cm left/right margins, 2cm top/bottom, 15pt column separation
- **Bibliography**: Uses `biblatex` with `biber` backend, `ieee` style, sorted by year-name-title. The bib file is hardcoded as `rho.bib` via `\addbibresource{rho.bib}` in the class file — this means the `.bib` file must be named `rho.bib` and be in the same directory as the `.tex` file being compiled.
- **Language support**: `rhobabel.sty` provides English/Spanish translations. Toggle with `\setboolean{es-babel}{true}` in the sty file. Chinese is added at the document level via `ctex`, not through the class.
- **Custom environments** (defined in `rhoenvs.sty`): `rhoenv` (framed box with colored title), `info`, `note` — all use the `rhocolor` theme (dark blue default: RGB 0, 0.2, 0.4).
- **Configuration booleans** (set in the `.tex` preamble):
  - `\setbool{rho-abstract}{true}` — show/hide abstract block
  - `\setbool{corres-info}{false}` — show/hide corresponding author info
  - `\setbool{linenumbers}{false}` — show/hide line numbers

## Figures

Place figures in `paper/` alongside `main.tex`. Insert with:
```latex
\begin{figure}[H]
    \centering
    \includegraphics[width=0.72\columnwidth]{filename.png}
    \caption{Caption text.}
\end{figure}
```
The `H` float specifier comes from the `here` package (loaded by the class).

## Bibliography

Add entries to `paper/rho.bib` using standard biblatex format. Cite with `\cite{key}`. The bibliography prints automatically — no `\printbibliography` command is needed in the document body (though you may need to add it if the class doesn't auto-print it).

## Editing Notes

- The `\rhostart{在}` command inserts a drop-cap ("lettrine") at the beginning of a section — the first character of the argument becomes a large colored initial.
- Chinese paragraph indentation is set to `2em` via `\setlength{\parindent}{2em}`.
- `\linespread{1.2}` is used for line spacing.
- The author's custom `\vec` redefinition (`\overrightarrow`) applies throughout the document.
- The `siunitx` package is loaded with `mode=math` and `reset-math-version=false`.