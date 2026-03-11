---
paths:
  - "code/stata/**/*.do"
  - "code/python/**/*.py"
  - "code/R/**/*.R"
---

# Replication-First Protocol

**Core principle:** Replicate original results to the dot BEFORE extending.
Applies to: replicating prior literature for context AND verifying our own earlier results.

---

## Phase 1: Inventory & Baseline

Before writing any code:

- [ ] Read the paper's replication README (if available)
- [ ] Inventory replication package: language, data files, scripts, outputs
- [ ] Record gold-standard numbers from the paper:

```markdown
## Replication Targets: [Author (Year)]

| Target | Table/Figure | Value | SE/CI | Notes |
|--------|-------------|-------|-------|-------|
| Main effect | Table 2, Col 3 | -0.142 | (0.034) | Log fares, clustered route SE |
```

- [ ] Store targets in `quality_reports/replication/YYYY-MM-DD_[paper]_targets.md`

---

## Phase 2: Translate & Execute

- [ ] Follow `stata-code-conventions.md` for all Stata coding standards
- [ ] Follow `r-code-conventions.md` for all R coding standards
- [ ] Translate line-by-line initially — don't "improve" during replication
- [ ] Match original specification exactly (covariates, sample, clustering, SE computation)
- [ ] Save all intermediate results to `data/processed/`

### Stata Pitfalls in Airline Pricing Papers

| Original approach | Our translation | Trap |
|-------------------|----------------|------|
| `areg` with `absorb()` | `reghdfe` with `absorb()` | Check demeaning method matches; singleton treatment differs |
| Cluster at route level | `cluster(route_id)` in reghdfe | Verify route definition (directional vs. non-directional) |
| `ivreg2` IV estimation | `ivreghdfe` | Confirm FE absorbed identically |
| `bootstrap, reps(999)` | `bootstrap, reps(999) seed(XXXX)` | Match seed and reps exactly |
| Log fare as outcome | `gen log_fare = log(fare)` | Verify fare definition matches original (incl. fees?) |

### Python to Stata Pitfalls

| Python | Stata | Trap |
|--------|-------|------|
| `pd.merge(..., how='left')` | `merge m:1` | Left join vs. inner join changes N |
| `groupby().mean()` | `collapse (mean)` | Check if weighted vs. unweighted mean |
| String-based route ID | Numeric encoded route ID | Verify route encoding consistent |

---

## Phase 3: Verify Match

### Tolerance Thresholds

| Type | Tolerance | Rationale |
|------|-----------|-----------|
| Integers (N, route counts) | Exact match | No reason for any difference |
| Point estimates | < 0.005 | Display rounding in paper |
| Standard errors | < 0.01 | Minor clustering variation |
| P-values | Same significance level | Exact p may differ |
| Percentages | < 0.1pp | Display rounding |

### If Mismatch

**Do NOT proceed to extensions.** Investigate systematically:
1. Sample size mismatch → check data construction and merge steps
2. Point estimate mismatch → check variable definitions and specification
3. SE mismatch → check cluster definition and Stata version (small-sample corrections differ)
4. Document investigation even if unresolved

### Replication Report

Save to `quality_reports/replication/YYYY-MM-DD_[paper]_report.md`:

```markdown
# Replication Report: [Author (Year)]
**Date:** [YYYY-MM-DD]
**Original language:** [Stata/R/Python]
**Our implementation:** [script path]

## Summary
- **Targets checked / Passed / Failed:** N / M / K
- **Overall:** [REPLICATED / PARTIAL / FAILED]

## Results Comparison

| Target | Paper | Ours | Diff | Status |
|--------|-------|------|------|--------|

## Discrepancies (if any)
- **Target:** X | **Investigation:** ... | **Resolution:** ...

## Environment
- Stata version, key packages (with versions), data source
```

---

## Phase 4: Only Then Extend

After replication is verified (all targets PASS or documented discrepancies explained):

- [ ] Commit replication script: "Replicate [Paper] Table X — all targets match"
- [ ] Now extend with our modifications (additional controls, subsamples, alternative specifications)
- [ ] Each extension builds on the verified baseline
