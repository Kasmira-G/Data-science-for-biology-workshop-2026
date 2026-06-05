# Session Log — Group A Yogurt Study

> Research question: *How does eating yogurt (vs. an unchanged diet) affect the vaginal microbiome after antibiotic treatment?*

---

## Day 1 — 2026-06-04

### Step 1 — Data exploration

We read all four CSV files for **Group A (yogurt study)**:

| File | Contents | Rows | Key columns |
|------|----------|------|-------------|
| `00_sample_ids_yogurt.csv` | Links participants to samples | 102 | `pid`, `sample_id`, `time_point`, `arm` |
| `01_participant_metadata_yogurt.csv` | Demographics | 51 | `age`, `birth_control`, `education`, `days_since_last_sex` |
| `02_qpcr_results_yogurt.csv` | Bacterial abundance (qPCR) | 102 | `qpcr_bacteria`, `qpcr_crispatus`, `qpcr_iners` |
| `03_luminex_results_yogurt.csv` | Immune markers (Luminex) | 1020 | `cytokine`, `conc`, `limits` |

The study design:
- 51 women, split into **yogurt** (n=26) and **unchanged diet** (n=25)
- Each participant sampled at **baseline** and **after antibiotic**
- qPCR measures: total bacteria, *L. crispatus*, *L. iners*
- Luminex measures: 10 cytokines / chemokines

---

### Step 2 — Table 1 (demographics by arm)

Added a **Table 1** section to `00_data_dictionary_group_a.qmd` using `gtsummary`.

```r
metadata |>
  select(-pid, -sex) |>
  tbl_summary(
    by = arm,
    statistic = list(age ~ "{mean} ({sd})")  # override default median (IQR) for age
  ) |>
  add_p(
    test = list(age ~ "t.test")              # override default Wilcoxon for age
  )
```

- Age shows as **Mean (SD)** with a **t-test** p-value
- Other variables use gtsummary defaults
- Exported as `output/table-1.html`

---

### Step 3 — Figure 1 (birth control by arm)

Added a **bar plot** of birth control use by study arm using `ggpubr::ggbarplot()`.

```r
chisq <- chisq.test(table(metadata$arm, metadata$birth_control))
```

- Uses a **chi-squared test** (not Wilcoxon) because birth control is categorical
- Annotated directly on the plot
- Exported as `output/figure-1.pdf`

---

### Step 4 — Figure 2 (qPCR faceted boxplots)

Added a new **Research Question** section with faceted boxplots showing all three bacterial measures.

**Version 1** — `ggpubr::ggboxplot()` with `stat_compare_means()`, orange/blue colours.

**Version 2 (final)** — custom `ggplot2` code with:
- **Pink** (`#D81B60`) for unchanged diet
- **Purple** (`#7B1FA2`) for yogurt
- Boxplots + jittered points (no paired lines — user preferred unpaired)
- Wilcoxon p-values computed manually and displayed as text labels above each comparison
- Log10 y-axis to handle the wide range of values
- Faceted by bacterium (total bacteria, *L. crispatus*, *L. iners*)
- Exported as `output/figure-2.pdf`

```r
arm_colors <- c("unchanged_diet" = "#D81B60", "yogurt" = "#7B1FA2")

ggplot(qpcr_long, aes(x = time_point, y = abundance)) +
  geom_boxplot(aes(fill = arm), alpha = 0.5, ...) +
  geom_point(aes(color = arm), position = position_dodge(...), ...) +
  facet_wrap(~ bacterium, scales = "free_y") +
  scale_fill_manual(values = arm_colors) +
  scale_color_manual(values = arm_colors, guide = "none") +
  scale_y_log10(labels = comma) +
  geom_text(data = pvals, aes(x = time_point, y = Inf, label = label), ...) +
  theme_bw()
```

---

### Step 5 — Total bacteria bar plot

Generated an additional bar plot (not added to the notebook) showing **mean ± SE** of total bacterial DNA at each time point.

| Time point | Unchanged diet | Yogurt | t-test p |
|---|---|---|---|
| Baseline | 16.4M | 19.5M | 0.65 (ns) |
| After antibiotic | 927K | 565K | 0.37 (ns) |

Key observation: Both arms show a **dramatic drop** after antibiotics (~20-30× less bacteria), but the difference between yogurt and unchanged diet is **not statistically significant** at either time point.

Exported as `output/total_bacteria_bar.pdf` and `output/total_bacteria_bar.png`.

---

## Day 2 — 2026-06-05

### Step 6 — Linear model adjusting for baseline

Added a **Linear model — adjusting for baseline** section to the data dictionary
notebook at `00_data_dictionary_group_a.qmd`. Fits three `lm()` models (one per
bacterial outcome) using after-antibiotic levels as the outcome and `arm` +
baseline level as predictors. Coefficient tables generated with
`gtsummary::tbl_regression()`.

