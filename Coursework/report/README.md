# Coursework report guide

An editable answer template for University of Bristol SEMTM0051, *Underfitting, Overfitting and Held-out Data*. The supplied deadline is **26 November 2026, 13:00**. There are no solutions, experimental results or fabricated plots. The lecturer's `../coursework.tex` remains the reference for questions and submission rules.

## Getting started

1. Edit `\studentname` and `\studentnumber` near the top of `report.tex`. Check the unit's anonymous-marking rules and remove the name if required.
2. Open `questions/question1.tex` for Exercise 1 (through `question8.tex` for Exercise 8). Replace each entire `\placeholder{Write answer here.}` command with your own answer, usually 2–5 sentences. Keep the `answer` environment and its letter.
3. Export your own six-panel figure to `figures/exercise1-six-panel.pdf`; it replaces the missing-file box automatically. Replace the placeholder caption too.
4. Compile and inspect the PDF. Continue one exercise at a time, and remove all placeholders before submission.

Files: `report.tex` contains shared styling, the first-page heading and the ordered question imports; `questions/question1.tex` through `questions/question8.tex` contain the eight exercises, including their text, figures and tables; `figures/` holds externally generated plots; this `README.md` explains editing; `.gitignore` excludes the generated `report.pdf` and compilation products. `.gitkeep` preserves the initially empty figures directory. Figure PDFs remain trackable. The lecturer's files and existing exercises outside this directory are untouched and are not automatically imported.

The design uses A4, 11pt Helvetica body text, conventional mathematical fonts, 2.2 cm margins, compact headings, restrained blue accents and dark text suitable for grayscale printing. The heading sits on page one, with no cover or contents page. Captions are slightly smaller than body text; plot axes, legends and titles should remain approximately body-text size in the finished PDF.

## Working with separate question files

```text
report/
├── report.tex
├── questions/
│   ├── question1.tex
│   ├── question2.tex
│   ├── question3.tex
│   ├── question4.tex
│   ├── question5.tex
│   ├── question6.tex
│   ├── question7.tex
│   └── question8.tex
├── figures/
└── README.md
```

Edit each exercise's answers, figures, captions and tables in its corresponding question file. Shared fonts, colours, packages and student details remain in `report.tex`. Each question is imported with `\input{questions/question1}` (and similarly for 2–8); `\input` allows the first seven exercises to flow naturally without forcing a page break per file.

The question files are document fragments: do not add `\documentclass`, `\begin{document}` or `\end{document}` to them. Always compile **`report.tex`**, even while editing a question. Each question starts with `% !TeX root = ../report.tex`, which tells supporting editors such as LaTeX Workshop to build the main report.

Figure paths are still relative to `report/`, **not** `questions/`. For example, inside `questions/question1.tex`, use `\plotfile{figures/exercise1-six-panel.pdf}`, not `../figures/...`. Labels and references work across question files as part of the same report. Keep the imports in order to preserve exercise numbering. The Exercise 8 page break stays in `report.tex`; its page-budget label stays with the heading in `questions/question8.tex`.

## Compilation on macOS and viewing in VS Code

A standard MacTeX/TeX Live installation supplies the packages and tools. No shell escape, custom fonts or Python integration are required. From the repository root:

```sh
cd Coursework/report
latexmk -pdf -interaction=nonstopmode -halt-on-error report.tex
open report.pdf
```

If latexmk is missing but pdfLaTeX is installed, run this twice, and again if rerun warnings remain:

```sh
pdflatex -interaction=nonstopmode -halt-on-error report.tex
pdflatex -interaction=nonstopmode -halt-on-error report.tex
```

macOS TeX executables are normally in `/Library/TeX/texbin`. If commands are missing, check your installed distribution and terminal PATH. If TeX is absent, you must install a distribution yourself; the project installs nothing.

In VS Code, open `report.tex`. If **LaTeX Workshop** is already installed, run **LaTeX Workshop: Build LaTeX project**, then **LaTeX Workshop: View LaTeX PDF file** from the Command Palette. Its default latexmk workflow needs no project settings. Otherwise build in the integrated terminal and view in Preview. Save changes before building; use `Coursework/report` as the terminal working directory so figure paths resolve.

Optional: `latexmk -c` cleans intermediate files while retaining the PDF; `latexmk -C` also removes the generated PDF.

## Every custom command and environment

