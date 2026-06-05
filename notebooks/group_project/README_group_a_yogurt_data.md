# Group A — Yogurt Study Data Exploration

## Overview

The Group A dataset comes from a study on the vaginal microbiome. It compares
**26 participants who ate yogurt** to **25 participants who kept their usual
diet** (unchanged diet). Each participant contributed a baseline sample and an
after-antibiotic sample, giving **102 total samples**.

---

## The 4 files and what they contain

| # | File | Rows | What one row represents |
|---|------|------|--------------------------|
| 0 | `00_sample_ids_yogurt.csv` | 102 | One biological sample — links participants to their lab results |
| 1 | `01_participant_metadata_yogurt.csv` | 51 | One participant — demographics, birth control, days since last sex |
| 2 | `02_qpcr_results_yogurt.csv` | 102 | One sample — total bacteria, *L. crispatus*, *L. iners* abundance |
| 3 | `03_luminex_results_yogurt.csv` | 1,020 | One cytokine measurement — 10 cytokines per sample in long format |

### Column details

**File 0 — `sample_ids`**
| Column | Meaning |
|--------|---------|
| `pid` | Participant ID (links to metadata) |
| `time_point` | `baseline` or `after_antibiotic` |
| `arm` | `yogurt` or `unchanged_diet` |
| `sample_id` | Sample code (links to qPCR and Luminex) |

**File 1 — `participant_metadata`**
| Column | Meaning |
|--------|---------|
| `pid` | Participant ID |
| `arm` | `yogurt` (n=26) or `unchanged_diet` (n=25) |
| `days_since_last_sex` | 1–48 days |
| `birth_control` | `no hormonal birth control` or `Depoprovera` |
| `age` | 24–37 years |
| `education` | `less than grade 9`, `grade 10-12, matriculated`, `grade 10-12, not matriculated`, or `post-secondary` |
| `sex` | All `female` |

**File 2 — `qpcr_results`**
| Column | Meaning |
|--------|---------|
| `sample_id` | Sample code |
| `qpcr_bacteria` | Total bacterial load (16S rRNA gene copies) |
| `qpcr_crispatus` | *Lactobacillus crispatus* abundance |
| `qpcr_iners` | *Lactobacillus iners* abundance |

**File 3 — `luminex_results`**
| Column | Meaning |
|--------|---------|
| `sample_id` | Sample code |
| `cytokine` | One of: IL-1a, IL-1b, IL-6, IL-8, IL-10, TNFa, IFN-Y, IP-10, MIG, MIP-3a |
| `conc` | Concentration (pg/mL or similar units) |
| `limits` | `within limits` or `out of range` (below detection) |

---

## How the tables link together

```
sample_ids ←── pid + arm ──→ participant_metadata
    │                               (File 1)
    │
    ├── sample_id ──→ qpcr_results
    │                     (File 2, one-to-one)
    │
    └── sample_id ──→ luminex_results
                          (File 3, one-to-many: 10 rows per sample)
```

**`sample_ids` is the hub** — it's the only table with both `pid` and `sample_id`.

To join everything:
```r
sample_ids %>%
  left_join(participant_metadata, by = c("pid", "arm")) %>%
  left_join(qpcr_results, by = "sample_id") %>%
  left_join(luminex_results, by = "sample_id")
```

---

## Key findings about the data

### 1. No explicit missing values

There are **zero NAs, zero blanks** anywhere in the dataset. But hidden
missingness exists (see point 4 below).

### 2. Distributions are heavily skewed

| Variable | Shape | What to do |
|----------|-------|------------|
| qPCR variables | Very right-skewed (mean >> median, 4 orders of magnitude range) | Use `log10()` transform before analysis |
| `days_since_last_sex` | Right-skewed with 5 extreme values (20–48) | Consider if those are real outliers |

### 3. A data integrity flag in qPCR

In **30 out of 102 samples**, the sum of `qpcr_crispatus` + `qpcr_iners` is
**greater than** `qpcr_bacteria` (total bacteria). This is biologically
impossible and likely points to a qPCR primer efficiency issue — the
species-specific primers amplify more efficiently than the universal 16S
primer. Proceed with caution when comparing across these columns.

### 4. Hidden missingness in cytokines

The `limits` column marks cytokines that were **below the assay's detection
limit**. Their reported `conc` values are imputed floor values (all identical
for a given cytokine), not real measurements.

| Cytokine | % out of range | Usable? |
|----------|---------------|---------|
| IL-1a | 0% | ✅ All real values |
| IP-10 | 3% | ✅ Mostly real, watch extremes |
| IL-8 | 4% | ✅ Mostly real, one sample at 2,500 |
| MIG | 13% | ⚠️ Some imputed values |
| MIP-3a | 24% | ⚠️ A quarter imputed |
| IL-1b | 73% | ⚠️ Mostly imputed |
| IL-6 | 75% | ⚠️ Mostly imputed |
| TNFa | 98% | ❌ Almost all imputed |
| IFN-Y | 99% | ❌ Almost all imputed |
| IL-10 | 100% | ❌ **Entirely imputed — drop this variable** |

For the heavily affected cytokines, consider a **binary analysis**
(detectable / not detectable) rather than using the concentration values.

### 5. L. crispatus is ~65% zeros

Two-thirds of samples have no detected *L. crispatus*. For these samples,
consider modelling presence/absence separately from abundance among those
where it's present.

---

## Files in this folder

| File | Description |
|------|-------------|
| `00_data_dictionary_group_a.qmd` | Quarto document with full data dictionary and R code (has live HTML preview) |
| `links_between_tables.txt` | Plain-text explanation of join relationships |
| `README_group_a_yogurt_data.md` | This file — everything summarised in one place |

---

*Generated: June 2026 — Data exploration for Group A (yogurt study)*