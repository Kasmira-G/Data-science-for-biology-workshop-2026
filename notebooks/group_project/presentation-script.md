# Presentation Script — Yogurt Study

> Speaker notes for `yogurt-study-presentation.pptx`
> Written for a beginner presenter — explain simply, no jargon without defining it first.

---

## Slide 1 — Title Slide

**On-screen text:** "Effect of Yogurt on the Vaginal Microbiome After Antibiotic Treatment"

### What to say

> "Good morning/afternoon everyone. Today I'll be presenting our group's analysis
> of how eating yogurt affects the vaginal microbiome after antibiotic treatment.
> 
> This was a study of 51 women — 26 who ate yogurt daily and 25 who kept their
> usual diet. Each participant provided a sample before and after a course of
> antibiotics. We measured three things: total bacterial load, and the abundance
> of two specific bacteria — *Lactobacillus crispatus* and *Lactobacillus iners*."

---

## Slide 2 — Research Question

**On-screen text:** Study design details (51 women, 2 groups, 2 time points, 3 outcomes)

### What to say

> "Our research question was: does eating yogurt help the vaginal microbiome
> recover better after antibiotics compared to just keeping your usual diet?
> 
> The study was set up like this: 51 women were split into two groups.
> The yogurt group — 26 women — ate yogurt daily. The unchanged diet group —
> 25 women — didn't change what they normally ate.
> 
> Each woman gave a sample at baseline — that's before antibiotics — and another
> sample after finishing their antibiotic course. So we have 102 samples total.
> 
> We looked at three things in those samples:
> - **Total bacteria** — the overall bacterial load
> - ***L. crispatus*** — a 'good' bacterium linked to vaginal health
> - ***L. iners*** — another common species that can be either protective or harmful
> 
> We used qPCR, which is a lab technique that measures DNA, to count how many
> of each bacterium were present."

### Optional extra (if someone asks why these bacteria)

> "*L. crispatus* is considered a marker of a healthy vaginal microbiome.
> When it's abundant, it produces lactic acid which keeps harmful bacteria in
> check. *L. iners* is more of a mixed bag — it can also produce lactic acid,
> but it's often associated with a less stable microbiome."

---

## Slide 3 — Table 1: Participant Characteristics

**On-screen text:** gtsummary table comparing yogurt vs unchanged diet on age, birth control, education, days since last sex

### What to say

> "This is what we call a **Table 1** — it's the standard way to start a
> medical paper. It shows the characteristics of participants in each group
> side by side, so you can check whether the two groups are similar.
> 
> Let's look at each row:
> 
> **Age:** The yogurt group had an average age of about 30, and the unchanged
> diet group about 29. The p-value here is 0.4 — that's well above 0.05,
> meaning there's no significant difference. The randomisation worked for age.
> 
> **Birth control:** About 35% of the yogurt group used Depoprovera compared
> to about 28% of the unchanged diet group. The p-value is 0.6 — again, no
> significant difference.
> 
> **Education:** The groups look similar across education levels, and the
> p-value is 0.6 — not significant.
> 
> **Days since last sex:** Both groups have similar medians and ranges.
> P-value is 0.9 — basically identical.
> 
> **The take-home message here is:** randomisation worked well. The two groups
> were well-balanced on all these characteristics, so any difference we see
> later in bacterial levels is more likely due to the yogurt itself, not to
> some pre-existing difference between the groups."

### If someone asks about p-values in Table 1

> "In a Table 1, we actually *want* the p-values to be high (above 0.05).
> That means the groups are similar. A low p-value here would actually be
> bad news — it would mean the groups were different at the start, making
> it harder to interpret the results."

---

## Slide 4 — Figure 2: qPCR Boxplots

**On-screen text:** Faceted boxplots — 3 panels (total bacteria, L. crispatus, L. iners), each with baseline and after-antibiotic, pink vs purple boxes

### What to say

