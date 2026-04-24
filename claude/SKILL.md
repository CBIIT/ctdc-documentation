---
name: ctdc-sprint-command-center
description: "Operational knowledge base for the CTDC Sprint Command Center Claude project. Contains SOPs, workflow templates, JQL recipes, stakeholder doc standards, demo day artifacts, retro format, deck standards, and session recovery procedures for the Clinical and Translational Data Commons (CTDC) engineering team."
---

# 🚀 CTDC Sprint Command Center — Skill Knowledge Base

> **Project:** Clinical and Translational Data Commons (CTDC)
> **Ecosystem:** Cancer Research Data Commons (CRDC)
> **Team:** React web application engineers
> **Claude Project:** Sprint Command Center
> **Last Updated:** 2026-04-24

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
- **Jira board ID:** `641`

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

### Specific Named Sprint
```
project = CTDC AND sprint = "CTDC Sprint 25"
```
> **Always verify the sprint name matches the sprint you intend to review.** Use `jira_get_sprints_from_board` with board ID `641` to confirm start/end dates align with your meeting date. Active sprint is different from the sprint being reviewed.

### Blocked Items
```
project = CTDC AND sprint in openSprints() AND status = "Blocked" ORDER BY updated ASC
```

### Epic and All Child Issues
```
project = CTDC AND ("Epic Link" = CTDC-XXXX OR parent = CTDC-XXXX) ORDER BY issuetype DESC, status ASC
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

### Release Tickets by fixVersion (multi-version releases)
```
project = CTDC AND fixVersion in ("CTDC FE 1.3.0.3", "CTDC BE 1.3.0.1", "DMN Core 1.11.0", "DMN Standalone 1.2.0")
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
| **Font (docs)** | Arial throughout |
| **Font (decks)** | Georgia (headers) + Calibri (body) — see Section 11 |
| **NIH Blue** | `#20558A` — section headers, cover accent bar, table header backgrounds |
| **NIH Red** | `#BE0000` — risk/blocker callouts, "At Risk" status badges, critical notes |
| **Light Blue** | `#EAF1F8` — alternating table rows, subtle backgrounds |
| **White** | `#FFFFFF` — header text on dark backgrounds |
| **Jira links** | Footnotes only in docs — never inline URLs (keeps docs readable in print) |
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

### 6a. Daily Standup Prep Checklist
Run these checks before each standup:
1. Pull blocked items: `status = "Blocked"` in current sprint
2. Pull items with no update in 48+ hours: `updated <= -48h` in current sprint
3. Surface anything that moved to Done since yesterday
4. Flag any tickets past their due date
5. Note any tickets with no assignee

---

### 6b. 🔢 Verify Before You Count — A Rule, Not a Suggestion

**Always verify Jira totals in code before building charts, narrative, or deck content.** Never hand-count from a JSON dump.

**Required workflow:**
1. Pull the sprint/release/epic tickets with a single `jira_search`
2. Write a short Node or Python script that reads the raw JSON, tallies by status / type / assignee, and prints counts
3. Use those verified counts to populate the deck, doc, or Slack message
4. Never guess or eyeball the totals from chat or JSON

**Why this matters:** Confusing "In Progress" with "In Progress + Ready for Review + Ready for QA + Testing" is easy when they're all `In Progress` category. Confusing "Reopened" with "Open" is easy. Hand-counting a 40-ticket sprint across 8 statuses is where mistakes land in stakeholder materials.

**Also verify before building:**
- **Sprint name** — the sprint being reviewed is NOT the active sprint (active = next one). Use `jira_get_sprints_from_board` with board `641` to confirm dates match the meeting date.
- **Ticket counts per metric** — total, done, carry-over, by status, by type, by assignee. All should sum correctly.
- **Release scope** — cross-reference GitHub release notes against Jira `fixVersion` query. Gaps reveal tagging hygiene issues.

---

### 6c. Sprint Review Summary Format (Enhanced)

