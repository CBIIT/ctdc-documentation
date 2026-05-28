---
name: ctdc-sprint-command-center
description: "Operational knowledge base for the CTDC Sprint Command Center Claude project. Contains SOPs, workflow templates, JQL recipes, stakeholder doc standards, demo day artifacts, retro format, deck standards, and session recovery procedures for the Clinical and Translational Data Commons (CTDC) engineering team."
---

# 🚀 CTDC Sprint Command Center — Skill Knowledge Base

> **Project:** Clinical and Translational Data Commons (CTDC)
> **Ecosystem:** Cancer Research Data Commons (CRDC)
> **Team:** React web application engineers
> **Claude Project:** Sprint Command Center
> **Last Updated:** 2026-05-27

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
- **Canonical entry point:** `CBIIT/crdc-ctdc-starter-kit` — a meta-repo that pins all CTDC component repos as git submodules. This is the source of truth for which repos are part of the active CTDC system. See Section 17.
- **Key repos (active):** `crdc-ctdc-ui`, `crdc-ctdc-backend`, `crdc-ctdc-files`, `crdc-ctdc-auth`, `crdc-ctdc-dataloader`, `crdc-ctdc-interoperation`, `bento-ctdc-static-content`, `ctdc-deployment`, `ctdc-model`, `ctdc-readMe-content`
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

### 7a. 📖 User Story Template (Drafted)

> **Use this template for every CTDC user story.** The canonical example is **CTDC-1691 (End User can find specific Participant IDs using an Input Set)** — drafted 2026-05-06 as a child of CTDC-2042 (Local Find — Participant). The template is deliberately scoped at story level, not epic level. It inherits the 7b-shared universal conventions (Markdown authoring, curly-brace escaping, render verification by UI screenshot), so most of the gotchas are covered there.

**Why this template**

User stories are smaller-scope deliverables than epics — they ship and close, rather than living forever as evergreen containers. The bare-bones As-a/I-want/So-that format is a starting point, but it leaves the engineer without enough structure to know when a story is *done*. This template gives every story:

- A one-sentence summary the engineer can read in five seconds and know what's being asked
- The classic As-a/I-want/So-that framing intact, written as flowing prose
- A Jira-native Epic Link (customfield_12350) so the *why* is one click away from the rendered ticket
- An In/Out of Scope split that prevents adjacent-feature creep
- Acceptance Criteria split into Functional and Performance & Quality, mirroring the epic AC pattern
- A dedicated Testing Requirements section so unit/integration/manual coverage is named explicitly, not buried in a P&Q bullet
- A Notes section for upstream provenance, predecessor links, and composition rules

The emoji set deliberately overlaps with the epic templates (🎯 🗺️ ✅ 📝) so a reader scanning a story and its parent epic side-by-side sees the same visual anchors. The two new ones are 👤 (User Story) and 🧪 (Testing Requirements) — both unique to the story scale.