> "Now let's look at the actual bacterial measurements. This figure shows
> three panels — one for each type of bacteria we measured.
> 
> **How to read this plot:** Each panel shows the data at baseline on the left
> and after antibiotics on the right. The pink boxes are the unchanged diet
> group, and the purple boxes are the yogurt group. The y-axis is on a log
> scale — that means each step up is a 10-fold increase, which helps us see
> the data clearly since the values range from very small to very large.
> 
> The p-values shown above each pair of boxes come from a **Wilcoxon rank-sum
> test** — that's a statistical test that compares two groups without assuming
> the data follows a normal distribution.
> 
> **Let's walk through each panel:**
> 
> **Total bacteria (left panel):** At baseline, both groups look similar —
> p-value is not significant. After antibiotics, both groups show a dramatic
> drop — you can see the boxes are much lower. The difference between yogurt
> and unchanged diet after antibiotics is also not significant (p is marked
> 'ns').
> 
> ***L. crispatus* (middle panel):** This is the interesting one. At baseline,
> the groups look similar. After antibiotics, the yogurt group's boxes are a
> bit higher than the unchanged diet group. The p-value here was about 0.076
> — it's approaching significance but didn't quite reach the traditional 0.05
> cutoff. So there's a hint that yogurt might help preserve or restore
> *L. crispatus*, but we can't be sure with this sample size.
> 
> ***L. iners* (right panel):** Similar story — big drop after antibiotics,
> but no real difference between the groups.
> 
> **The big picture from this figure:** Antibiotics wipe out a lot of bacteria.
> There's a suggestion that yogurt might help *L. crispatus* recover better,
> but it's not statistically significant."

### Explaining the log scale (if someone looks confused)

> "You might wonder why the y-axis says 'log10'. That's because the raw
> numbers range from about 1,000 to 100 million gene copies. If we plotted
> that on a normal scale, everything would be squished at the bottom. A log
> scale spreads the data out so we can actually see the patterns.
> On a log scale, going from 1 to 2 means a 10-fold increase, not a doubling."

---

## Slide 5 — Linear Model: Total Bacteria

**On-screen text:** Coefficient table from tbl_regression() for total bacteria model, with interpretation text below

### What to say

> "Now we move to the linear models. This is where we get a more precise answer
> than the boxplots alone could give us.
> 
> **Why do we need a model?** The boxplots compared yogurt vs unchanged diet
> separately at each time point. But we know the same people were measured
> twice — each person's baseline level predicts their after-antibiotic level.
> A linear model lets us account for that.
> 
> **What this model does:** It predicts the after-antibiotic bacterial level
> using two things: the study arm (yogurt or unchanged diet) AND the person's
> baseline level. The formula is:
> 
> > `after_antibiotic ~ arm + baseline`
> 
> **How to read this table:**"
>
> *(Point to each row as you explain)*
>
> "The table has four columns:
> - **Characteristic** — the predictor
> - **Beta** — the estimated effect size
> - **95% CI** — the confidence interval (the range of plausible values)
> - **p-value** — how likely this result is if there's really no effect
>
> **The row we care about is 'arm: yogurt'.** The unchanged diet group is
> the reference — it's shown as '—' meaning it's the baseline for comparison.
>
> The Beta for yogurt is **-0.37**. That means the yogurt group had about
> 0.37 log-units *less* total bacteria after antibiotics compared to the
> unchanged diet group, after adjusting for where each person started at
> baseline.
>
> The 95% confidence interval goes from -0.77 to 0.02. Notice that this
> interval *almost* doesn't include zero — it goes up to 0.02, which is
> just barely past zero.
>
> The p-value is **0.061**. That's above the traditional cutoff of 0.05,
> so it's not technically 'statistically significant'. But it's close.
> In scientific terms, we'd say this is a **borderline result** — it hints
> that yogurt might reduce bacterial load after antibiotics, but we'd need
> a larger study to be sure.
>
> The other row in the table is 'log_bacteria_baseline' with a Beta of 0.55
> and a p-value of 0.007. This just tells us something we already know —
> people who start with more bacteria tend to end with more bacteria after
> antibiotics. That's why we include it in the model — it improves our
> estimate of the yogurt effect."

### If someone asks: "Why is this better than the boxplot?"

> "Great question. In the boxplot, the after-antibiotic comparison for total
> bacteria showed a p-value that wasn't close to significant. But when we
> adjusted for baseline in the linear model, the p-value dropped to 0.061.
> That's because the model accounts for the fact that some people naturally
> have higher or lower bacterial levels. It's like comparing people against
> *their own starting point* rather than just comparing group averages.
> This gives us more statistical power to detect a real effect."

### If someone asks: "What does 'adjusting for baseline' mean?"

> "Imagine two women in the study. One started with very high bacteria at
> baseline, the other with very low. After antibiotics, the first woman still
> has higher bacteria than the second — but that's mostly because she started
> higher, not because of the yogurt. The model mathematically accounts for
> this by including the baseline value. It answers the question: 'If two
> women had the same baseline level, what difference would we expect from
> yogurt alone?'"

---

## Slide 6 — Linear Model: *L. crispatus*

**On-screen text:** Coefficient table for L. crispatus model, with interpretation text below

### What to say