```
## Sprint [N] Review — CTDC
**Sprint Dates:** [start] → [end] (pulled from Jira, not memory)
**Sprint Goal:** [exact goal text from Jira sprint record]

### 🎯 Goal Scorecard
[X] of [Y] goals delivered. Each goal scored: ✓ DELIVERED / ▲ PARTIAL / ✗ NOT STARTED.
Score against the Jira-recorded sprint goal, not raw ticket completion.

### 📊 Velocity (by ticket count — team does not track story points)
- Total tickets: X
- Done / Closed: Y (Z%)
- In Motion (In Progress + Ready for Review + Ready for QA + Testing): A
- On Hold: B
- Open / Reopened: C
- Carry-overs: (Total − Done)

### 🧩 Breakdown by Work Type
- User Stories: done / total
- Tasks: done / total
- Bugs: done / total

### 👥 Who Closed What
Table of Done tickets by assignee, ordered by count descending.

### 🚀 Release Scope (if a release shipped in this sprint)
- Versions shipped (FE, BE, File Service, Auth, Interoperation, DMN Core, DMN Standalone)
- Tickets by fixVersion
- Data gaps worth naming (versions without Jira tickets; tickets shipped without fixVersion tags)

### ✅ Completed Work
[table: key | summary | type | assignee]

### 🔄 Carried Over
[table: key | summary | status | assignee | reason]

### 🚩 Risks & Flags
[list with color coding: 🟥 high / 🟨 medium / 🟩 info]

### 🎤 Demo Schedule (if combined with release demo)
[table: slot | time | feature | demo lead | supporting | tickets]

### 🔄 Retro Format
Liked · Lacked · Learned (not Start/Stop/Continue). 10-min silent brainstorm.
Retro board URL: [varies per sprint — confirm with TPM]
```

---

### 6d. 🔄 Retrospective Format

**The CTDC team uses Liked · Lacked · Learned.** Do not default to Start/Stop/Continue — it is wrong for this team.

