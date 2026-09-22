---
type: project-founding
project: survey-data-cleanup
level: standard
created: 2026-09-01
---

# survey-data-cleanup — Founding document

## Decisions summary
- project_title: Survey data cleanup — Q3 respondent dataset
- project_type: data analysis
- goal: produce a clean, deduplicated, outlier-flagged dataset ready for analysis
- scope: included — deduplication, completion-time outlier flagging, missing-data audit; excluded — statistical analysis itself
- success_criteria: zero duplicate respondent IDs; all sub-2-minute completions flagged with a reason; missing-data rate reported per question
- deliverables: cleaned dataset (CSV), cleaning report (this project's deliverable file)
- timeline: 1 week
- confirmed_by_user: yes, 2026-09-01

## Discussion summary
Considered cleaning in the analysis notebook directly vs. as a separate
tracked project; chose the latter so the cleaning decisions are
auditable independent of whichever analysis script consumes the output.
Agreed the completion-time threshold (2 minutes) based on median survey
length of ~9 minutes.

Related: survey-data-cleanup-plan.md (a shared terminology.md was not used in this example)
