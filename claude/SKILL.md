---
name: ctdc-sprint-command-center
description: "Operational knowledge base for the CTDC Sprint Command Center Claude project. Contains SOPs, workflow templates, JQL recipes, stakeholder doc standards, domain context, and session recovery procedures for the Clinical and Translational Data Commons (CTDC) engineering team."
---

# 🚀 CTDC Sprint Command Center — Skill Knowledge Base

> **Project:** Clinical and Translational Data Commons (CTDC)
> **Ecosystem:** Cancer Research Data Commons (CRDC)
> **Team:** React web application engineers
> **Claude Project:** Sprint Command Center
> **Last Updated:** 2026-03-27

---

## 📋 How This File Works

This SKILL.md contains **operational knowledge** — workflows, SOPs, templates, and domain context. It is loaded by Claude when relevant to a task.

**Behavioral guardrails** (like Docker recovery and scope boundaries) are mirrored in the **Claude.ai Project Instructions** for the Sprint Command Center project. When updating one, update the other.

---

## 1. 🐳 Session Recovery & Infrastructure

### Docker / MCP Recovery

If any MCP tool (Jira/Atlassian, Asana, etc.) is unavailable or returns a connection error, **Docker is not running** on the user's machine. Do not attempt workarounds, do not ask for manual data. Surface the issue immediately:

> "🐳 **Docker doesn't appear to be running.** The Jira/Atlassian MCP container needs to be active. Please start Docker and restore the container, then let me know when it's back up and I'll pick up right where we left off."

Wait for the user to confirm Docker is running before retrying any Jira operations.

---

## 2. 🧬 Domain Context

### About CTDC

The **Clinical and Translational Data Commons (CTDC)** is part of the NCI's Cancer Research Data Commons (CRDC). It provides researchers with access to clinical trial data and translational research datasets, enabling discovery across multiomics and clinical data types.

- **Frontend:** React web application (Bento Framework)
- **GitHub org:** CBIIT
- **Key repos:** `crdc-ctdc-ui`, `ctdc-model`, `ctdc-deployments`, `bento-ctdc-static-content`
- **Sister project:** Integrated Canine Data Commons (ICDC) — same ecosystem, separate Jira project
- **Jira project key:** `CTDC`

### Multiomics Context

The team works with multiomics data — this means datasets that combine multiple biological measurement types (genomics, proteomics, transcriptomics, etc.). When writing tickets or summaries for stakeholders, explain multiomics concepts simply: *"data that measures many different biological signals from the same samples, like reading both the DNA instructions and the proteins those instructions produce."*

---

## 3. 🗺️ Scope Boundaries — Which Claude Project to Use

| Task | Go To |
|------|-------|
| Sprint planning, ticket management, daily standups | 🚀 **Sprint Command Center** (this project) |
| Portfolio-level planning, roadmaps, cross-project priorities | 📁 **Portfolio & Roadmap** project |
| Browser testing, screenshots, UI automation | 🌐 **QA & Testing** project |
| Microsoft/Azure documentation lookup | 🔍 Web search or dedicated project |

---

## 4. 🔍 JQL Recipes

Frequently used JQL queries for the CTDC project.

### Current Sprint
```
project = CTDC AND sprint in openSprints() ORDER BY priority DESC
```

### Blocked Items
```
project = CTDC AND sprint in openSprints() AND status = "Blocked" ORDER BY updated ASC
```

### Epic and All Child Issues
```
project = CTDC AND (\"Epic Link\" = CTDC-XXXX OR parent = CTDC-XXXX) ORDER BY issuetype DESC, status ASC
```

### Recently Updated (last 24 hours — daily standup prep)
```
project = CTDC AND updated >= -24h ORDER BY updated DESC
```

### Overdue (past due date, not done)
```
project = CTDC AND due < now() AND status != Done ORDER BY due ASC
```

### Unassigned Open Issues
```
project = CTDC AND sprint in openSprints() AND assignee is EMPTY AND status != Done
```

### Items in Review/QA (good for standup)
```
project = CTDC AND sprint in openSprints() AND status in ("In Review", "QA", "Testing")
```

---

## 5. 📄 Stakeholder Document Standards

### Epic Summary Document (for leadership/stakeholders)

When asked to produce an epic summary `.docx` for leadership, follow this structure:

1. **Cover / Title Block**
   - Epic title and Jira key
   - Date prepared
   - Prepared by (TPM name)
   - Status badge (In Progress / Complete / At Risk)

2. **Executive Summary** (3–5 sentences)
   - What problem does this epic solve?
   - Why does it matter to researchers/users?
   - What is the expected outcome?

3. **Scope & Objectives**
   - Bulleted list of in-scope deliverables
   - Out-of-scope callouts if relevant

4. **Work Breakdown** (table)
   - Columns: Ticket Key | Summary | Type | Status | Assignee | Story Points
   - Group by: Stories → Tasks → Bugs → Sub-tasks

5. **Progress Summary**
   - % complete (done tickets / total tickets)
   - Story points burned vs. total
   - Sprint association

6. **Risks & Blockers**
   - Any tickets currently blocked
   - Dependencies on other teams or systems

7. **Diagrams & Visuals**
   - Include any architecture diagrams, data flow diagrams, or mockups attached to tickets
   - If no attachments exist, note: *"No diagrams attached to this epic at time of publication."*

8. **Next Steps / Open Questions**