| Rule | Value |
|---|---|
| **Format** | Liked · Lacked · Learned |
| **Silent brainstorm duration** | 10 minutes |
| **Full retro block** | 20 minutes (10 silent + 10 group/discuss/vote) |
| **Retro tool** | [retrotool.io](https://retrotool.io) |
| **Board URL** | Varies per sprint — **always confirm with the TPM before referencing in materials** |
| **Action items** | Owner + deadline required or they won't happen |

**Facilitation flow:**
1. Open board link (10 min silent brainstorm — everyone drops cards, no talking)
2. Group similar cards
3. Discuss as a team
4. Dot-vote the top 3 action items
5. Assign owners and deadlines

**Sprint-in-motion caveat:** When a retro happens mid-sprint (next sprint has already started), action items coming out of the retro can't go into next sprint planning because that's already happened. Triage options: insert into the in-flight sprint as additions, or defer to the following sprint. State this explicitly when opening the retro so the team calibrates expectations.

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

### Team Roster

| Name | Role | Username | Account Key | Slack ID |
|---|---|---|---|---|
| Gina Kuffel | Senior TPM | `kuffelgr` | `JIRAUSER57501` | `U025MCK7MD3` |
| Yizhen Chen | Tech Lead / Full Stack | `cheny39` | `cheny39` | `UJ59LJYU9` |
| Nahom Tesfatsion | Frontend Engineer | `tesfatsionnh` | `JIRAUSER56020` | `U01TUNBB02K` |
| Adam Davenport | Backend Engineer | `davenportaw` | *(not confirmed)* | `U06GM3BFWLU` |
| Eric Miller | Backend Engineer | `millerer` | `JIRAUSER60101` | `U03SEJFGU7L` |
| Nathan Huang | Frontend Engineer (DMN focus) | `huangn4` | `JIRAUSER65560` | `U07LB6W2RMH` |
| Valentina Epishina | QA Engineer | `epishinavv` | `JIRAUSER58300` | `U02PMPFHBRN` |
| Patrick Breads | Data Engineer | `breadsp2` | `JIRAUSER55423` | *(not confirmed)* |
| Stephanie Singleton | Data Concierge | `singletonss` | `JIRAUSER62602` | `U073X1J8FM2` |
| Charles Ngu | DevOps / Deployments | `nguca` | `JIRAUSER61006` | `U04L21QJGNM` |
| Michael Fleming | Pipeline Engineer | `flemingme` | `flemingme` | *(not confirmed)* |

### Cross-Team Contacts

| Name | Role | Team | Slack ID |
|---|---|---|---|
| Alec Mattu | Core DMN / Bento Framework | CRDC-DH | `U045N4QQRT9` |
| Ambar Rana | Dev Lead | ICDC | *(email: ambar.rana@nih.gov)* |

### Stakeholder Roles
- 🏛️ **Stakeholders:** NCI leadership, data submitters, research community
- 🧑‍💼 **Senior Technical PM** owns the Sprint Command Center Claude project; manages both CTDC and ICDC

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
- Update the team roster (Section 8) when team membership changes; confirm Slack IDs and Jira account keys when a new member is added

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

### ⚠️ Known Gotcha — Batch Read Inconsistency

**`jira_search` batch queries sometimes return `null` for populated custom fields.** This was observed with `customfield_23650` (Developer) on CTDC-1718 — the batch query returned `null`, but a single-ticket `jira_get_issue` confirmed the field was populated with `huangn4`.

**Rule:** When a custom field appears empty in a batch `jira_search` result, **always verify with a single-ticket `jira_get_issue` call before acting on it as "empty."** Flagging a field as empty in a stakeholder document (or asking the user to investigate) is a real cost — don't pay it on a false negative.

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

---

## 11. 🎨 Sprint Review & Release Demo Deck Standards

Combined Sprint Review + Release Demo + Retro decks follow this standard. Built with the `pptxgenjs` npm package and delivered as `.pptx`.

### 11a. Build Tool & Setup

- **Package:** `pptxgenjs` (global npm install: `npm install -g pptxgenjs`)
- **Layout:** `LAYOUT_WIDE` (13.333" × 7.5")
- **QA workflow:** Build → convert to PDF via `soffice` → rasterize with `pdftoppm` → inspect each slide image → fix defects → re-render once. Stop after one fix cycle unless a new user-visible defect appears.

### 11b. Palette & Typography

| Element | Value |
|---|---|
| **Primary (NIH Blue)** | `#20558A` |
| **Primary dark (NIH Blue Dark)** | `#143458` — dark slide backgrounds |
| **Accent light** | `#EAF1F8` — alternating rows, soft card backgrounds |
| **Accent red (NIH Red)** | `#BE0000` — sparingly, for risk callouts and the single repeat motif |
| **Supporting green** | `#16A34A` — "Done" / delivered / positive |
| **Supporting amber** | `#D97706` — carry-over / warning |
| **Text ink** | `#1F2937` |
| **Muted text** | `#64748B` |
| **Subtle dividers** | `#E2E8F0` |
| **Header font** | Georgia (bold) |
| **Body font** | Calibri |

### 11c. Standard Slide Order (Combined Review + Release + Retro)

1. **Cover** (dark bg) — Sprint number, release tag, meeting date/time, sprint dates, preparer block
2. **Agenda** (light bg) — Numbered card rows, duration per block, total
3. **Sprint [N] by the Numbers** — Big stat callout + status doughnut chart
4. **Sprint [N] Goal Scorecard** — "X of Y goals delivered" + one card per goal (✓/▲/✗)
5. **Breakdown by Work Type** — Stacked bar (Done vs Carry-over) by User Story / Task / Bug + takeaways panel
6. **Who Closed What** — Horizontal bar by assignee + shout-out callout
7. **Release [version] Overview** (dark bg, section break) — 4 stat cards
8. **Demo Schedule** — Table: slot | time | feature | presenter(s) | tickets
9–12. **Presenter Intro Slides** — One per demo slot: role eyebrow, presenter card(s), "What you'll see" bullets, ticket refs
13. **Looking Ahead: Sprint [N+1]** — Carry-over chart + key flags panel
14. **Up Next: Retrospective** (dark bg, section break) — Title + format + timebox
15. **Retro Board Link** — Call to action with clickable retrotool URL + ground rules strip

### 11d. Design Rules (what NOT to do)

- **Never put red accent bars under slide titles.** Accent lines under titles read as AI-generated slop. Use whitespace or a dark background instead. A red motif line elsewhere on the slide (between content blocks, not attached to a heading) is OK.
- **Never let big stat numbers overflow their text box.** `fontSize: 72` in an `h: 1.2` box with `valign: "middle"` will clip the top of the glyph. Use `fontSize: 64` in `h: 1.3` with valign middle, or size the box generously.
- **Multi-presenter slots** get a single slide with two presenter cards stacked vertically on the left, "What you'll see" bullets on the right. Allocate 8 min instead of 5–6 for two-presenter demos.

### 11e. Presenter Intro Slide Structure

Each demo slot gets one intro slide with this layout:

- **Top-left:** `SLOT N` in red eyebrow caps
- **Top-right:** duration (e.g., `8 MINUTES`) in muted caps
- **Title:** feature name in large Georgia bold
- **Subtitle:** plain-English "why this matters" in italic NIH Blue
- **Left column (1 or 2 cards):** Role eyebrow + Name in Georgia + focus sentence in muted Calibri
- **Right column:** "What you'll see" bullets (use square bullets, `bullet: { code: "25A0" }`)
- **Bottom-right:** Ticket references in muted + bold NIH Blue

---

## 12. 🎤 Demo Day Artifacts Workflow

A combined Sprint Review + Release Demo + Retro meeting produces a specific set of artifacts. Build them in this order and deliver on this schedule.

### 12a. The Five Artifacts

| # | Artifact | Format | Destination | Timing |
|---|---|---|---|---|
| 1 | **Slack announcement** | Slack message in `#ctdc-discussions` | Channel `CQ4ABNA4T` | 24–48 hours before meeting |
| 2 | **Sprint Review deck** | `.pptx` | Used live in meeting | Ready 24h before meeting |
| 3 | **Per-presenter prep sheets** | `.md` per presenter | DM to each presenter | 24h before meeting |
| 4 | **Retro board** | retrotool.io board | URL in deck + Slack announcement | Created by TPM before meeting |
| 5 | **Goal scorecard framing** | Built into deck slide 4 | — | Built with deck |

### 12b. Per-Presenter Prep Sheets

**One sheet per person**, not per slot. If a presenter owns two slots, both go in their one sheet.

Each prep sheet contains:
- Meeting date/time + slot(s) + total airtime
- Pre-meeting checklist (what to open, what to test, what to pre-load)
- Context on what they're showing and why
- Talking points with suggested wording (not a script — they can borrow or discard)
- Step-by-step live demo walkthrough
- Likely questions with prepared answers
- "If something goes wrong" fallbacks
- Ticket reference list

**Distribution:** DM each prep sheet privately 24 hours before the meeting (not the channel — they contain presenter-specific prompts). Thursday afternoon is the sweet spot for a Friday meeting; earlier and presenters forget.

### 12c. Goal Scorecard Framing

Always pull the sprint goal from Jira (`jira_get_sprints_from_board`, then check the `goal` field on the sprint object). Score each goal against the real outcome in the sprint:

| Marker | Meaning |
|---|---|
| ✓ **DELIVERED** | Goal fully met |
| ▲ **PARTIAL** | Progress made but not complete |
| ✗ **NOT STARTED** | Goal not addressed |

Headline format: *"X of Y goals delivered."* This reframes a sprint more favorably for leadership than raw ticket completion % alone. When the raw completion rate is low (e.g., 28%) but major goals were delivered (e.g., release shipped, major data load completed), the scorecard tells a more accurate story.

---

## 13. 📦 Release Tagging Hygiene Checklist

Before declaring a release "ready to announce," verify these Jira/GitHub hygiene items. Gaps found during Sprint 25 / Release 1.3.0.3 review are what motivated this checklist.

### 13a. Pre-Release Checklist

- [ ] **Every shipped ticket has a `fixVersion` tag.** Cross-reference: tickets in the release branch but missing `fixVersion` tags get surfaced in JQL gap queries (see 13b below).
- [ ] **Every shipped ticket has the Developer field populated** (`customfield_23650`). Empty Developer fields on shipped work are a signal something slipped past the workflow gate.
- [ ] **Every versioned component has its own Jira fixVersion.** If FE / BE / File Service / Auth / Interoperation / DMN Core / DMN Standalone all shipped new versions, each one should exist as a fixVersion in Jira with tickets tagged.
- [ ] **Cross-reference GitHub release notes against fixVersion queries.** If GitHub says "File Service 1.1.0.109 shipped" but the Jira query for `fixVersion = "File Service 1.1.0.109"` returns zero tickets, surface the gap — either the version bump was pure infra (document as such) or tagging is slipping.
- [ ] **Verify all release tickets resolve to confirmed developers via the Developer field (not just the Assignee).** The Assignee on a shipped ticket is often the QA who closed it, not the dev who built it. Use the Developer field for demo attribution.

### 13b. Gap-Finder JQL Queries

**Tickets in the release-associated epic that are closed but have no fixVersion:**
```
project = CTDC AND "Epic Link" = CTDC-XXXX AND status = Closed AND fixVersion is EMPTY
```

**Tickets with a release fixVersion but no Developer field set:**
```
project = CTDC AND fixVersion = "CTDC FE 1.3.0.3" AND "Developer" is EMPTY
```

**Tickets tied to the user story of a shipped feature but not tagged with the release fixVersion (catches the CTDC-1670 class of miss):**
```
project = CTDC AND "Epic Link" = CTDC-1650 AND status = Closed AND fixVersion is EMPTY
```

### 13c. When Tagging is Inconsistent

If gaps are found during pre-release review, you have two options:
1. **Back-fill tags retroactively** before announcing the release. Right for small gaps.
2. **Acknowledge the gap in the release retro** and fix the process going forward. Right when gaps reveal systemic tagging failures across multiple components.

Either way, surface the gap to the team — don't paper over it. Data gaps in release tagging erode the ability to generate release notes automatically.

---

## 14. 💬 Slack Communication Reference

### 14a. Channels

| Channel | Purpose | ID |
|---|---|---|
| `#ctdc-discussions` | Team-wide announcements, sprint/release posts | `CQ4ABNA4T` |
| `#ctdc-dev` | Engineering-focused discussion | `C03CM2T302J` |

### 14b. Slack Message Formatting — Known Failures

Real failures encountered when building release announcements. Avoid these patterns.

- **`---` horizontal rules trigger `invalid_blocks` errors.** Slack's block validator rejects them. Use blank lines or a `:large_blue_diamond:` / `:white_medium_square:` as a visual break between sections instead.
- **Arrow characters (`→`) combined with user mentions can fail validation.** Use plain text labels ("Presenters:", "Tickets:") instead of decorative arrows in the same line as `<@U123>` mentions.
- **User mentions render as `<@U123456>`.** If you see `<mailto:@U123...>` in a message (often in pasted content from Slack exports), that's a malformed mention — strip and re-format.
- **For `@channel` notifications, use `<!channel>` in the API payload.** Slack UI renders it as `@channel` and pings the channel on send.
- **Bot-sent messages show bot attribution** (e.g., "sent by Claude"). To post as yourself, copy the message text and paste it in the Slack UI directly. The draft tool solves this for new messages — drafts created via MCP send as the user when they hit "Send."

### 14c. Draft Tool Behavior

- Drafts created via `slack_send_message_draft` attach to a specific channel. They appear **only when you navigate to that channel**, not in the global "Drafts & Sent" sidebar view. If a user says they don't see the draft, the most likely cause is they're looking at the global Drafts view instead of the target channel.
- **Only one attached draft per channel at a time.** Creating a new draft may replace an existing draft.
- When the user has already reviewed the message in chat, `slack_send_message` is appropriate. If they haven't, use `slack_send_message_draft` so they can review in the Slack UI.

### 14d. Standard Slack Announcement Template

Use this structure for Sprint Review + Release Demo announcements in `#ctdc-discussions`:

```
<!channel>  (optional — use only if you need to notify the full channel)
:rocket: *Sprint [N] Review + Release [version] Demo Day!* :rocket:

[Hook sentence — what's happening and why it matters]

:calendar: *When:* [Day] at [Time] [Timezone]

*What's on the docket:*
• Sprint [N] review — what we shipped, what's carrying over ([goal scorecard tease])
• Live demos from the devs on every feature and bug fix
• Retrospective — Liked / Lacked / Learned on our retro board
• Action items

Full release notes: <[github release URL]|[release tag]>

:microphone: *Demo Schedule — who's on deck:*

*Slot N ([N] min)* — [Feature name]
Presenter(s): <@[slack_id]>
Tickets: [CTDC-XXXX, CTDC-YYYY]

[repeat for each slot]

<@[QA Slack ID]> will be in the room to back up QA acceptance — thank you for shepherding *[N] tickets* through QA this sprint! :test_tube: :trophy:

:clipboard: *Retro board — bookmark before Friday:*
<[retrotool URL]|[retrotool URL]>
Format: *10 min silent brainstorm* → group similar cards → discuss → dot-vote top 3 actions

*Demo presenters — quick prep ask (24h before):*
• [Prep item 1]
• [Prep item 2]

*Shout-outs from the release:* :gift:
• [Cross-team wins, on-time delivery, notable collaboration]

See you [day] at [time]! :raised_hands: Drop questions, heckles, or demo tips in the thread.
```
