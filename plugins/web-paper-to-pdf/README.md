# web-paper-to-pdf

Convert a supported Distill-style web paper into an unofficial archival PDF.
The workflow captures the rendered page, extracts text, equations, references,
and figures into LaTeX, then compiles the document.

## Supported content

The converter is designed for Distill-derived pages such as transformer-circuits.pub,
distill.pub, and alignment.anthropic.com. It is not a general HTML-to-PDF engine.
Interactive widgets cannot be preserved in a static PDF.

## Requirements

- Python 3 with beautifulsoup4, lxml, and requests.
- Google Chrome for rendering the source page.
- pdflatex and bibtex for compilation.
- A PDF renderer such as pdftoppm for checking the output.

The current converter uses Chrome's standard macOS application path. Other
platforms need an adjustment to fetch_rendered_html() in convert.py.

## Use with an agent

Enable the plugin and ask it to save a supported paper URL as a PDF. The
[skill instructions](skills/web-paper-to-pdf/SKILL.md) describe extraction,
compilation, and visual inspection. Dependencies must be checked on the machine
running the workflow.

## Manual use

Run from the repository root, replacing PAPER_URL with the paper's URL:

```bash
skill_dir="$(pwd)/plugins/web-paper-to-pdf/skills/web-paper-to-pdf"
paper_work="$(mktemp -d /tmp/web-paper-pdf.XXXXXX)"
python3 "$skill_dir/convert.py" "PAPER_URL" --out "$paper_work"
cp "$skill_dir/neurips_2024.sty" "$paper_work/"
cd "$paper_work"
pdflatex -interaction=nonstopmode paper.tex
bibtex paper
pdflatex -interaction=nonstopmode paper.tex
pdflatex -interaction=nonstopmode paper.tex
```

convert.py writes the LaTeX source, downloads available images, and attempts to
fetch the bibliography. The following commands copy the style file and compile
paper.pdf. Inspect the title, figures, references, and equations before using
the result. Reusing an output directory may reuse cached page content and images.

## Limits and attribution

SVG figures are skipped. Complex tables, unsupported math macros, and unusual
page structures may need manual correction. Render dates can differ between
builds. Keep the original web page for interactive content and authoritative text.

Generated PDFs include an unofficial-rendering notice. They are not endorsed by
the original authors. Respect the source page's copyright and license. The
bundled NeurIPS 2024 style file retains its original author attribution and terms;
this project is not affiliated with the organizations publishing source papers.

Installation is covered in the [repository README](../../README.md).
Repository code uses the [MIT License](../../LICENSE).