### Formatting Rules for Stakeholder Docs
- Use **US Letter** page size (never A4)
- Font: Arial throughout
- Brand color for headers: NCI Blue `#20558A`
- Keep technical jargon minimal — if it must be used, add a plain-English parenthetical
- Tables preferred over bullet walls for multi-ticket data
- Include Jira links as footnotes, not inline URLs (keeps docs readable in print)

---

## 6. ☀️ Sprint Reporting Templates

### Daily Standup Prep Checklist
Run these checks before each standup:
1. Pull blocked items: `status = "Blocked"` in current sprint
2. Pull items with no update in 48+ hours: `updated <= -48h` in current sprint
3. Surface anything that moved to Done since yesterday
4. Flag any tickets past their due date
5. Note any tickets with no assignee

### Sprint Review Summary Format
```
## Sprint [N] Review — CTDC
**Sprint Dates:** [start] → [end]
**Goal:** [sprint goal text from Jira]

### Velocity
- Committed: X points
- Completed: Y points
- Completion Rate: Z%

### Completed Work
[table: key | summary | points]

### Carried Over
[table: key | summary | reason for carry-over]

### Blockers Encountered
[list]

### Notes for Next Sprint
[list]
```

---

## 7. 🎫 Ticket Writing Standards

### Story Format
```
As a [type of user],
I want to [action/feature],
So that [benefit/outcome].

**Acceptance Criteria:**
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

**Technical Notes:**
[Any relevant implementation context]

**Related:**
- Epic: CTDC-XXXX
- Design: [link if applicable]
```

### Bug Format
```
**Environment:** [dev / qa / staging / prod]
**Severity:** [Critical / High / Medium / Low]

**Steps to Reproduce:**
1.
2.
3.

**Expected Behavior:**
[What should happen]

**Actual Behavior:**
[What actually happens]

**Screenshots/Logs:**
[Attach or paste]
```

---

## 8. 👥 Key Contacts & Roles

> Update this section as team membership changes.

| Role | Notes |
|------|-------|
| 🧑‍💼 Senior Technical PM | Owns this Claude project; manages CTDC and ICDC |
| 👩‍💻 Engineering Team | React developers building CRDC data commons UIs |
| 🏛️ Stakeholders | NCI leadership, data submitters, research community |

---

## 9. 🔧 Maintenance Notes

- This file lives at `CBIIT/ctdc-documentation/claude/SKILL.md`
- The companion **Project Instructions** live in the Claude.ai Sprint Command Center project settings — keep both in sync when making changes
- Update JQL recipes when Jira workflow statuses change
- Update domain context when new repos or major architectural changes occur
- Review stakeholder doc standards before each major release cycle
- New SOPs or workflow patterns discovered in Claude conversations should be added here via PR

---

## 10. 🗃️ Jira Custom Field Reference

> **Critical.** The NCI tracker (`tracker.nci.nih.gov`) has non-standard field configurations. Standard Jira field IDs and the `jira_link_to_epic` tool do NOT work reliably. Always use the confirmed field IDs below.
>
> These fields are confirmed on **Task** issue type in both **CTDC** and **ICDC** projects — they are consistent across both.

### ✅ Confirmed Writable Fields (via `jira_update_issue`)

| Field Name | Custom Field ID | Value Format | Notes |
|---|---|---|---|
| **Epic Link** | `customfield_12350` | `"CTDC-XXXX"` (plain string) | Set via `fields` parameter. Confirmed working on Task type. |
| **Developer** | `customfield_23650` | `[{"name": "username"}]` (array of objects) | Multi-value user picker. Set via `fields` parameter. |

### ❌ Fields That Do NOT Work via API on This Instance

| Field | Why It Fails |
|---|---|
| `customfield_10014` | Not on edit screen for CTDC Tasks — "cannot be set" error |
| `customfield_10100` | Not on edit screen for CTDC Tasks — "cannot be set" error |
| `customfield_18250` | "Developer Legacy" — single user picker, not rendered on Task screens |
| `customfield_10657` | Visible in UI but blocked on REST API edit screen for Tasks |
| `jira_link_to_epic` tool | Calls succeed but epic link does not appear in the UI |

### 📝 Usage Examples

**Set Epic Link:**
```json
jira_update_issue(
  issue_key = "CTDC-2010",
  fields = {"customfield_12350": "CTDC-2008"}
)
```

**Set Developer:**
```json
jira_update_issue(
  issue_key = "CTDC-2010",
  fields = {"customfield_23650": [{"name": "millerer"}]}
)
```

**Set Both at Once:**
```json
jira_update_issue(
  issue_key = "CTDC-2010",
  fields = {
    "customfield_12350": "CTDC-2008",
    "customfield_23650": [{"name": "millerer"}]
  }
)
```

### 🔍 How to Discover New Fields

When a field is set manually in the Jira UI and the API equivalent is unknown:
1. Pull the ticket with `fields = "*all"` before and after the manual edit
2. Diff the `customfield_XXXXX` values — the one that changed from `null` to a value is the writable field
3. Note the value format (string vs. object vs. array) and replicate it exactly

### 👥 Known Username → Account Key Mapping (CTDC Team)

| Name | Username | Account Key |
|---|---|---|
| Yizhen Chen | `cheny39` | `cheny39` |
| Eric Miller | `millerer` | `JIRAUSER60101` |
| Nahom Tesfatsion | `tesfatsionnh` | `JIRAUSER56020` |
| Adam Davenport | `davenportaw` | *(not yet confirmed)* |
| Gina Kuffel (TPM) | `kuffelgr` | `JIRAUSER57501` |
| Charles Ngu | `charles.ngu@nih.gov` | *(email format — use email)* |
