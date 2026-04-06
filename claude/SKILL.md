---
name: ctdc-sprint-command-center
description: "Operational knowledge base for the CTDC Sprint Command Center Claude project. Contains SOPs, workflow templates, JQL recipes, stakeholder doc standards, domain context, and session recovery procedures for the Clinical and Translational Data Commons (CTDC) engineering team."
---

# 🚀 CTDC Sprint Command Center — Skill Knowledge Base

> **Project:** Clinical and Translational Data Commons (CTDC)
> **Ecosystem:** Cancer Research Data Commons (CRDC)
> **Team:** React web application engineers
> **Claude Project:** Sprint Command Center
> **Last Updated:** 2026-04-06

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

### Overview

Leadership and stakeholder summary documents are **never** Jira links — they are polished `.docx` files stored in the `epic-summaries/` folder at the root of this repository. Each epic gets its own subfolder with versioned files.

**Repository path:** `CBIIT/ctdc-documentation/epic-summaries/`
**Full SOP:** See `epic-summaries/README.md`

---

### 5a. Authorship & Affiliation

**Critical:** The preparer is affiliated with **Frederick National Laboratory for Cancer Research (FNL)**, not NCI or CBIIT. NCI/CBIIT is the federal sponsor. Always reflect this distinction.

| Field | Value |
|---|---|
| **Prepared By** | Gina Kuffel, Senior Technical Project Manager |
| **Organization** | Frederick National Laboratory for Cancer Research (FNL) |
| **Supporting** | NCI / Center for Biomedical Informatics and Technology (CBIIT) |
| **Program** | Cancer Research Data Commons (CRDC) |

---

### 5b. Brand & Formatting Standards

| Property | Value |
|---|---|
| **Page size** | US Letter (8.5" × 11") — NEVER A4 |
| **Font** | Arial throughout |
| **NIH Blue** | `#20558A` — section headers, cover accent bar, table header backgrounds |
| **NIH Red** | `#BE0000` — risk/blocker callouts, "At Risk" status badges, critical notes |
| **Light Blue** | `#EAF1F8` — alternating table rows, subtle backgrounds |
| **White** | `#FFFFFF` — header text on dark backgrounds |
| **Jira links** | Footnotes only — never inline URLs (keeps docs readable in print) |
| **Jargon** | Minimize; always add plain-English parentheticals when technical terms are unavoidable |

---

### 5c. Document Structure

Every epic summary `.docx` follows this section order:

1. **Cover Page**
   - Document title (epic name + "— Epic Summary")
   - Jira key, version, date, status badge
   - Prepared by / organization block (FNL as preparer, NCI/CBIIT as sponsor)

2. **Executive Summary** (3–5 sentences, plain English)
   - What problem does this epic solve?
   - Why does it matter to researchers/users?
   - What is the expected outcome?

3. **Scope & Objectives**
   - In-scope deliverables (bullets)
   - Out-of-scope callouts

4. **Work Breakdown** (table)
   - Columns: Ticket Key | Summary | Type | Status | Assignee | Story Points
   - Grouped: Stories → Tasks → Bugs → Sub-tasks

5. **Progress Summary**
   - % complete, story points burned/total, sprint association

6. **Risks & Blockers** (table)
   - Columns: Ticket | Issue | Impact | Mitigation
   - If none: state explicitly

7. **Diagrams & Visuals**
   - Architecture diagrams, data flow diagrams, mockups from Jira attachments
   - If none: *"No diagrams attached to this epic at time of publication."*

8. **Next Steps & Open Questions**

9. **Document History** (version table at end)
   - Columns: Version | Date | Author | Summary of Changes

---

### 5d. Versioning Rules

Files are stored as: `CTDC-XXXX-summary-v{MAJOR}.{MINOR}.docx`

| Version Bump | When to Use |
|---|---|
| `v1.0` | First published version |
| `vX.(Y+1)` | Minor update — ticket status changes, assignee corrections, wording fixes |
| `v(X+1).0` | Major revision — scope change, re-planning, new phases added |

> **Never overwrite an existing file.** Always create a new versioned file and commit with a descriptive message.

---

### 5e. How Claude Generates Epic Summary Docs

1. Pull epic + all child tickets from Jira via `jira_search` (use JQL: `"Epic Link" = CTDC-XXXX OR parent = CTDC-XXXX`)
2. Populate all template sections from ticket data
3. Generate `.docx` using `docx` npm package (US Letter, Arial, NIH Blue/Red branding)
4. Validate the file
5. Commit to `epic-summaries/CTDC-XXXX-short-name/CTDC-XXXX-summary-v1.0.docx`
6. Update `epic-summaries/README.md` Registered Epics table

---

### 5f. 🏛️ Architecture Leadership Documents (`.docx`)

A second category of leadership document exists alongside epic summaries: **architecture leadership overviews**. These are distilled, non-technical `.docx` files that translate technical architecture documents into stakeholder-readable summaries.

**The pattern:**
- The **source of truth** is a `.md` architecture reference file in `claude/architecture/` in this repo (e.g., `claude/architecture/file-download-and-auth-stack.md`)
- The **leadership deliverable** is a `.docx` distilled from it, stored in the **CTDC Architecture** folder in SharePoint
- The two are maintained in parallel — the `.md` evolves with engineering details; the `.docx` is updated when the leadership-facing content materially changes
- The `.docx` version is **independent** from the `.md` version — start at `v1.0` for the first published `.docx` regardless of what version the source `.md` is at

**Document structure for architecture leadership overviews** (7 sections, in this order):

1. **Purpose** — what this document covers and who it's for
2. **Background** — what types of data/files exist and how they're accessed (use a table)
3. **Integration Points** — what NCI/CRDC services are involved and why (explain in plain English; no service internals)
4. **How It Works** — numbered plain-English step-by-step flow of the end-to-end process
5. **Access Model** — table summarizing authentication and access control rules
6. **Audit & Compliance** — what gets recorded per download/event, and any known gaps
7. **Key Notes for Leadership** — bullet summary of the most important facts for a program officer or CIO

**Naming convention:** `[PROJECT]_[Topic]_LeadershipOverview_v{MAJOR}_{MINOR}.docx`
- Example: `CTDC_FileDownload_LeadershipOverview_v1_0.docx`

**Versioning:**

| Version Bump | When to Use |
|---|---|
| `v1.0` | First published `.docx` for this topic |
| `vX.(Y+1)` | Minor update — wording, corrections, clarifications |
| `v(X+1).0` | Major revision — significant new content, structural change, or underlying architecture changed |

> **Never overwrite an existing file.** Always create a new versioned file.

**Storage:** SharePoint → **CTDC Architecture** folder

**Source `.md` location:** `CBIIT/ctdc-documentation/claude/architecture/`

**When to update the `.docx`:** When the source `.md` changes in a way that affects leadership-relevant content (e.g., a compliance gap is resolved, an access model changes, a new integration point is added). Purely technical corrections to the `.md` that don't change the leadership narrative do not require a new `.docx`.

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
- When a new epic summary is published, update the **Registered Epics** table in `epic-summaries/README.md`
- When a new architecture leadership `.docx` is published, note it in Section 5f and update the source `.md` header if applicable

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
