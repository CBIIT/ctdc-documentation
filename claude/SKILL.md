---
name: ctdc-sprint-command-center
description: "Operational knowledge base for the CTDC Sprint Command Center Claude project. Contains SOPs, workflow templates, JQL recipes, stakeholder doc standards, demo day artifacts, retro format, deck standards, and session recovery procedures for the Clinical and Translational Data Commons (CTDC) engineering team."
---

# 🚀 CTDC Sprint Command Center — Skill Knowledge Base

> **Project:** Clinical and Translational Data Commons (CTDC)
> **Ecosystem:** Cancer Research Data Commons (CRDC)
> **Team:** React web application engineers
> **Claude Project:** Sprint Command Center
> **Last Updated:** 2026-04-30

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
- **Key repos:** `crdc-ctdc-ui`, `crdc-ctdc-backend`, `ctdc-model`, `ctdc-deployments`, `bento-ctdc-static-content`
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

### 7a. Story Format
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

---

### 7b. 🏛️ Epic Templates by Grouping

CTDC epics fall into seven groupings, each with its own template tuned to that grouping's actual concerns. Section **7b-shared** holds the universal rules that apply to **every** template. Sections **7b-1** through **7b-7** hold the per-grouping templates. Only the Application Pages template (7b-1) is currently drafted; the other six are stubs to be drafted in focused sessions with a real CTDC example epic in hand.

**The seven groupings:**

1. **Application Pages** (7b-1) — User-facing pages and surfaces in the CTDC web application (Home, Programs, Explore Dashboard, Study, Study Details, Participant Details, Cart, Static Pages, Program Details).
2. **Microservices** (7b-2) — Backend services with their own API contract, deployment, and lifecycle (e.g., the file service, the authn service, the backend GraphQL service).
3. **Features** (7b-3) — Cross-cutting capabilities that span multiple pages or services (e.g., file download flows, manifest export, search).
4. **Products** (7b-4) — Standalone deliverables consumed by external systems or end users (e.g., the megazip artifact, the CTDC application as a whole).
5. **Infrastructure** (7b-5) — Deployment, CI/CD, environments, cloud topology, observability (e.g., Jenkins migration, environment provisioning).
6. **Security** (7b-6) — Authn / authz / audit / compliance / vulnerability response (e.g., RAS-enabled file download, session timeout configuration, audit log gaps).
7. **Data** (7b-7) — Data model, ingestion, releases, indexes (e.g., CMB v5 release, Memgraph schema changes, OpenSearch index updates).

**When an epic could fit more than one grouping**, choose by **primary engineering ownership**:
- Backend Engineering → Microservices
- DevOps / Platform → Infrastructure
- Federal InfoSec / FedRAMP → Security
- Data Engineering / Stewards → Data
- Frontend Engineering → Application Pages or Features (Application Pages if it scopes a single page; Features if it spans many)

If the choice still isn't clear, default to the grouping whose template most closely matches the epic's actual content. The grouping label is a navigation aid for stakeholders, not a gatekeeping classification.

---

#### 7b-shared. Universal Epic Conventions

These rules apply to **every** epic template, regardless of grouping. Each per-grouping section (7b-1 through 7b-7) inherits these without restating them.

**Authorship & affiliation**
- Preparer: Gina Kuffel, Senior Technical Project Manager
- Organization: Frederick National Laboratory for Cancer Research (FNL/BACS)
- Federal sponsor: NCI / Center for Biomedical Informatics and Technology (CBIIT)
- Program: Cancer Research Data Commons (CRDC)

**Domain content rules**
- **Memgraph, never Neo4j.** Always reference Memgraph as the graph database with the parenthetical *"replaces the historical Neo4j references"* when first introduced. The public `crdc-ctdc-starter-kit` README is outdated and must not be mirrored.
- **OpenSearch named explicitly** when the epic surfaces aggregations, counts, or facet-driven queries.
- **FAIR mission stated.** Connect the epic back to making CTDC data Findable, Accessible, Interoperable, and Reusable somewhere in the description (typically in Context & Background and User Impact).
- **CTDC ↔ ICDC translations.** When mirroring an ICDC pattern, translate node names and relationship names — `participant`/`case`, `specimen`/`sample`, `associated_with`/`of_*`, `data_file_uuid`/`file_uuid`, `study_accession`/`clinical_study_designation`. See Section 16.

