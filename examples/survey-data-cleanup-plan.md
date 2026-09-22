---
type: project-plan
project: survey-data-cleanup
---

# survey-data-cleanup — Plan

## Content plan
1. Deduplicate respondent IDs (data analysis: source and quality)
2. Flag outliers by completion time (data analysis: methods)
3. Audit missing data per question and summarize (data analysis: validation)

## Format plan
- Cleaned dataset → `survey-data-cleanup-dataset.csv` (not tracked by this
  skill directly; referenced from the deliverable file)
- Cleaning report → `survey-data-cleanup-cleaning-report.md` (subtype:
  execution-log), holds the full method and decision trail for all three
  stages

## Work plan

### Stage 1 — Deduplication ✅ Done
- Identify duplicate respondent IDs
- Decide keep/drop rule (keep latest submission)
- Apply and record count removed

🔲 Review point. Is the deduplicated file present and does the duplicate
count match manual sampling?
- Deliverable present: yes
- Matches plan: yes
- Acceptable: yes
- Change needed: no

### Stage 2 — Outlier review
- Flag responses with completion time < 2 minutes
- Manually spot-check 10 flagged responses for plausibility
- Record flagging rule and count in the cleaning report

🔲 Review point. Are all flags justified and logged with a reason?

### Stage 3 — Missing-data audit
- Compute missing-data rate per question
- Note any question with >10% missing as a risk for analysis
- Summarize in the cleaning report

🔲 Review point. Does the missing-data summary match
`success_criteria` (per-question reporting)?

success_criteria (final acceptance): zero duplicate IDs; all outliers
flagged with a reason; missing-data rate reported per question.
