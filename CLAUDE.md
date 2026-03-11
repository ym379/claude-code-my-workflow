# CLAUDE.md — Airlines & Data Value
**Project:** Airlines & Data Value
**Institution:** Wharton School, University of Pennsylvania
**Branch:** main

---

## Core Principles

- **Plan first** — enter plan mode before non-trivial tasks; save plans to `quality_reports/plans/`
- **Verify after** — compile/run and confirm output at the end of every task
- **Single source of truth** — `paper/` LaTeX is authoritative; output tables/figures derive from code
- **Data immutability** — `data/raw/` is never modified; all transformations go to `data/processed/`
- **Quality gates** — nothing ships below 80/100
- **[LEARN] tags** — when corrected, save `[LEARN:category] wrong → right` to MEMORY.md

---

## Folder Structure

```
my-project/
├── CLAUDE.md                    # This file
├── .claude/                     # Rules, skills, agents, hooks
├── Bibliography_base.bib        # Centralized bibliography
├── paper/                       # LaTeX manuscript (.tex, .bbl, .pdf)
├── data/
│   ├── raw/                     # Original data — NEVER modified
│   └── processed/               # Cleaned, analysis-ready datasets
├── code/
│   ├── stata/                   # .do files (primary analysis & estimation)
│   ├── python/                  # .py scripts (data pipeline, cleaning, NLP)
│   └── R/                       # .R scripts (publication-quality figures)
├── output/
│   ├── tables/                  # Auto-generated .tex table fragments
│   └── figures/                 # Auto-generated .pdf / .png figures
├── quality_reports/             # Plans, session logs, replication reports
├── explorations/                # Research sandbox
├── templates/                   # Session log, quality report templates
├── master_supporting_docs/      # Related papers, data documentation
└── Slides/                      # Conference/seminar presentation decks
```

---

## Commands

```bash
# LaTeX paper (3-pass pdflatex + bibtex)
cd paper && pdflatex -interaction=nonstopmode manuscript.tex
bibtex manuscript
pdflatex -interaction=nonstopmode manuscript.tex
pdflatex -interaction=nonstopmode manuscript.tex

# Stata batch run (macOS)
stata -b do code/stata/main.do

# Python script
python code/python/script_name.py

# R figure generation
Rscript code/R/figures.R

# Quality score (if Quarto slides exist)
python scripts/quality_score.py Quarto/file.qmd
```

---

## Quality Thresholds

| Score | Gate | Meaning |
|-------|------|---------|
| 80 | Commit | Good enough to save |
| 90 | PR | Ready for submission draft |
| 95 | Excellence | Aspirational — referee-ready |

---

## Skills Quick Reference

| Command | What It Does |
|---------|-------------|
| `/compile-latex [file]` | 3-pass pdflatex + bibtex |
| `/review-paper [file]` | Full manuscript review |
| `/review-r [file]` | R code quality review |
| `/data-analysis [dataset]` | End-to-end analysis workflow |
| `/lit-review [topic]` | Literature search + synthesis |
| `/research-ideation [topic]` | Research questions + strategies |
| `/interview-me [topic]` | Interactive research interview |
| `/validate-bib` | Cross-reference citations |
| `/proofread [file]` | Grammar/typo/clarity review |
| `/commit [msg]` | Stage, commit, PR, merge |
| `/deep-audit` | Repository-wide consistency audit |
| `/learn [skill-name]` | Extract discovery into persistent skill |
| `/context-status` | Show session health + context usage |
| `/create-lecture` | Conference slide deck (Beamer) |
| `/compile-latex [file]` | Compile Beamer presentation |

---

## LaTeX Custom Commands (Paper)

| Command | Effect | Use Case |
|---------|--------|----------|
| *(add as you define them)* | | |

## Stata Table Conventions

| Convention | Standard | Notes |
|------------|----------|-------|
| Table engine | `esttab` / `estout` | Always output `.tex` fragment to `output/tables/` |
| Significance stars | `* 0.10 ** 0.05 *** 0.01` | Top-journal standard |
| Standard errors | Clustered (route or carrier) | Document cluster level in table note |
| Fixed effects | Absorbed via `reghdfe` | Note FE structure in table header |

---

## Current Paper State

| Section | File | Status | Notes |
|---------|------|--------|-------|
| Manuscript | `paper/manuscript.tex` | Not started | Main LaTeX file |
| Main analysis | `code/stata/main.do` | Not started | Core regressions |
| Data pipeline | `code/python/build_data.py` | Not started | Raw → processed |
| Main figures | `code/R/figures.R` | Not started | ggplot2 output |