**Epic posture defaults**
- **Priority:** Major (set via MCP, not the description).
- **Status:** Open. These are evergreen containers, not work items.
- **Assignee:** Unassigned, unless directed otherwise. Individual child tickets carry the actual ownership.
- **Labels:** Preserve any existing label (e.g., `Task-1.3.8.X`). Do not add or remove labels in epic-normalization passes unless deliberately changing them.

**Cross-epic linkage**
- Out of Scope, Dependencies, and Notes sections must point readers to the epics that cover adjacent or excluded work.
- Application page epics cross-reference the file download stack epic (CTDC-1764) when relevant.

**Quality bar**
- WCAG 2.1 AA accessibility, design system conformance, performance baselines under realistic data volumes, and automated test coverage appear as Performance & Quality acceptance criteria in every user-facing epic.
- For non-user-facing epics (microservices, infrastructure, security, data), the equivalent quality bar is named in that grouping's template.

**Verification & ground truth**
- **For Application Pages and Features:** verify the live UI with Playwright (`browser_navigate` + `browser_snapshot`) before drafting. Ground In Scope claims in what the page actually renders.
- **For Microservices, Infrastructure, Security, Data:** verify against the source repo, deployed config, or live system before drafting. Do not infer scope from imagination.
- **For all groupings:** the rendered Jira UI is the only ground truth for description rendering. Wiki source returned by `jira_get_issue` does not reveal rendering state and looks identical for working and broken tickets — only a UI screenshot tells the truth.

**Markdown conventions**
- Section headers: `### {emoji} **{Title}**` (h3, emoji + bold).
- Bullets are flush-left; no indented `-` bullets with bold labels.
- Use `**bold**` for emphasis (matched delimiters).
- Inline route templates: plain text without backticks, but **escape every curly brace as `\{` and `\}`** (e.g., `#/study/\{study_code\}`). See "MCP write notes" below for why this is required. Backticks are fine for single-token names like `participantById` or `customfield_12350`.
- **Curly-brace rule (universal).** Anywhere curly braces appear in description text — URLs with path parameters, scope items naming a variable, acceptance criteria, glossary entries, notes, anywhere — escape them with backslashes: `\{program_short_name\}`, not `{program_short_name}`. The only exception is text inside a fenced code block, where the renderer treats content as literal. The Markdown source you author with backslash-escapes will round-trip into Jira-wiki as `\{...\}` and render in the UI as literal `{...}` with no parser-state corruption.

**MCP write notes (description field caveat)**

The Jira-wiki renderer treats `{...}` as **macro syntax** — `{code}`, `{noformat}`, `{panel}`, `{color}`, etc. When the renderer encounters an unknown macro name like `{program_short_name}`, the parser does not gracefully fail back to literal text. Instead, parser state gets corrupted for the surrounding block, and the corruption propagates *forward* through the document. Symptoms include:

- Adjacent headers losing their `h3.` recognition and rendering as literal `h3.` text
- Bullets after the offending point losing their list context
- Some headers disappearing entirely (no `h3.` prefix, just bare bold text on a line)
- Paragraph layout fracturing right at the `{...}` occurrence

This was the verified root cause of the CTDC-2040 (Program Details Page) render failure on 2026-04-30. The fix is straightforward: **escape every curly brace in description text as `\{...\}`** per the Markdown conventions rule above. After applying the fix, CTDC-2040 rendered cleanly on the next push.

**Workflow:**

1. Author Markdown with `\{...\}` escaping anywhere curly braces appear in body text.
2. Push the description via the MCP (`jira_update_issue` with the `description` field).
3. **Verify the render with a UI screenshot from the user**, not the wiki source. Wiki source returned by `jira_get_issue` is unreliable as a render preview.
4. **If rendering is broken**, the most likely cause is unescaped curly braces somewhere in the description. Re-check the source for any `{...}` not preceded by a backslash.
5. **Last-resort fallback:** if the source is clean and the render is still broken, ask the user to re-paste the description through the Jira web UI's Description field. The UI normalizes whatever transport-level state the MCP introduced. This is rarely needed once the curly-brace rule is followed.

