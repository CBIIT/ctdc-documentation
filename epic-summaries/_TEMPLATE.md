# Epic Summary Template

> **Usage:** Copy this file into the appropriate `CTDC-XXXX-short-name/` subfolder. Fill in all `{{PLACEHOLDER}}` values. Claude uses this template to generate the `.docx` output.

> **Two variants.** A **feature epic** summary uses every section below. An **evergreen epic** summary (a standing container such as Internal Data Modeling, Submission Data Modeling, or Data Integration) uses only the stable sections: keep 1 (Cover), 2 (Executive Summary), 3 (Scope & Objectives), the Glossary, 7 (Diagrams & Visuals), and Document History; **omit 4 (Work Breakdown), 5 (Progress Summary), 6 (Risks & Blockers), and 8 (Next Steps)**, and add the short **Current Status** section (4a below) in their place, pointing at Jira and the CTDC engineering dashboard as the live source. See `README.md`, "When a Summary Is Written," for the rule and the reasoning.

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

## 4a. CURRENT STATUS (evergreen epics only; replaces sections 4, 5, 6, and 8)

This is an evergreen epic, so this document does not carry a point-in-time work breakdown or progress figures; they would be out of date within a sprint. Current child tasks, their status, and story points are always available in Jira under epic {{JIRA_EPIC_KEY}}, and at the summary level on the CTDC engineering dashboard.

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
