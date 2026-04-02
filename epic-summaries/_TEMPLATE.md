# Epic Summary Template

> **Usage:** Copy this file into the appropriate `CTDC-XXXX-short-name/` subfolder. Fill in all `{{PLACEHOLDER}}` values. Claude uses this template to generate the `.docx` output.

---

## METADATA (not printed — for version tracking)

```
Epic Key:       {{JIRA_EPIC_KEY}}          e.g. CTDC-1764
Epic Title:     {{EPIC_TITLE}}
Document Ver:   {{VERSION}}                e.g. v1.0
Date Prepared:  {{DATE}}                   e.g. April 2, 2026
Prepared By:    Gina Kuffel, Senior Technical Project Manager
Organization:   Frederick National Laboratory for Cancer Research (FNL)
                Contractor to NCI / Center for Biomedical Informatics and Technology (CBIIT)
Jira Link:      https://tracker.nci.nih.gov/browse/{{JIRA_EPIC_KEY}}
```

---

## 1. COVER PAGE

- **Document Title:** {{EPIC_TITLE}} — Epic Summary
- **Jira Key:** {{JIRA_EPIC_KEY}}
- **Version:** {{VERSION}}
- **Date:** {{DATE}}
- **Status:** {{STATUS_BADGE}}   <!-- In Progress | Complete | At Risk | On Hold -->
- **Prepared By:** Gina Kuffel, Senior Technical Project Manager
- **Organization:** Frederick National Laboratory for Cancer Research (FNL)
  - *Contractor supporting NCI / CBIIT — Cancer Research Data Commons (CRDC)*

---

## 2. EXECUTIVE SUMMARY

> 3–5 sentences. Plain English. Answer: What is this? Why does it matter? What is the expected outcome? No Jira jargon.

{{EXECUTIVE_SUMMARY}}

---

## 3. SCOPE & OBJECTIVES

### In Scope
- {{SCOPE_ITEM_1}}
- {{SCOPE_ITEM_2}}

### Out of Scope
- {{OUT_OF_SCOPE_1}}

---

## 4. WORK BREAKDOWN

> Table of all child tickets. Group: Stories → Tasks → Bugs → Sub-tasks.

| Ticket Key | Summary | Type | Status | Assignee | Story Points |
|---|---|---|---|---|---|
| {{KEY}} | {{SUMMARY}} | {{TYPE}} | {{STATUS}} | {{ASSIGNEE}} | {{POINTS}} |

---

## 5. PROGRESS SUMMARY

- **Total Tickets:** {{TOTAL}}
- **Complete:** {{DONE}} ({{PCT_DONE}}%)
- **In Progress:** {{IN_PROGRESS}}
- **Not Started:** {{TODO}}
- **Blocked:** {{BLOCKED}}
- **Story Points — Burned / Total:** {{BURNED}} / {{TOTAL_POINTS}}
- **Sprint Association:** {{SPRINT_NAME}}

---

## 6. RISKS & BLOCKERS

> Flag anything currently blocked or at risk. If none, say so explicitly.

| Ticket | Issue | Impact | Mitigation |
|---|---|---|---|
| {{KEY}} | {{RISK_DESC}} | {{IMPACT}} | {{MITIGATION}} |

---

## 7. DIAGRAMS & VISUALS

{{DIAGRAMS_NOTE}}

*If no diagrams are attached: "No architecture diagrams or mockups are attached to this epic at the time of publication."*

---

## 8. NEXT STEPS & OPEN QUESTIONS

- {{NEXT_STEP_1}}
- {{NEXT_STEP_2}}
- **Open Question:** {{OPEN_QUESTION_1}}

---

## DOCUMENT HISTORY

| Version | Date | Author | Summary of Changes |
|---|---|---|---|
| v1.0 | {{DATE}} | Gina Kuffel (FNL) | Initial publication |