| Interface | Arguments and example |
| --- | --- |
| `\studentname` | No arguments; edit its definition, e.g. `\newcommand{\studentname}{Your name}`. Used in the first-page heading. |
| `\studentnumber` | No arguments; edit its definition, e.g. `\newcommand{\studentnumber}{Your number}`. |
| `\exercise{title}` | Required title; creates the next numbered heading. Example: `\exercise{Fitting All the Data}\label{ex:1}`. The eight existing calls already give correct numbering; do not insert extra calls between them. |
| `\begin{answer}{a} ... \end{answer}` | Required letter without parentheses. Prints **(a)**; supports multiple paragraphs, equations and floats, and may span pages. |
| `\placeholder{instruction}` | Required instruction, shown in grey italics and square brackets. Example: `\placeholder{Write answer here.}`. Replace the whole command with real content. |
| `\plotfile[width]{path}` | Required PDF/PNG path relative to `report/`; optional width defaults to `\linewidth`. Example: `\plotfile[0.8\linewidth]{figures/my-plot.pdf}`. Preserves proportions, caps image height at 65% of the text area, and displays a labelled placeholder if the file is absent. Wrap it in `figure` for captions and numbering. Works inside a subfigure using its local width. |

Page-budget arithmetic and temporary macros inside it are internal, not editing interfaces. Standard environments and package commands are illustrated below. Use unique labels and simple filenames without spaces or special characters.

## Text, new paragraphs and emphasis

```latex
\begin{answer}{a}
Your own answer goes here.

This blank line starts a new paragraph in the same answer.
\textbf{Bold}, \emph{italic emphasis}, and \texttt{monospace text}.
\end{answer}
```

A single newline is normally a space. Use a blank line for a paragraph, not repeated `\\`. Escape special characters in prose: `\%`, `\&`, `\_`, `\#`, `\$`, `\{`, `\}`. For example, `\texttt{my\_plot.pdf}` prints an underscore. File-path arguments to `\plotfile` may contain unescaped underscores.

## Inline, displayed and numbered maths

These are syntax examples, not coursework answers. Include only notation relevant to your own explanations.

```latex
Inline: $x_i$, $\alpha$, $\lambda$, $\sigma^2$ and $\bm{v}$.

Unnumbered display:
\[
  z = \frac{a}{b}
\]

Numbered equation:
\begin{equation}
  s = \sum_{i=1}^{n} x_i
  \label{eq:sum}
\end{equation}

More symbols: $\nabla f$, $\lVert\bm{v}\rVert_2$, $x_{i+1}$,
$10^{-3}$, $\theta$, $\mathbb{R}$.
See \cref{eq:sum}, or Equation~\eqref{eq:sum}.
```

`amsmath`, `amssymb` and `bm` are loaded. Use `align` for aligned numbered lines, `align*` for unnumbered lines. Do not nest `equation` inside `\[ ... \]`.

## Images and graphs: full width or smaller

PDF is preferred for vector plots; PNG is supported. A missing file intentionally becomes a placeholder, so a successful build does not prove that all images exist.

```latex
\begin{figure}[htbp]
  \centering
  \plotfile{figures/my-plot.pdf}
  \caption{Describe your own figure.}
  \label{fig:my-plot}
\end{figure}
```

For a smaller centred image, replace the command with:

```latex
\plotfile[0.65\linewidth]{figures/my-image.png}
```

This uses 65% of the available width, adjusting height proportionally. Never specify an arbitrary width and height that distort the image. The helper's height cap can shrink unusually tall figures; redesign their aspect ratio or split panels if labels become too small. The standard command `\includegraphics[width=0.7\linewidth]{figures/my-plot.pdf}` is available if you deliberately want missing files to trigger an error.

Keep captions concise; put interpretation in your answer. Put `\label` immediately after `\caption`. Reference with `Figure~\ref{fig:my-plot}` or `\cref{fig:my-plot}`. Replace the Exercise 1 placeholder rather than adding a duplicate figure.

## Two plots side by side

```latex
\begin{figure}[htbp]
  \centering
  \begin{subfigure}[t]{0.48\linewidth}
    \centering
    \plotfile{figures/left.pdf}
    \caption{Left panel description.}\label{fig:pair-left}
  \end{subfigure}\hfill
  \begin{subfigure}[t]{0.48\linewidth}
    \centering
    \plotfile{figures/right.pdf}
    \caption{Right panel description.}\label{fig:pair-right}
  \end{subfigure}
  \caption{Describe the comparison.}\label{fig:pair}
\end{figure}
```

Widths and gaps must fit within `\linewidth`. Use `\cref{fig:pair}` for the whole figure, `\cref{fig:pair-left}` for a panel, or `\subref{fig:pair-left}` for just its letter.

## Six-panel figures

The simplest Exercise 1 option is one composite exported as `figures/exercise1-six-panel.pdf`, using the existing figure in the template. Panel letters embedded in that PDF are not separate LaTeX references. For individually referenceable panels, replace the existing figure with:

```latex
\begin{figure}[htbp]
  \centering
  \begin{subfigure}[t]{0.32\linewidth}
    \plotfile{figures/panel-a.pdf}
    \caption{Panel description.}\label{fig:six-a}
  \end{subfigure}\hfill
  \begin{subfigure}[t]{0.32\linewidth}
    \plotfile{figures/panel-b.pdf}
    \caption{Panel description.}\label{fig:six-b}
  \end{subfigure}\hfill
  \begin{subfigure}[t]{0.32\linewidth}
    \plotfile{figures/panel-c.pdf}
    \caption{Panel description.}\label{fig:six-c}
  \end{subfigure}

  \medskip
  \begin{subfigure}[t]{0.32\linewidth}
    \plotfile{figures/panel-d.pdf}
    \caption{Panel description.}\label{fig:six-d}
  \end{subfigure}\hfill
  \begin{subfigure}[t]{0.32\linewidth}
    \plotfile{figures/panel-e.pdf}
    \caption{Panel description.}\label{fig:six-e}
  \end{subfigure}\hfill
  \begin{subfigure}[t]{0.32\linewidth}
    \plotfile{figures/panel-f.pdf}
    \caption{Panel description.}\label{fig:six-f}
  \end{subfigure}
  \caption{Describe your six-panel figure.}\label{fig:six}
\end{figure}
```

If labels are too small in three columns, use two columns over three rows with `0.48\linewidth` panels. Check every axis, title and legend at final size. Do not shrink text to make it fit.

## Tables: captions, rows, columns and numeric alignment

```latex
\begin{table}[htbp]
  \centering
  \caption{Describe your measured comparison.}\label{tab:comparison}
  \begin{tabular}{@{}lrr@{}}
    \toprule
    Setting & Training loss & Validation loss \\
    \midrule
    \multicolumn{3}{c}{\placeholder{Insert measured rows here.}} \\
    \bottomrule
  \end{tabular}
\end{table}
```

`booktabs` supplies professional horizontal rules; avoid vertical rules. `lrr` means one left-aligned text column and two right-aligned numeric columns. `@{}` removes outer padding. Put a table caption above the table and its label after the caption. Reference with `Table~\ref{tab:comparison}` or `\cref{tab:comparison}`.

Replace the `\multicolumn` placeholder line with real measured rows, separating cells with `&` and ending each row with `\\`. This schematic row is a placeholder, not data:

```latex
\placeholder{Setting} & \placeholder{Measured loss} & \placeholder{Measured loss} \\
```

- Add a row by inserting a line before `\bottomrule`; remove one by deleting its line.
- Add a column by adding an alignment letter to `lrr` and a cell to the header and every row. Update `\multicolumn{3}` to the new count if retaining the placeholder.
- Remove a column by removing its alignment letter and its cell, including the adjacent `&`, from every row.
- Use consistent, justified decimal precision within each numeric column. Fixed decimal places let decimals line up in the right-aligned columns without another dependency.
- For long text use `\begin{tabularx}{\linewidth}{@{}Xrr@{}} ... \end{tabularx}` so the first column wraps. Do not scale tables to tiny text.

This pattern supports network size, weight decay, training/validation/test loss and optimiser comparisons; supply your own measured values only.

## Cross-references

```latex
See \cref{ex:1} and \cref{fig:exercise1}.
See Table~\ref{tab:weight-decay}.
See Equation~\eqref{eq:sum}.
```

The last example requires inserting the labelled equation shown above. Labels must be unique: prefixes `ex:`, `fig:`, `tab:` and `eq:` help. Put section labels after `\exercise`, figure/table labels after captions and equation labels inside numbered equations. The existing `\subsection*` headings are unnumbered; reference Exercise 8 as a whole, or use `\subsection{...}\label{...}` if a numbered subsection is needed. Rebuild until references settle; don't type figure numbers by hand.

## Placement, spacing and page breaks

`figure` and `table` are floats. `[htbp]` allows here, top, bottom or a float page; it is a preference, not an exact position. Put source near the first discussion. `[tbp]` favours page edges. Avoid blanket `[H]` placement (`float` is not loaded) or excessive manual breaks.

The `placeins` package adds a float barrier at each numbered exercise, keeping figures within their exercise. Use `\FloatBarrier` manually after a group if needed, but too many barriers waste space. Figures cannot break across pages; split an oversized figure or adjust its exported aspect ratio while retaining legibility.

Use blank lines for paragraphs and the existing spacing first. `\smallskip` and `\medskip` add modest deliberate gaps. Avoid repeated `\\`, negative `\vspace`, tiny fonts or narrower margins. Edit repetitive content before compressing typography.