**Other fields are safe via MCP.** Priority, summary, custom fields, status transitions, and labels write reliably through `jira_update_issue` because they don't go through the wiki-text renderer.

---

#### 7b-1. 🏛️ Application Pages (Drafted)

> **Use this template for every epic that scopes a CTDC application page or major user-facing surface.** The canonical examples are Home (CTDC-2025), Programs (CTDC-1922), Explore Dashboard (CTDC-1803), Study (CTDC-1645), Study Details (CTDC-1650), Participant Details (CTDC-1644), Cart (CTDC-1074), Static Pages (CTDC-1015), and Program Details (CTDC-2040).

**Why this template**

These are **ongoing, evergreen epics** that remain Open across the life of the project. They serve as containers for all enhancements, bug fixes, and data-integration updates to a given page or surface. The structure makes them readable to engineers, PMs, and federal stakeholders without a Jira learning curve, and the section emojis act as visual anchors when scanning a long description.

**Section order (15 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Do not omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly (e.g., "None at this time") rather than dropping the header — that absence is itself a signal.

1. `### 🎯 **Epic Summary**` — One paragraph: what the epic delivers, the live URL, who consumes it, and the explicit statement that it is an ongoing epic tracking initial implementation, enhancements, bug fixes, and data-integration updates across the life of the CTDC project.
2. `### 🧬 **Context & Background**` — Two paragraphs: (a) what CTDC is and its FAIR mission within CRDC, (b) what this specific page/surface does, what data it surfaces, and where the data comes from (Memgraph + OpenSearch).
3. `### 🏁 **Goal / Objectives**` — Bullet list of 3–5 concrete objectives the page must achieve.
4. `### 🗺️ **Scope**` — Two sub-blocks, **In Scope** and **Out of Scope**, each as a bullet list. In Scope items must be verifiable against the live page (use Playwright to ground claims). Out of Scope items should explicitly point to the epic that covers the excluded work (e.g., *"Cart manifest creation and download (see CTDC-1074)"*).
5. `### 👥 **Stakeholders**` — Bullet list of the standard stakeholder set: Product Owner, Senior TPM (FNL/BACS), NCI/CBIIT Federal Program Leadership, UX/UI Designer, Frontend Engineering Team (ESI), Backend/API Engineering Team (ESI), Data Engineering Team, Data Stewards, QA/Testers. Adjust only if a role is genuinely irrelevant.
6. `### 📖 **Key Definitions / Concepts**` — Glossary of terms specific to the page or surface. Always include **Memgraph** with the parenthetical *"replaces the historical Neo4j references"* and **OpenSearch** when either is involved.
7. `### ✅ **Success Metrics / Acceptance Criteria**` — Two sub-blocks: **Functional** (numbered list) and **Performance & Quality** (numbered list). Functional criteria must be verifiable on Dev/QA/Stage/Prod. Performance & Quality must include WCAG 2.1 AA, design system conformance, performance baselines under realistic data volumes, and automated test coverage.
8. `### 🔗 **Dependencies**` — Bullet list of upstream systems, services, and sibling epics this page relies on. Always name Memgraph + OpenSearch when the page reads CTDC graph data, and the relevant GraphQL resolver families.
9. `### 💭 **Assumptions**` — Bullet list of working assumptions (data model evolves backward-compatibly, content ownership, throughput envelope, etc.).
10. `### 🚧 **Constraints**` — Bullet list of non-negotiables: security/privacy/Section 508, controlled-access protections, performance under growth, external link freshness if applicable.
11. `### ⚠️ **Risks & Mitigations**` — Bullet list. Each item is **Risk:** statement, then **Mitigation:** statement. Cover at least: data drift, performance regression, scope creep from data-model changes, and surface-specific risks (link rot, session-state regression, etc.).
12. `### 🌟 **User Impact**` — Single short paragraph (3–5 sentences) tying the page back to CTDC's FAIR mission and the researcher's actual experience. This is the section that survives in stakeholder summaries.
13. `### 🧩 **Components / Features Breakdown**` — Four sub-blocks, each with a bold sub-heading and bullet list: **UI Components**, **Backend / Data**, **Integration**, **Testing**.
14. `### 📋 **Documentation & Compliance**` — Bullet list: user-facing help content, data dictionary alignment, accessibility conformance review cadence, and any cross-epic integration documentation requirements.
15. `### 📝 **Notes**` — Bullet list. Always include: (a) the standing-epic statement that this remains Open across the project life with child tickets attached for individual enhancements, (b) the cross-reference list to all related application page epics (Home, Programs, Explore Dashboard, Study, Study Details, Participant Details, Cart, Static Pages, Program Details), and (c) any stack-wide reference (e.g., the file download epic CTDC-1764) when relevant.

**Standing emoji set (use these, not substitutes)**

| Section | Emoji | Section | Emoji |
|---|---|---|---|
| Epic Summary | 🎯 | Dependencies | 🔗 |
| Context & Background | 🧬 | Assumptions | 💭 |
| Goal / Objectives | 🏁 | Constraints | 🚧 |
| Scope | 🗺️ | Risks & Mitigations | ⚠️ |
| Stakeholders | 👥 | User Impact | 🌟 |
| Key Definitions | 📖 | Components Breakdown | 🧩 |
| Acceptance Criteria | ✅ | Documentation & Compliance | 📋 |
| | | Notes | 📝 |

**Required content rules (Application Pages specific — universal rules in 7b-shared also apply)**

- **Live URL named in Epic Summary.** Use the production URL (e.g., `https://clinical.datacommons.cancer.gov/#/studies`). For pages that don't yet exist in production, state explicitly that the page is forward-looking and note what entry point the page will have when delivered.
- **Live UI verified before drafting.** Use Playwright (`browser_navigate` + `browser_snapshot`) on the production URL. If the route 404s because the page is forward-looking, verify the *adjacent* page that will provide the entry point (e.g., the Explore Dashboard's Participants tab for a future Participant Details page) and explicitly note the future-state stance in the epic.
- **Cross-epic references threaded.** Out of Scope, Dependencies, and Notes must point to the related Application Pages epics and to CTDC-1764 (file download stack) when relevant.
- **WCAG 2.1 AA + design system + performance baselines + automated tests** appear in Performance & Quality, every time.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text. URLs with path parameters, scope items naming a variable, acceptance criteria — all of them. See 7b-shared "Markdown conventions" and "MCP write notes."

**Writing-and-publishing workflow**

1. **Verify the live page** with Playwright before drafting. Note the actual route, headers, tabs, widgets, table columns, and external links.
2. **Draft the description** in Markdown with all 15 sections in order, applying the section emojis and content rules above. Apply the universal Markdown conventions from 7b-shared, including the curly-brace escaping rule.
3. **Push the description via the MCP** (`jira_update_issue` with the `description` field).
4. **Set non-description fields via the MCP** in the same call: `priority` to Major, plus any other fields you intend to set. Never include `labels` unless deliberately changing them.
5. **Verify the rendered description with a UI screenshot** from the user. The wiki source is unreliable as a render preview.
6. **If rendering is broken**, first re-check the Markdown source for any unescaped `{...}` — that's the most likely cause. If the source is clean and the render is still broken, fall back to UI paste.
7. **Update the related-epics cross-reference list** in the Notes section of every other Application Pages epic when a new page epic is added.

---

#### 7b-2. 🛠️ Microservices (Stub — Template TBD)

> **Template not yet defined.** To be drafted in a focused session with a real CTDC microservice epic as the model. Candidate examples: the file service, the authn service, the backend GraphQL service. Until this template is drafted, microservice epics should follow the 7b-shared universal conventions and the most-relevant adjacent template (typically 7b-5 Infrastructure or 7b-6 Security).

When drafting this template, expected sections likely include API contract, service boundaries, deployment topology, dependency on other services, observability/SLOs, version compatibility, and cross-environment behavior. Final structure to be confirmed against a real example.

---

#### 7b-3. 🔄 Features (Stub — Template TBD)

> **Template not yet defined.** To be drafted in a focused session with a real CTDC feature epic as the model. Candidate examples: file download flows that span Explore + Cart + Participant Details, manifest export integration with downstream tools, global search. Until this template is drafted, feature epics should follow the 7b-shared universal conventions and use 7b-1 Application Pages as the closest analog.

When drafting this template, expected sections likely emphasize cross-page surface area, user journey, the spread of UI/Backend/Data work, and the seam between feature delivery and ongoing page-level epic ownership.

---

#### 7b-4. 📦 Products (Stub — Template TBD)

> **Template not yet defined.** To be drafted in a focused session with a real CTDC product epic as the model. Candidate examples: the megazip ZIP artifact (CTDC-1924 / CTDC-1926 lineage), the CTDC application as a whole, manifest format spec as a versioned product. Until this template is drafted, product epics should follow the 7b-shared universal conventions.

When drafting this template, expected sections likely emphasize the product contract with external consumers, versioning policy, backward compatibility, the consumer support obligation, and the difference between product evolution and product maintenance.

---

#### 7b-5. 🚧 Infrastructure (Stub — Template TBD)

> **Template not yet defined.** To be drafted in a focused session with a real CTDC infrastructure epic as the model. Candidate examples: Jenkins migration work, environment provisioning, deployment topology changes, observability rollouts. Until this template is drafted, infrastructure epics should follow the 7b-shared universal conventions.

When drafting this template, expected sections likely emphasize environments (Dev/QA/Stage/Prod), deployment pipelines, runtime topology, observability/alerting, rollback strategy, and the difference between landed infrastructure and ongoing infrastructure operations.

---

#### 7b-6. 🔒 Security (Stub — Template TBD)

> **Template not yet defined.** To be drafted in a focused session with a real CTDC security epic as the model. Candidate examples: RAS-enabled file download (CTDC-1764), session timeout configuration, audit log gap remediation, FedRAMP control implementations. Until this template is drafted, security epics should follow the 7b-shared universal conventions.

When drafting this template, expected sections likely emphasize threat model, authn/authz boundaries, audit logging requirements, controlled-access protections, compliance citations (NIST, FedRAMP, Section 508), and verification through penetration testing or compliance review.

---

#### 7b-7. 🧬 Data (Stub — Template TBD)

> **Template not yet defined.** To be drafted in a focused session with a real CTDC data epic as the model. Candidate examples: CMB v5 release (CTDC-1753), Memgraph schema changes, OpenSearch index updates, data dictionary additions. Until this template is drafted, data epics should follow the 7b-shared universal conventions.

When drafting this template, expected sections likely emphasize the data model delta, ingestion path, validation against the data dictionary, downstream consumer impact (which pages/features rely on the data), release cadence, and the difference between data releases and software releases (which CTDC tracks separately).

---

### 7c. Bug Format
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
- When a per-grouping epic template (7b-1 through 7b-7) evolves — new sections, emoji changes, content rules, or new gotchas — update the template here AND consider a normalization pass across all existing epics in that grouping so they stay consistent

### 9a. Epic Template Status Tracker

Track which per-grouping epic templates are drafted vs. still TBD. Each future session that drafts a new template fills in that row.

| Grouping | Template Status | Example Epic for Drafting |
|---|---|---|
| Application Pages (7b-1) | ✅ Drafted v1 | CTDC-2025 (Home — gold standard) |
| Microservices (7b-2) | 🚧 TBD | TBD |
| Features (7b-3) | 🚧 TBD | TBD |
| Products (7b-4) | 🚧 TBD | TBD |
| Infrastructure (7b-5) | 🚧 TBD | TBD |
| Security (7b-6) | 🚧 TBD | TBD |
| Data (7b-7) | 🚧 TBD | TBD |

### 9b. Lessons Learned from 2026-04-30 Normalization Pass

- **Wiki source is not ground truth for rendering.** Only a UI screenshot from the user reveals whether a Jira description renders correctly. The wiki source returned by `jira_get_issue` looks identical for working and broken tickets.
- **The verified description-render bug is unescaped curly braces.** Jira-wiki treats `{...}` as macro syntax (`{code}`, `{noformat}`, `{panel}`, etc.). When the renderer hits an unknown macro name like `{program_short_name}`, parser state corrupts for the surrounding block and the damage propagates *forward* through the document — adjacent headers lose their `h3.` recognition, bullets lose their list context, and some headers disappear entirely. The fix is to escape every curly brace in description text as `\{...\}`. CTDC-2040 (Program Details Page) was the test case that proved this; pre-fix push corrupted ~half the document, post-fix push rendered cleanly.
- **Earlier suspected causes were wrong.** Before the curly-brace finding, several theories were advanced and abandoned: mismatched bold delimiters, indented bullets, backtick code spans, asterisk-vs-underscore emphasis, and most prominently CRLF/LF line endings. Each theory was confidently asserted in SKILL.md until the next data point falsified it. CTDC-1645's stored description has every one of those "defects" and renders perfectly. The line-endings theory was particularly seductive because it explained the apparent nondeterminism — but the real explanation is that some epic descriptions happened to contain `{...}` patterns and others didn't, which looked random because nobody was inspecting the body text for macro syntax.
- **Cascade-from-a-point pattern indicates parser-state corruption, not a transport bug.** When render breakage propagates *forward* from a specific point in the document — paragraphs fracturing right where some specific token appears, then everything after that point breaking — the cause is parser-state corruption from that token, not anything to do with how the bytes were transported. Look for unescaped wiki macro syntax first.
- **Theory thrash is a smell.** When debugging without ground-truth screenshots, multiple "this is the bug" theories in a row, each falsified by the next data point, mean the methodology is wrong, not just the theory. Stop pushing changes and ask for a screenshot before continuing. The 2026-04-30 session went through three wrong theories (CRLF/LF, underscore-italic, then a confused mix of both) before the correct cause was identified by reading the actual cascade pattern in a screenshot dump.

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

### ⚠️ Description Field — Escape Curly Braces

**Curly braces `{...}` in description text are treated as Jira-wiki macro syntax** and corrupt parser state for the surrounding block when the macro name is unknown. Symptoms include adjacent headers rendering as literal `h3.` text, bullets losing list context, and entire sections disappearing. **Fix:** escape every curly brace in description text as `\{...\}`. See Section 7b-shared "MCP write notes" for the full diagnostic and workflow. Other fields (priority, summary, custom fields, status, labels) are not routed through the wiki renderer and are unaffected.

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

### 🔗 Issue Link Type Names

When linking issues with `jira_create_issue_link`, this tracker uses the following confirmed link type names. Some commonly-tried names do NOT exist here.

| Link Type | Status | Notes |
|---|---|---|
| `Blocks` | ✅ Works | Use this to express "ticket A blocks ticket B" — pass A as `inward_issue_key`, B as `outward_issue_key` |
| `is blocked by` | ❌ Fails | "No issue link type with name 'is blocked by' found" — use `Blocks` with the issue order reversed instead |

**Rule of thumb:** When a link operation needs to express a blocking relationship, always use `Blocks` and reason about which issue is the inward (the blocker) vs the outward (the blocked). Don't try to use the inverse phrasing — it fails on this instance.

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

---

## 15. 🛠️ CTDC Backend Repo Reference

> **Critical for backend tickets.** When writing backend stories or investigating backend behavior, use this section to ground file paths in reality. Do not guess.

### 15a. Repo Identity

| Property | Value |
|---|---|
| **Repo URL** | `https://github.com/CBIIT/crdc-ctdc-backend` |
| **Default branch** | `master` (NOT `main` — different from the frontend) |
| **Build system** | Maven (`pom.xml`, `mvnw`) |
| **Language / framework** | Java 11+ / Spring Boot |
| **Containerization** | Dockerfile at repo root |

### 15b. Architecture — Submodule Pattern

The backend is split between **shared core code** (a git submodule) and **CTDC-specific code**:

| Java Package | Source | Purpose |
|---|---|---|
| `gov.nih.nci.bento` | Git submodule → `CBIIT/bento-backend-core` | Shared Bento Framework backend code (do not edit here) |
| `gov.nih.nci.bento_ri` | Local — in this repo | CTDC-specific extensions, resolvers, and overrides |

**When a ticket says "edit the DataFetcher,"** the engineer should be looking at `bento_ri`, not `bento`. Changes to `bento` flow upstream through the submodule — that's a separate, broader change.

### 15c. Key File Locations (verified)

| What | Path |
|---|---|
| **GraphQL schemas** | `src/main/resources/graphql/` |
| ↳ Main CTDC private (authenticated) schema | `src/main/resources/graphql/crdc-ctdc-private-es.graphql` |
| ↳ CTDC public schema | `src/main/resources/graphql/crdc-ctdc-public-es.graphql` |
| ↳ CTDC full ES schema | `src/main/resources/graphql/crdc-ctdc-es-full.graphql` |
| ↳ Bento-extended shared schemas | `src/main/resources/graphql/bento-extended*.graphql` |
| **OpenSearch index mappings** | `src/main/resources/yaml/` |
| ↳ CTDC-specific index definitions | `src/main/resources/yaml/es_indices_ctdc.yml` |
| ↳ Bento shared index definitions | `src/main/resources/yaml/es_indices_bento.yml` |
| ↳ Facet search config | `src/main/resources/yaml/facet_search_es.yml` |
| ↳ Global search config | `src/main/resources/yaml/global_search_es.yml` |
| ↳ Single search config | `src/main/resources/yaml/single_search_es.yml` |
| ↳ Query specifications | `src/main/resources/yaml/queries-specification.yml` |
| **Java DataFetchers (CTDC-specific)** | `src/main/java/gov/nih/nci/bento_ri/model/` |
| ↳ Authenticated/private resolver — main one to edit for dashboard work | `PrivateESDataFetcher.java` |
| ↳ Public resolver | `PublicESDataFetcher.java` |
| **Spring Boot config** | `src/main/resources/application.properties` |

### 15d. Stack — Real vs README

The repo `README.md` describes the system using **Neo4j + GraphQL plugin** terminology. **That is outdated.** The active CTDC stack is:

| Component | Reality | README Says |
|---|---|---|
| Graph database | **Memgraph** (migrated from Neo4j) | Neo4j |
| Search/aggregation | **OpenSearch** | (not mentioned) |
| GraphQL plugin | Custom Java DataFetchers | "Neo4j GraphQL plugin" |

Treat the README as historical context only. When writing tickets that reference the data path, reference Memgraph and OpenSearch, not Neo4j and the GraphQL plugin. The OpenSearch index YAMLs in `src/main/resources/yaml/` are the authoritative source for what fields are indexed and queryable.

### 15e. Comparison with ICDC Backend

When mirroring an ICDC feature into CTDC, the equivalent files are:

| CTDC | ICDC Reference |
|---|---|
| `CBIIT/crdc-ctdc-backend` | `CBIIT/bento-icdc-backend` |
| `src/main/resources/graphql/crdc-ctdc-private-es.graphql` | `src/main/resources/graphql/icdc.graphql` |
| `src/main/resources/yaml/es_indices_ctdc.yml` | `src/main/resources/yaml/es_indices_icdc.yml` |
| `gov.nih.nci.bento_ri.model.PrivateESDataFetcher` | (same package path in ICDC repo) |

This mapping is helpful when writing tickets that say "implement the ICDC pattern in CTDC" — point the engineer at both files in their respective repos for direct diffing.

### 15f. Search Tip — Don't Use Default GitHub Search Alone

GitHub's code search across `org:CBIIT` does not always surface this repo for terms like `searchParticipants` or `numberOfFiles` (verified 2026-04-27 — zero hits). When investigating CTDC backend behavior, **scope your search to this repo explicitly**:

```
repo:CBIIT/crdc-ctdc-backend <term>
```

Or use the GitHub UI directly on the repo. Org-scoped searches can quietly miss this repo because of indexing quirks with the submodule structure.

---

## 16. 🧬 CTDC Data Model Reference

> **Critical for backend tickets that touch the graph.** Backend tickets often hinge on whether a relationship exists between two node types. Verify against this section before writing dependency blocks like "data model PR required" — that prerequisite is sometimes already satisfied.

### 16a. Repo Identity

| Property | Value |
|---|---|
| **Repo URL** | `https://github.com/CBIIT/ctdc-model` |
| **Default branch** | `prod` (NOT `main` or `master` — different from both frontend and backend) |
| **Current version** | `v1.22.2` (verified 2026-04-27 — check `Version:` line at top of model file for latest) |

### 16b. Key File Locations

| What | Path |
|---|---|
| **Node + relationship definitions** | `model-desc/ctdc_model_file.yaml` |
| **Property definitions (CDE references, value sets, types)** | `model-desc/ctdc_model_properties_file.yaml` |
| **Visual model diagram (SVG)** | `model-desc/ctdc-model.svg` |
| **Loader configurations** | `model-desc/load/` |

When a ticket needs to know whether a relationship exists, the answer lives in `ctdc_model_file.yaml` under the `Relationships:` block. Read that file directly — never guess.

### 16c. The `associated_with` Relationship Pattern

**This is the single most important graph-modeling fact about CTDC.** CTDC consolidates many file relationships under a single relationship name (`associated_with`), distinguished only by source/destination node type. ICDC takes the opposite approach — every relationship has its own name (`of_case`, `of_study`, `of_sample`, `from_diagnosis`, etc.).

**CTDC's `associated_with` relationship has these `Src`/`Dst` pairs** (verified in v1.22.2):

| Source | Destination | Meaning |
|---|---|---|
| `file` | `specimen` | File attached to a specimen |
| `file` | `diagnosis` | File attached to a diagnosis |
| `file` | `participant` | File attached to a participant |
| `file` | `study` | **File attached directly to a study** (study-level files) |
| `associated_link` | `study` | External link about a study |
| `image_collection` | `study` | Image collection about a study |
| `file` | `assay` | File attached to an assay |
| `assay` | `specimen` | Assay run on a specimen |

**Implication for Cypher queries:** scope by destination label, not by relationship name. Example for "files attached directly to a study":

```cypher
MATCH (f:file)-[:associated_with]->(s:study)
RETURN f
```

**The same query in ICDC** would use a different relationship name (`of_study`):

```cypher
MATCH (f:file)-[:of_study]->(s:study)
RETURN f
```

When mirroring an ICDC backend pattern in CTDC, **always translate the relationship name** — do not copy ICDC's `of_*` patterns verbatim.

### 16d. Verification Workflow for "Does this relationship exist?"

When a backend ticket needs to know whether a given relationship exists between two node types:

1. Open `ctdc_model_file.yaml` on the `prod` branch
2. Find the `Relationships:` block (toward the bottom of the file)
3. Search for the `Src:` / `Dst:` pair you care about
4. If it exists, **no model PR is needed** — proceed with the backend Task
5. If it doesn't exist, the relationship must be added first via a model PR (coordinate with Patrick / data engineering)

**Example from the CTDC-2034 work:** The frontend Study Files tab needs the backend to return files attached directly to a study. Question: does `file → study` exist as a direct relationship? Searched `ctdc_model_file.yaml`, found `Src: file, Dst: study` under `associated_with`. Answer: yes. No model PR needed. Removed the upstream-data-model dependency from the Task.

### 16e. Comparison with ICDC Data Model

| Property | CTDC | ICDC |
|---|---|---|
| **Repo** | `CBIIT/ctdc-model` | `CBIIT/icdc-model` |
| **Default branch** | `prod` | (verify before assuming) |
| **File relationship convention** | Single `associated_with` relationship, distinguished by `Src`/`Dst` node types | Per-destination naming (`of_case`, `of_study`, `of_sample`, `from_diagnosis`, etc.) |
| **Cypher idiom for study files** | `MATCH (f:file)-[:associated_with]->(s:study)` | `MATCH (f:file)-[:of_study]->(s:study)` (or anonymous `-->`) |

**The takeaway:** never copy ICDC Cypher into CTDC verbatim. The semantic intent is portable; the relationship labels are not.

### 16f. Other Useful Knowledge for Backend Work

- **`participant`** is CTDC's equivalent of ICDC's **`case`**. When ICDC documentation refers to "case files" or "case-attached files," the CTDC translation is "participant-attached files" (going through `file -[:associated_with]-> participant`).
- **`specimen`** is CTDC's equivalent of ICDC's **`sample`**.
- **CTDC file UUID field** is `data_file_uuid`. **ICDC file UUID field** is `file_uuid`. These are not interchangeable — adjust query results and frontend keys accordingly.
- **CTDC study identifier field** is `study_accession`. **ICDC study identifier field** is `clinical_study_designation`. Translate when porting queries.