> "Now let's look at *L. crispatus* — the 'good' bacterium we care about most.
> 
> The model is the same structure: we predict the after-antibiotic level of
> *L. crispatus* using the study arm and the baseline level.
> 
> Looking at the 'arm: yogurt' row: the Beta is **+0.82**. That's a *positive*
> number, meaning the yogurt group had *higher* levels of *L. crispatus*
> after antibiotics compared to the unchanged diet group. That's in the
> direction we'd hope for if yogurt helps.
> 
> But look at the confidence interval: it goes from **-0.40 to 2.0**. That's
> a very wide range — it includes both negative and positive values. This
> tells us we're very uncertain about the true effect size. It could be that
> yogurt has a moderately negative effect, a moderately positive effect, or
> no effect at all.
> 
> The p-value is **0.2** — well above 0.05. So this result is not
> statistically significant.
> 
> **Why is the confidence interval so wide?** Because *L. crispatus* had a
> lot of zeros — remember from the data exploration that about two-thirds
> of samples had no detected *L. crispatus*. When a lot of values are zero,
> there's less information in the data, and the model has a harder time
> making precise estimates.
> 
> Also interesting: the baseline coefficient is -0.30 with a p-value of 0.6
> — meaning baseline *L. crispatus* doesn't even predict after-antibiotic
> levels well. This makes sense because antibiotics are so disruptive that
> they reset the microbiome regardless of where you started."

### If someone asks: "So yogurt doesn't help *L. crispatus*?"

> "That's the cautious interpretation — we can't say it helps based on this
> data alone. But the direction of the effect is positive (+0.82), which is
> consistent with the hint we saw in the boxplots (p = 0.076). So it's
> possible that a larger study would detect a significant effect. We just
> can't conclude that from this study."

---

## Slide 7 — Linear Model: *L. iners*

**On-screen text:** Coefficient table for L. iners model, with interpretation text below

### What to say

> "Finally, *L. iners*. This one is straightforward.
> 
> The Beta for yogurt is **-0.17** — a very small negative number, meaning
> essentially no difference between the groups.
> 
> The confidence interval goes from -0.78 to 0.44 — it easily includes zero.
> 
> The p-value is **0.6** — not even close to significant.
> 
> And the baseline coefficient is -0.03 with p = 0.8 — so baseline doesn't
> predict after-antibiotic levels here either.
> 
> **Conclusion:** Yogurt doesn't appear to have any meaningful effect on
> *L. iners* levels after antibiotics. This isn't surprising — *L. iners*
> is a very common and resilient species that seems to respond primarily
> to the antibiotic itself, not to dietary interventions."

---

## Slide 8 — Summary

**On-screen text:** Bullet points of key findings

### What to say

> "Let me bring everything together.
> 
> **First, the good news:** The randomisation worked. Our Table 1 showed the
> two groups were well-balanced, so any differences we see are likely due
> to the yogurt and not to pre-existing differences between the groups.
> 
> **Second, antibiotics work:** Both groups showed a dramatic drop in total
> bacterial load after antibiotics — about 20 to 30 times less bacteria.
> 
> **Third, the yogurt effect:** None of our three linear models found a
> statistically significant effect of yogurt at the p < 0.05 level. But the
> pattern is worth noting:
> 
> - **Total bacteria:** the yogurt group had lower levels after antibiotics
>   (p = 0.061 — borderline). This is the most promising finding.
> - ***L. crispatus:*** the yogurt group had higher levels, but the estimate
>   was very uncertain (p = 0.2).
> - ***L. iners:*** no difference at all (p = 0.6).
> 
> **So what does this mean?** The evidence is suggestive but not conclusive.
> There are hints that yogurt might help reduce total bacterial load and
> potentially support *L. crispatus* recovery after antibiotics. But the
> study was relatively small — 51 participants — and may not have had enough
> statistical power to detect a real but modest effect.
> 
> **For future research:** A larger study would be needed to confirm these
> findings. It might also be interesting to look at other outcomes — like
> the immune markers from the Luminex data, or to analyse *L. crispatus*
> as a binary outcome (present vs absent) since so many samples had zero."

### Optional: Transition to Q&A

> "I'm happy to take questions. Some things we could discuss further:
> - How we might improve the study design
> - Whether a different statistical approach (like a mixed model) might be better
> - How these results compare to other studies on probiotics and the microbiome"

---

## Tips for presenting

| Do | Don't |
|---|---|
| Point at the specific row/number you're talking about | Read every number on the slide |
| Pause after saying a key number | Rush through the p-values |
| Say "not statistically significant" instead of "not significant" | Say "the model failed" or "nothing was significant" |
| Use plain English after giving the numbers | Assume everyone knows what a confidence interval is |
| Make eye contact with the audience | Turn your back to read the slide |