`\newpage` ends the text page but can leave floats pending. `\clearpage` also flushes figures and tables. **Keep the existing `\clearpage` before Exercise 8 and its `\label{budget:exercise-eight}` attached to the heading.** Exercises 1–7 have no forced page breaks. The short initial placeholders do not reserve fixed space for finished answers.

## Matplotlib export workflow

Generate and analyse your own data in the separately submitted Python file/notebook. Once you already have a plotted Matplotlib `fig`, this export-only snippet saves it; it does not create an experiment or fake data. Adjust the destination relative to the directory where your script runs:

```python
# Existing fig and data come from your own experiment.
from pathlib import Path

output = Path("Coursework/report/figures")  # when running from repository root
output.mkdir(parents=True, exist_ok=True)
fig.savefig(output / "my-plot.pdf", bbox_inches="tight")
# Optional raster alternative:
# fig.savefig(output / "my-plot.png", dpi=300, bbox_inches="tight")
```

Insert with `\plotfile{figures/my-plot.pdf}` inside a figure. PDF preserves vector lines and text. PNG DPI cannot repair undersized fonts. Design plots for final dimensions: the text area is about 16.6 cm wide; half-width panels are about 8 cm wide. Export near the final size, with approximately 11pt axis labels, titles and legends, then check the PDF at 100%. Include units where appropriate and distinguish curves by more than colour. Export composite figures with readable labels in every panel. The template does not modify or run experiment scripts.

## Checking the page limit and submitting

The original submission rules allow **six pages for Exercises 1–7 including figures and tables**, **three additional pages for Exercise 8 including figures**, and **nine pages total**. Exercise 8's own wording also asks for at most two pages of write-up plus figures: aim for at most two pages of prose inside its three-page allocation.

The first-page heading counts towards the first six pages. There is no uncounted appendix. Submit code separately as one `.py` file or notebook, without extensive code listings in the PDF.

After references settle, each build prints this summary in the terminal and `report.log`:

```text
PAGE BUDGET: Exercises 1--7 = .../6; Exercise 8 = .../3; total = .../9.
```

It warns when any limit is exceeded. Counts use the previous compilation's references; run latexmk to completion or rerun pdfLaTeX until rerun warnings disappear. Do not reset page numbers or remove the boundary. To find the summary:

```sh
rg 'PAGE BUDGET|exceed' report.log
# Without ripgrep:
# grep -E 'PAGE BUDGET|exceed' report.log
```

Independently inspect the PDF: if Exercise 8 starts on page P and there are T pages, the main portion uses P−1 pages and the investigation T−P+1. Confirm P−1 ≤ 6, T−P+1 ≤ 3 and T ≤ 9. A PDF viewer shows the total; `pdfinfo report.pdf` also checks total and A4 size if Poppler is installed.

The initial page count does **not guarantee** that your completed content will fit. Before submission check every page for placeholders, missing plots, clipping, readable labels, correct references and accurate captions. Required answers: Exercise 1 a–d; 2 a–d; 3 a–c; 4 a–b; 5 a–c; 6 a–d; 7 a–c. Complete the five Exercise 8 headings with your own investigation. Verify all results are measured and separately check that your code reproduces the figures as required.

## Common LaTeX problems

| Symptom | Remedy |
| --- | --- |
| `Undefined control sequence` | Check spelling and braces; use loaded commands. Address the first error and its source line. |
| `Missing $ inserted` | Put maths in `$...$`; escape prose underscores with `\_`. |
| `Runaway argument` / `File ended while scanning` | Look above the reported line for missing `}` or an unmatched environment. |
| `Extra alignment tab` | Match the row cell count to the column specification; literal ampersands require `\&`. |
| `??` or undefined references | Check unique labels and their position; rebuild until stable. If needed run `latexmk -c`, then rebuild. |
| Figure placeholder remains | Check path, extension, filename case and working directory; add the real file to `figures/`. Missing files are intentionally tolerated. |
| Missing `.sty` package | Check your TeX installation/package manager. The template installs nothing automatically. |
| `Overfull \\hbox` | Inspect the affected line/page. Shorten long text or filenames, use wrapping table columns, or revise figure layout rather than hiding the warning. |
| `Float too large` / distant figures | Split panels, reduce physical height, or relax placement restrictions while keeping text readable. |
| Page-budget warning | Edit repetitive prose or duplicate visuals; improve figure proportions. Preserve readable type and margins. |

Full diagnostics are in `report.log`. Later errors may be consequences of the first error. A clean compilation never replaces visual inspection.
