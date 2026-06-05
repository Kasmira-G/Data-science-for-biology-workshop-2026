# Group Project — Session 1: Get to know your dataset

This session is about exploring the dataset your group has been assigned.
You're not analyzing it yet — you're building a mental map of what's in it.
Open the CSVs in `data/group_a_yogurt/` or `data/group_b_menstruation/`,
read them into R, and look around. Use Pi to help you when something is
unclear.

## Questions to answer as a group

- How many tables are there in your dataset, and what does each one contain? 
There are four tables, it contains the sample_ids, the participant_metadata, qPCR_results, luminex_results
- For each table, what does **one row** represent? (the unit of observation)
Table one, each row represents one biological samples
Table two, each row represents one participant
Table three, each row represents one samples bacterial DNA
Table four, each row represents one cytokine x one sample
- For each table, what does **each column** mean? Which columns are
  categorical, which are numeric, which are identifiers?
Table one, there are 4 columns (PID - identifier), time-point (baseline / after_antibiotic) and arm (2 levels) are categorical
Table two, there are 7 columns (PID - identifier), arm (categorical), birth_control (categorical), education (categorical), sex (categorical), days_since_last_sex (numerical), age (numeric)
Table three, there are 4 columns, (SID - identifier), qpcr_bacteria: total bacteria (numerical), qpcr_crispatus: L. crispatus (numerical), L. iners (numerical)
Table four, there are 4 columns, (SID - identifier), cytokine name (IL-1a, IL-10, IL-1b, IL-8, IL-6, TNFa, IP-10, MIG, IFN-Y, MIP-3a), conc concentration (numerical), limits (categorical)  
- How are the tables linked to each other? What column(s) would you use to join them?
They are connected through two key columns:
  pid        — a participant ID (e.g. pid_01, pid_02, ...)
  sample_id  — a unique code for each biological sample

Here is how they fit together:

  File 0: sample_ids  (<-- hub -- every other file joins to this one)
    Columns: pid, time_point, arm, sample_id
    Has BOTH pid and sample_id, so it acts as the bridge.

  File 1: participant_metadata
    Columns: pid, arm, days_since_last_sex, birth_control, age, education, sex
    Joined TO sample_ids USING: pid, arm
    → This adds participant demographics to each sample.

  File 2: qpcr_results
    Columns: sample_id, qpcr_bacteria, qpcr_crispatus, qpcr_iners
    Joined TO sample_ids USING: sample_id
    → One row per sample, so this is a one-to-one join.

  File 3: luminex_results
    Columns: sample_id, cytokine, conc, limits
    Joined TO sample_ids USING: sample_id
    → 10 rows per sample (one per cytokine), so this is a one-to-many join.

To join everything into one big table, start from sample_ids and work outward:

  sample_ids %>%
    left_join(participant_metadata, by = c("pid", "arm")) %>%
    left_join(qpcr_results, by = "sample_id") %>%
    left_join(luminex_results, by = "sample_id")

- How many participants are in the study? How many observations per
  participant?
The data tells me there are 51 unique participants. Each participant has 2 observations (samples) in the sample_ids file. The participant_metadata file has 51 participant. Arms: 25 in unchanged_diet, 26 in yogurt

 51 participants — 26 in the yogurt arm, 25 in the unchanged diet arm. 
 2 observations each. Every participant has:  
 - A baseline sample (before the intervention) 
 - An after_antibiotic sample

 So 51 participants × 2 = 102 total samples in the dataset.                                                                                                          
                                                         
- How are the numeric variables distributed? Are any heavily skewed? Do any
  have suspicious values?

 Five cytokines (IL-10, IFN-y, TNFa, IL-1b, IL-6) are mostly below detection limits — IL-10 is entirely unusable. All three qPCR variables are heavily right-skewed, 
 with L. crispatus being ~65% zeros, so a simple mean won't tell you much. There's also a data-integrity issue: in 30 out of 102 qPCR samples, the sum of the two    
 Lactobacillus species exceeds the total bacteria measurement, which shouldn't happen biologically.    

- Where are the missing values, and what do you think they mean?
### Short summary                                                                                                                                                   
                                                                                                                                                                     
 There are no explicit missing values (no NAs, no blanks) anywhere in the dataset. But the real missingness is hidden — in the Luminex data, 498 out of 1020         
 cytokine measurements are flagged "out of range," which means the true value is unknown because it fell below the assay's detection limit. And in the qPCR data,    
 zeros for L. crispatus (~65% of samples) likely mean it was not detected rather than truly absent.                                                                  
                                                                                                                                                                     
 ### Detailed description                                                                                                                                            
                                                                                                                                                                     
 Every cell in every file is filled in — no NAs, no empty strings, no "NA" text values. The data collection forms were clearly designed to avoid blanks. However,    
 the missing information is encoded in two other ways:                                                                                                               
                                                                                                                                                                     
 1. Luminex limits column — When limits = "out of range", the conc value is not a real measurement. It's an imputed floor value (all the same number for a given     
    cytokine, e.g. all 102 IL-10 samples show exactly 0.767). This means the true concentration is somewhere below what the assay can detect — a classic "below      
    limit of detection" (BLOD) situation. Five cytokines (IL-10, IFN-y, TNFa, IL-1b, IL-6) are affected more than 70% of the time.                                   
 2. qPCR zeros — When qpcr_crispatus or qpcr_iners is 0, it could mean the bacterium was genuinely absent, or it was present at levels too low for the qPCR assay to 
    detect. Since L. crispatus is zero in 65% of samples, this isn't an error — it's a biological signal (many vaginal microbiomes are crispatus-negative) — but it  
    acts like missingness for any analysis that needs a log-transform.   


- What do you **not** understand about the dataset yet?