**Section order (6 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header — same rule as the epic templates.

1. `### 🎯 **Story Summary**` — One sentence: what this story delivers, who consumes it, on which surface. Example: *"This story delivers the Upload Participant Set capability on the Explore Dashboard, letting researchers paste or upload a list of Participant IDs to seed a cohort directly."*

2. `### 👤 **User Story**` — The classic As-a / I-want / So-that, written as flowing prose. One paragraph, not three bullet lines. Example: *"As an end user with a known list of Participant IDs relevant to my research, I want to paste or upload that list directly into CTDC so that I can seed a cohort without having to rebuild it from scratch using filters."*

3. `### 🗺️ **Scope**` — Two sub-blocks, **In Scope** and **Out of Scope**, each as a bullet list. Same shape as 7b-1 (Application Pages epics) but trimmed. Out of Scope items should point to sibling stories or epics that cover excluded work when one exists.

4. `### ✅ **Acceptance Criteria**` — Two sub-blocks, mirroring the epic AC pattern:
   - **Functional** — Numbered list. Each item is verifiable by QA on a deployed environment. Use plain English with `**bold**` on key UI labels and component names. Escape every curly brace as `\{...\}` if path parameters or variable names appear.
   - **Performance & Quality** — Numbered list with the standard CTDC quality bar:
     1. WCAG 2.1 AA accessibility on all new UI elements
     2. Design system conformance (colors, typography, spacing, button states)
     3. Performance baseline maintained under realistic data volumes
     4. Cross-browser parity on supported browsers (Chrome, Firefox, Safari, Edge)

5. `### 🧪 **Testing Requirements**` — Three sub-blocks naming the test artifacts the dev needs to produce. Test coverage is a first-class deliverable on this team — Valentina is QA-only and does not write code, so every line of automated test that covers this story has to be written by the engineer. Burying that work in a single P&Q bullet hides the actual scope of what's being asked.
   - **Unit Tests** — Numbered list of unit test surfaces (parsers, match logic, state reducers, utility functions). Each item is a single observable behavior, testable in isolation.
   - **Integration Tests** — Numbered list of end-to-end flows that exercise multiple components together (modal flows, file upload flows, post-submit state changes, re-open flows, clear flows, error paths).
   - **Manual QA Scenarios** — Numbered list of things that are hard to automate but still need verification: tooltip text content, cross-browser visual parity, accessibility audits (keyboard navigation, screen reader announcements, focus management), visual conformance against the design system.

6. `### 📝 **Notes**` — Bullet list. Optional content: design references, technical hints the engineer needs, decisions already made, links to mockups or Figma, predecessor tickets, upstream package provenance, composition rules with adjacent features. If there's no meaningful note, write *"None at this time."* The parent epic is the canonical link via the Jira `customfield_12350` Epic Link field and the Jira UI exposes it directly — do not repeat the parent epic in the description body.

**Standing emoji set (6 entries)**

| Section | Emoji |
|---|---|
| Story Summary | 🎯 |
| User Story | 👤 *(unique to story scale)* |
| Scope | 🗺️ |
| Acceptance Criteria | ✅ |
| Testing Requirements | 🧪 *(unique to story scale)* |
| Notes | 📝 |

**Required content rules (Story specific — universal rules in 7b-shared also apply)**

- **Story Summary names the surface.** Same as epic Surface Area / live URL — name the page or component the story touches (e.g., "Explore Dashboard," "Local Find Box on the Explore Dashboard," "Cart drawer").
- **Parent Epic field set on the ticket itself.** Use `customfield_12350` per Section 10. The Jira-rendered Epic Link is the canonical link readers follow; the description body does not duplicate it. Verified 2026-05-27 on CTDC-2064 and CTDC-2065 — removing the Parent Epic & Context section trimmed visual noise without losing context, because the Jira UI shows the Epic Link prominently.
- **Acceptance Criteria are testable.** Each Functional item must be a single observable behavior QA can pass/fail in one check. If an item has two clauses joined by "and," consider splitting.
- **Testing Requirements is the dev's checklist before "Ready for QA."** It is not optional and not a duplicate of P&Q. P&Q says "the work must meet the bar"; Testing Requirements says "these are the test artifacts the dev produces to demonstrate the work meets the bar." Different audiences, different verification paths.
- **Performance & Quality is non-negotiable** — every user-facing story carries the same bar. Don't trim it because the story feels small. Note: P&Q in this template is shortened compared to the epic version because the test-coverage line moved into Testing Requirements; the four remaining items (WCAG, design system, performance, cross-browser) are still mandatory.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as epics. Especially relevant in Notes when referencing variable names like `\{participant_id\}`.
- **Upstream provenance threaded into Notes** when the story is part of a feature built on an upstream package. Routes bug fixes correctly between CTDC integration code and upstream package code.

**Writing-and-publishing workflow**

1. Pull the existing ticket via `jira_get_issue` to capture the original requirements text — preserve the substance, restructure the form. Fix typos and tighten wordings; don't drop requirements.
2. Confirm the parent epic link via `customfield_12350` on the ticket itself; if missing, set it before pushing the description.
3. Draft the description in Markdown with all 6 sections in order, applying the section emojis and content rules above.
4. Push the description via `jira_update_issue` with the `description` field.
5. Verify the rendered description with a UI screenshot from the user — wiki source is unreliable as a render preview (per 7b-shared).
6. If rendering is broken, first re-check the Markdown source for any unescaped `{...}`. Then check for the Markdown-vs-Jira-wiki authoring confusion (asterisks rendering as italic instead of bold).

**When to expand vs trim**

- **Story has fewer than 5 functional ACs and no test surface complexity** → keep all 6 sections; the structure earns its keep on small stories by giving the engineer a checklist.
- **Story is a pure documentation update or copy change with no code path** → Testing Requirements may legitimately be light (manual QA only, no Unit or Integration sub-blocks needed). State so explicitly with "None at this time" under the empty sub-blocks rather than dropping them.
- **Story is a spike or research task with no acceptance criteria** → this template is the wrong shape. Use the Bug Format pattern adapted for spikes, or write a free-form Task description with explicit "deliverables" rather than "acceptance criteria."

---

### 7b. 🏛️ Epic Templates by Grouping

CTDC epics fall into seven groupings, each with its own template tuned to that grouping's actual concerns. Section **7b-shared** holds the universal rules that apply to **every** template. Sections **7b-1** through **7b-7** hold the per-grouping templates. Two templates are currently drafted (7b-1 Application Pages and 7b-3 Features); the other five are stubs to be drafted in focused sessions with a real CTDC example epic in hand.

**The seven groupings:**

1. **Application Pages** (7b-1) — User-facing pages and surfaces in the CTDC web application (Home, Programs, Explore Dashboard, Study, Study Details, Participant Details, Cart, Static Pages, Program Details).
2. **Microservices** (7b-2) — Backend services with their own API contract, deployment, and lifecycle (e.g., the file service, the authn service, the backend GraphQL service).
3. **Features** (7b-3) — Cross-cutting capabilities that span multiple pages or services (e.g., Local Find, file download flows, manifest export, search).
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

**Feature vs Product disambiguation when an upstream package is involved.** This is a recurring decision because CTDC consumes several upstream Bento Core npm packages (`@bento-core/local-find`, `@bento-core/cart`, `@bento-core/authentication`, `@bento-core/header`, `@bento-core/footer`, `@bento-core/facet-filter`, `@bento-core/paginated-table`, and others). The decision rule:

- If CTDC consumes an upstream package built by another team (e.g., the Bento Core team) and CTDC's work is **integrating, adapting, and operating** that capability within CTDC, the CTDC epic is a **Feature** epic. Document the upstream origin in the dedicated `📦 Upstream Provenance` section of the Features template (7b-3). The upstream team may have a Product epic in their own project tracking the package itself; that is not CTDC's concern.
- Reserve **Product** epics (7b-4) for cases where CTDC is the primary builder of a deliverable consumed by external systems (e.g., the megazip artifact, the CTDC application as a whole as a deliverable to NCI, manifest format spec as a versioned product).
- The **audience for CTDC's epic** is CTDC's engineering team and CTDC's stakeholders — not the upstream maintainers. That audience perspective is what makes Local Find a Feature, not a Product, in CTDC's Jira project.

---

[NOTE: SKILL.md sections 7b-shared through 17 unchanged from prior version — only Section 7a (User Story Template), Section 9a (Template Status Tracker), Section 9d (lessons-learned 2026-05-06), and Section 9g (new lessons-learned 2026-05-27) were modified. This commit truncated in tool input — see follow-up commit.]
