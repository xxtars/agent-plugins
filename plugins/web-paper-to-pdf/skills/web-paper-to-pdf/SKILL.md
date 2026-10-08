---
name: web-paper-to-pdf
description: Convert a supported Distill-style web paper into an unofficial archival PDF using LaTeX. Use when the user asks to save a paper from transformer-circuits.pub, distill.pub, or alignment.anthropic.com as a PDF, especially when browser printing gives a poor layout.
---

# Render a Distill-style web paper as a LaTeX PDF

Browser headless print (`chrome --print-to-pdf`) on Distill papers produces poor layout: floating TOC sidebars overlap text, hr+h2 page-breaks insert blank pages, and figures don't size correctly. This skill instead parses the rendered DOM, extracts content + math + citations + images, and compiles a clean LaTeX paper.

## When to use

- The user asks to save a paper URL as PDF AND the page is a Distill/transformer-circuits template
- A previous browser-print attempt produced bad layout
- The user says they want LaTeX-rendered or paper-style output

Domains this is tuned for:
- `transformer-circuits.pub`
- `distill.pub`
- `alignment.anthropic.com` (uses Distill-derived template)

For non-Distill pages, fall back to `chrome --headless --print-to-pdf`.

## Toolchain

Check that the following dependencies are available before starting:
- Python 3 with `beautifulsoup4`, `lxml`, `requests`
- `pdflatex` (TeX Live; the NeurIPS template is tested against pdflatex)
- `bibtex`
- Google Chrome (headless, for DOM dump)

Template: **NeurIPS 2024 style** (`neurips_2024.sty` ships with the skill). This is the same template Anthropic uses for their archival arXiv versions of transformer-circuits papers (e.g. `arxiv.org/abs/2604.07729` "Emotion Concepts and their Function in a Large Language Model" → `neurips_2024.sty`).

The pipeline:
1. **DOM dump** — `chrome --headless --dump-dom <url>` waits ~15s for JS-rendered widgets, captures the rendered HTML.
2. **Parse** — bs4 walks the tree, converts:
   - `<d-math>` → `\(...\)` (inline) or `\[...\]` (block)
   - `<d-cite key="foo,bar">` → `\cite{foo,bar}` (numeric square-bracket style)
   - `<d-footnote>` → `\footnote{...}`
   - `<figure>` / `.gdoc-image` → `\begin{figure}\includegraphics{...}\caption{...}\end{figure}`
   - `<div class="authors"><div>row1<br>row2…</div></div>` → author block with rows separated by `\\`
   - `<div class="affiliations">` → centered affiliation below authors
   - `<h2/h3/h4>` → `\section* / \subsection* / \subsubsection*` (unnumbered, Anthropic style) + `\addcontentsline{toc}`
   - Leading paragraphs before first heading → `\begin{abstract}` block
3. **Download** — fetches `bibliography.bib` and all images to a local working dir (cached by URL hash).
4. **Copy** — `neurips_2024.sty` is copied into the work dir.
5. **Compile** — pdflatex → bibtex → pdflatex → pdflatex.

The script `convert.py` and the style file `neurips_2024.sty` are shipped alongside this `SKILL.md` — resolve them relative to this skill's directory at invocation time. Use the actual path of the loaded skill. Do not assume a particular client installation directory.

## How to invoke

```bash
SKILL_DIR="$(dirname "$0")"   # or substitute the actual skill directory
WORK=/tmp/paper-$(date +%s)
python3 "$SKILL_DIR/convert.py" <URL> --out $WORK
cp "$SKILL_DIR/neurips_2024.sty" $WORK/
cd $WORK
pdflatex -interaction=nonstopmode paper.tex >/dev/null
bibtex paper >/dev/null
pdflatex -interaction=nonstopmode paper.tex >/dev/null
pdflatex -interaction=nonstopmode paper.tex
cp paper.pdf <destination>.pdf
```

Pass an explicit `--out` directory for each paper; the script otherwise reuses its fixed temporary default. The work dir is reusable — the DOM dump and image downloads are cached.

## Failure modes and what to do

- **`! LaTeX Error: There's no line here to end. l.N \\`** — a stray `\\` from `<br>` outside a paragraph. The script already strips these in post-processing; if it still happens, look for the offending HTML pattern and extend the cleanup regex in `convert.py`.
- **`! LaTeX Error: Unicode character X (U+XXXX)`** — pdflatex doesn't know that character. Add it to `UNICODE_TEXT_FIXES` (for plain replacements like `−→-`) or `UNICODE_MACRO_FIXES` (for math symbols via `\ensuremath{...}`). The script's last-resort fallback strips any remaining non-ASCII to `?`, so this only fires for chars that get added back by walked-through `<d-math>` content.
- **Unresolved citation `[?]`** — bibtex didn't find the key. Check that `bibliography.bib` exists in the work dir and contains the expected `@article{key, ...}` entry. Some Distill papers use `bibliography.json` instead — extend `convert.py` to handle that.
- **Missing figures** — the page renders the figure with JS (e.g. a sliders widget). The `--dump-dom` capture should include it as static HTML; if not, increase `--virtual-time-budget` from 15s to 30s. SVG figures are skipped on purpose (would need rasterisation).
- **Unknown LaTeX macros** (e.g. `\R`, `\E`) inside `<d-math>` — the preamble already defines common ones (`\R`, `\E`, `\N`, `\Z`); add new `\providecommand{\X}{...}` entries to the preamble in `convert.py` if a paper uses more.
- **Tables look bad** — the script renders them as fixed-width `p{3cm}` columns. Edit `render_table` in `convert.py` if a specific paper needs different column widths.
- **Author block looks empty / shows "Authors Affiliations Published…"** — the paper uses an unfilled `<d-byline>` template. The script looks at `<div class="authors">` and `<div class="affiliations">` instead, which is where transformer-circuits actually puts the byline. If a different paper uses yet another structure, the extraction in `build_tex` needs to be widened.

## Verification

Always rasterize a few pages with `pdftoppm -f N -l N -r 80` and Read them visually after compile. Check:
- Page 1: title block looks right (title, authors, date)
- A figure page: image embedded with caption, not floating to wrong section
- Around `\section{References}`: bibliography rendered with author-year style
- A math-heavy page: equations typeset properly

## Note on scope

This is **NOT** a general HTML-to-PDF tool. It is specialized for the supported Distill-template page structure. For arbitrary blog posts or PDFs of regular web pages, `chrome --print-to-pdf` is still the right call.
