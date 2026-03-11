---
paths:
  - "**/*.R"
  - "code/R/**/*.R"
  - "scripts/**/*.R"
---

# R Code Standards

**Standard:** Senior empirical economist quality — publication-ready figures for top finance/econ journals.

---

## 1. Reproducibility

- `set.seed()` called ONCE at top (YYYYMMDD format)
- All packages loaded at top via `library()` (not `require()`)
- All paths relative to repository root via `here::here()` or explicit relative paths
- `dir.create(..., recursive = TRUE)` for output directories

---

## 2. Function Design

- `snake_case` naming, verb-noun pattern
- Roxygen-style documentation
- Default parameters, no magic numbers
- Named return values (lists or tibbles)

---

## 3. Domain Correctness

- Verify figure data matches numbers in corresponding Stata regression output
- Check axis labels use correct variable names and units (fares in USD, not log-USD)
- Confirm sample sizes in figures match paper's data section

---

## 4. Visual Identity (Journal-Ready)

```r
# --- Journal-appropriate palette ---
# Primary: black/white/gray for print compatibility
# Accent: one muted color for differentiation
col_main    <- "#1a1a1a"   # Near-black (primary series)
col_accent  <- "#2563eb"   # Muted blue (secondary series / highlights)
col_gray    <- "#6b7280"   # Medium gray (confidence bands, reference lines)
col_light   <- "#e5e7eb"   # Light gray (shading, backgrounds)
col_pos     <- "#15803d"   # Green (positive effects)
col_neg     <- "#b91c1c"   # Red (negative effects)
```

### Custom Theme
```r
theme_paper <- function(base_size = 11) {
  theme_minimal(base_size = base_size) +
    theme(
      plot.title       = element_text(face = "bold", size = base_size + 1),
      plot.subtitle    = element_text(color = col_gray, size = base_size - 1),
      axis.title       = element_text(size = base_size),
      axis.text        = element_text(color = col_main),
      panel.grid.minor = element_blank(),
      panel.grid.major = element_line(color = col_light, linewidth = 0.4),
      legend.position  = "bottom",
      legend.key.size  = unit(0.8, "lines"),
      strip.text       = element_text(face = "bold")
    )
}
```

### Figure Dimensions for Journal Submission
```r
# Single-column figure (3.5" in most journals)
ggsave(filepath, width = 3.5, height = 2.8, units = "in", dpi = 300, bg = "white")

# Full-width figure (7" in most journals)
ggsave(filepath, width = 7.0, height = 4.0, units = "in", dpi = 300, bg = "white")

# Wide panel figure (e.g., event study with multiple outcomes)
ggsave(filepath, width = 7.0, height = 3.5, units = "in", dpi = 300, bg = "white")
```

**Output path:** Always save to `output/figures/` (never to code/ or root)

---

## 5. RDS Data Pattern

**Load pre-computed Stata/Python estimates; do not re-run heavy estimation in R.**

```r
# Load estimates saved from Stata/Python
coef_data <- readRDS(file.path("output", "rds", "main_estimates.rds"))

# Save any R-computed intermediate objects
saveRDS(plot_data, file.path("output", "rds", "event_study_data.rds"))
```

---

## 6. Common Pitfalls in Airline Pricing Figures

| Pitfall | Impact | Prevention |
|---------|--------|------------|
| Plotting log fares without noting units | Misleading axis | Label as "Log Fare (USD)" |
| Event study with unbalanced panel | Noisy pre-trend estimates | Check event-time cell counts |
| Too many series in one plot | Unreadable | Max 4-5 series; use facets for more |
| Missing `bg = "white"` for PDFs | Transparent bg in some viewers | Always specify `bg = "white"` for journal submission |
| Hardcoded paths | Breaks on other machines | Use relative paths from project root |

---

## 7. Line Length

**Standard:** Keep lines <= 100 characters.

**Exception:** Mathematical formula implementations may exceed 100 chars if an inline comment explains the operation.

---

## 8. Code Quality Checklist

```
[ ] Packages at top via library()
[ ] set.seed() once at top (YYYYMMDD)
[ ] All paths relative to project root
[ ] theme_paper() applied to all figures
[ ] Output to output/figures/ with explicit width/height/dpi/bg
[ ] Heavy computations loaded from RDS (not re-run)
[ ] Figures legible in grayscale (for print)
[ ] Comments explain WHY not WHAT
```
