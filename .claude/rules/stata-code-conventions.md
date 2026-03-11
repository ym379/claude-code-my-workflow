---
paths:
  - "code/stata/**/*.do"
  - "code/stata/**/*.ado"
---

# Stata Code Standards

**Standard:** Senior empirical economist quality — reproducible, readable, publication-ready.

---

## 1. File Header (Required on Every .do File)

```stata
/*===========================================================================
  File:     descriptive_name.do
  Project:  Airlines & Data Value
  Author:   [Your Name]
  Date:     YYYY-MM-DD
  Updated:  YYYY-MM-DD

  Purpose:  One-sentence description of what this script does.

  Inputs:   data/processed/input_file.dta
  Outputs:  output/tables/table_X.tex
            data/processed/output_file.dta

  Notes:    Any important caveats, sample restrictions, or dependencies.
===========================================================================*/
```

---

## 2. Script Preamble (Required at Top of Every Script)

```stata
version 17                        // Pin Stata version for reproducibility
set more off
clear all
set seed 20240101                 // YYYYMMDD format — set ONCE at top

// --- Global path macros (relative to project root) ---
global root    ".."               // adjust depending on run location
global data    "$root/data"
global raw     "$root/data/raw"
global proc    "$root/data/processed"
global out_tab "$root/output/tables"
global out_fig "$root/output/figures"
global code    "$root/code/stata"

// --- Logging ---
cap log close
log using "$root/quality_reports/logs/scriptname_YYYYMMDD.log", replace text
```

---

## 3. Variable Naming

- `snake_case` throughout — no `camelCase`, no `ALLCAPS`
- Descriptive names: `log_fare`, `route_hhi`, `carrier_share` — NOT `x1`, `lf`, `y`
- Dummy variables prefixed with `d_`: `d_hub`, `d_treated`, `d_post`
- Interaction terms named explicitly: `d_treated_post` (not `interact`)
- Lag/lead variables: `fare_lag1`, `hhi_lead2`

---

## 4. Regression Standards

### Preferred Estimators
```stata
// High-dimensional fixed effects (primary workhorse)
reghdfe log_fare log_data_invest route_controls, ///
    absorb(route_id quarter_id carrier_id) ///
    cluster(route_id) ///
    noabsorb

// Store estimates immediately
eststo m1
```

### Fixed Effects Documentation
```stata
* Always comment which FE are included and what variation they absorb:
* Route FE: absorbs time-invariant route characteristics
* Quarter FE: absorbs common macro shocks
* Carrier FE: absorbs carrier-level time-invariant heterogeneity
```

### Standard Errors
- Default: cluster at the **route** level (or carrier-route if panel is carrier-route-time)
- Document cluster choice in code comment and paper
- Never use heteroskedasticity-only SEs without justification

---

## 5. Table Output (estout/esttab)

```stata
// Output .tex fragment to output/tables/
esttab m1 m2 m3 using "$out_tab/table_main.tex", ///
    replace booktabs ///
    b(3) se(3) star(* 0.10 ** 0.05 *** 0.01) ///
    label nogaps compress ///
    stats(N r2_a, fmt(%9.0fc %9.3f) labels("Observations" "Adj. R²")) ///
    keep(log_data_invest route_controls) ///
    mtitle("(1)" "(2)" "(3)") ///
    title("Effect of Data Investment on Ticket Fares") ///
    addnotes("Robust standard errors clustered at the route level in parentheses.")
```

**Rules:**
- Always `replace` (never `append` to table files without intention)
- Always use `booktabs` for journal-quality formatting
- Stars: `* 0.10 ** 0.05 *** 0.01` — top-journal convention
- Always include N and R² in stats()
- Always add a note about SE clustering

---

## 6. Data Management

```stata
// Save intermediate datasets as .dta with descriptive names
save "$proc/fares_cleaned_2010_2022.dta", replace

// Never overwrite raw data — always save to processed/
// Always compress before saving
compress
save "$proc/output_file.dta", replace
```

---

## 7. Logging

```stata
// At end of script:
log close
```

- Every analysis run produces a log in `quality_reports/logs/`
- Logs named: `scriptname_YYYYMMDD.log`

---

## 8. Common Pitfalls in Airline Pricing Regressions

| Pitfall | Impact | Prevention |
|---------|--------|------------|
| Directional vs. non-directional routes | 2x sample size difference | Define route consistently (unordered city pair) |
| DB1B fare < $25 or > $2500 | Miscoded tickets bias results | Filter with documented thresholds |
| Codeshare itineraries | Ticketing ≠ operating carrier | Decide on carrier definition; document |
| reghdfe singleton groups | Silently drops observations | Use `keepsingletons` flag consciously |
| Clustered SEs with few clusters | Over-rejection | Check cluster count; use wild bootstrap if N_cluster < 50 |
| Time FE absorbing treatment variation | Zero first stage | Verify treatment varies within time period |
| Interaction terms in esttab | May not appear with `keep()` | List interactions explicitly in keep() |

---

## 9. Code Quality Checklist

```
[ ] version XX at top
[ ] set more off, clear all, set seed
[ ] Global path macros (no hardcoded paths)
[ ] Log file opened and closed
[ ] All variables snake_case with descriptive names
[ ] All FE documented in comments
[ ] Cluster level documented and justified
[ ] esttab output to output/tables/ with booktabs + star convention
[ ] Intermediate data saved to data/processed/
[ ] Script runs clean from top to bottom (no manual intervention needed)
```