**Key concepts explained to the user (beginner-friendly):**
- Linear models answer: *"After antibiotics, is yogurt different from unchanged
  diet — adjusting for where each person started at baseline?"*
- This is better than separate Wilcoxon tests because it uses all the info
  (same people measured twice), accounts for individual differences, and has
  more statistical power.
- How to read `tbl_regression()` output: Beta column, 95% CI, p-value.
- What "adjusting for baseline" means — controlling for each person's starting point.

**Results:**

| Outcome | Yogurt Beta | 95% CI | p-value | Plain English |
|---|---|---|---|---|
| Total bacteria (log) | −0.37 | −0.77, 0.02 | 0.061 | Yogurt: slightly lower — borderline |
| *L. crispatus* (log) | +0.82 | −0.40, 2.0 | 0.2 | Yogurt: somewhat higher, very uncertain |
| *L. iners* (log) | −0.17 | −0.78, 0.44 | 0.6 | No meaningful difference |

None reached p < 0.05. Total bacteria is borderline (p = 0.061), suggesting a
potential yogurt effect that a larger study might confirm.

**Notebook rendered** to HTML successfully — all model code chunks run without errors.

---

### Step 7 — Export regression tables as standalone HTML

Exported the three regression model tables as standalone HTML files so they can
be viewed in a browser and printed to PDF:

| File | Content |
|---|---|
| `output/regression-table.html` | All 3 models side-by-side in a merged table |
| `output/regression-total-bacteria.html` | Total bacteria model only |
| `output/regression-crispatus.html` | *L. crispatus* model only |
| `output/regression-iners.html` | *L. iners* model only |

---

### Step 8 — PowerPoint presentation

Created `output/yogurt-study-presentation.pptx` with 8 slides:

| Slide | Content |
|---|---|
| 1 — Title | "Effect of Yogurt on the Vaginal Microbiome After Antibiotic Treatment" |
| 2 — Research question | Study design (51 women, 2 groups, 2 time points, 3 outcomes) |
| 3 — Table 1 | Participant characteristics by arm (gtsummary → flextable) |
| 4 — Figure 2 | qPCR faceted boxplots with pink/purple colours |
| 5 — Model 1 | Total bacteria coefficient table + plain-English interpretation |
| 6 — Model 2 | *L. crispatus* coefficient table + interpretation |
| 7 — Model 3 | *L. iners* coefficient table + interpretation |
| 8 — Summary | Key findings at a glance |

Presentation built with `officer` R package, embedding gtsummary tables as
flextable objects and Figure 2 as a PNG image.

---

### Step 9 — Presentation speaker script

Created `presentation-script.md` — a detailed speaker notes document for every
slide, with:

- **Plain-language explanations** for each figure and table
- **How to walk through coefficient tables** step by step (Beta, CI, p-value)
- **"If someone asks…"** boxes** answering common audience questions:
  - *"Why is the linear model better than the boxplot?"*
  - *"What does 'adjusting for baseline' mean?"* (with concrete analogy)
  - *"So yogurt doesn't help?"*
  - *"Why a log scale?"*
  - *"What if p-values are high in Table 1?"*
- **Tips table** (dos and don'ts) at the end for presenting confidently

---

## Key findings summary

1. **Randomisation worked** — the two arms are well-balanced on age, birth control, education, and baseline bacterial levels (all p-values > 0.05).
2. **Antibiotics work** — total bacterial load drops ~20-30× from baseline in both groups.
3. **No clear yogurt effect** — after antibiotics, the yogurt group had slightly lower total bacteria (565K vs 927K) and slightly higher *L. crispatus* (approached significance at p = 0.076), but neither difference reached p < 0.05.
4. **Linear models with baseline adjustment** confirmed the same pattern — total bacteria was borderline (p = 0.061), crispatus (p = 0.2) and iners (p = 0.6) were not significant.
5. **The research question remains open** — a larger study might detect a real effect that this sample size was too small to confirm.

---

## Generated output files

All files in `notebooks/group_project/output/`:

| File | Description |
|------|-------------|
| `table-1.html` | Table 1 — participant characteristics by study arm |
| `figure-1.pdf` | Bar plot of birth control by arm with chi-squared p-value |
| `figure-2.pdf` | Faceted qPCR boxplots (pink/purple, unpaired, with p-values) |
| `figure-2.png` | Preview version of Figure 2 |
| `total_bacteria_bar.pdf` | Total bacteria mean ± SE bar plot (not in notebook) |
| `total_bacteria_bar.png` | Preview version of total bacteria bar plot |
| `regression-table.html` | All 3 regression models side-by-side |
| `regression-total-bacteria.html` | Total bacteria model table |
| `regression-crispatus.html` | *L. crispatus* model table |
| `regression-iners.html` | *L. iners* model table |
| `yogurt-study-presentation.pptx` | PowerPoint presentation (8 slides) |
| `presentation-script.md` | Speaker notes script for every slide |