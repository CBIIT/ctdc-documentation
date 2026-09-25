---
name: ctdc-sprint-command-center
description: "Operational knowledge base for the CTDC Sprint Command Center Claude project. Contains SOPs, workflow templates, JQL recipes, stakeholder doc standards, demo day artifacts, retro format, deck standards, and session recovery procedures for the Clinical and Translational Data Commons (CTDC) engineering team."
---

# 🚀 CTDC Sprint Command Center — Skill Knowledge Base

> **Project:** Clinical and Translational Data Commons (CTDC)
> **Ecosystem:** Cancer Research Data Commons (CRDC)
> **Team:** React web application engineers
> **Claude Project:** Sprint Command Center
> **Last Updated:** 2026-09-25

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
- **Key repos (active):** `crdc-ctdc-ui`, `crdc-ctdc-backend`, `crdc-ctdc-files`, `crdc-ctdc-authn`, `crdc-ctdc-dataloader`, `crdc-ctdc-interoperation`, `bento-ctdc-static-content`, `ctdc-deployment`, `ctdc-model`, `ctdc-readMe-content`
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

### 7a. 📖 User Story Template (Drafted v2)

> **Use this template for every CTDC user story.** The canonical example is **CTDC-1691 (End User can find specific Participant IDs using an Input Set)**: drafted 2026-05-06 as a child of CTDC-2042 (Local Find: Participant). The template is deliberately scoped at story level, not epic level. It inherits the 7b-shared universal conventions (Markdown authoring, curly-brace escaping, render verification by UI screenshot), so most of the gotchas are covered there.
>
> **v2 (2026-09-04): slimmed from 7 to 5 sections, no Jira keys in the body, no em dashes.** The 🔗 Parent Epic & Context section was removed (the Epic Link field `customfield_12350` is the canonical link; restating it in the body is noise) and the 📝 Notes section was removed (predecessor tickets, design tasks, Figma links, and decisions are carried by native Jira links, remote links, and comments: the same reasoning that slimmed the Design Task template to v2 on 2026-07-07). ICDC adopted the identical 5-section shape the same day (`CBIIT/icdc-documentation → claude/templates/user-story-template.md`, canonical ICDC-4244) so the two projects stay aligned. CTDC-1691 still carries the v1 7-section body; retrofit is optional: new stories use v2.

**Why this template**

User stories are smaller-scope deliverables than epics: they ship and close within a sprint or two, where an epic scopes a whole phase of work. The bare-bones As-a/I-want/So-that format is a starting point, but it leaves the engineer without enough structure to know when a story is *done*. This template gives every story:

- A one-sentence summary the engineer can read in five seconds and know what's being asked
- The classic As-a/I-want/So-that framing intact, written as flowing prose
- An In/Out of Scope split that prevents adjacent-feature creep
- Acceptance Criteria split into Functional and Performance & Quality, mirroring the epic AC pattern
- A dedicated Testing Requirements section so unit/integration/manual coverage is named explicitly, not buried in a P&Q bullet

The emoji set deliberately overlaps with the epic templates (🎯 🗺️ ✅) so a reader scanning a story and its parent epic side-by-side sees the same visual anchors. The two unique ones are 👤 (User Story) and 🧪 (Testing Requirements): both unique to the story scale.

**Section order (5 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header: same rule as the epic templates.

1. `### 🎯 **Story Summary**`: One sentence: what this story delivers, who consumes it, on which surface. Example: *"This story delivers the Upload Participant Set capability on the Explore Dashboard, letting researchers paste or upload a list of Participant IDs to seed a cohort directly."*

2. `### 👤 **User Story**`: The classic As-a / I-want / So-that, written as flowing prose. One paragraph, not three bullet lines. Example: *"As an end user with a known list of Participant IDs relevant to my research, I want to paste or upload that list directly into CTDC so that I can seed a cohort without having to rebuild it from scratch using filters."*

3. `### 🗺️ **Scope**`: Two sub-blocks, **In Scope** and **Out of Scope**, each as a bullet list. Same shape as the 7b epic template. Out of Scope items describe the excluded work in words; do not cite the Jira key of the story or epic that covers it (add a `Relates` link instead).

4. `### ✅ **Acceptance Criteria**`: Two sub-blocks, mirroring the epic AC pattern:
   - **Functional**: Numbered list. Each item is verifiable by QA on a deployed environment. Use plain English with `**bold**` on key UI labels and component names. Escape every curly brace as `\{...\}` if path parameters or variable names appear.
   - **Performance & Quality**: Numbered list with the standard CTDC quality bar:
     1. WCAG 2.1 AA accessibility on all new UI elements
     2. Design system conformance (colors, typography, spacing, button states)
     3. Performance baseline maintained under realistic data volumes
     4. Cross-browser parity on supported browsers (Chrome, Firefox, Safari, Edge)

5. `### 🧪 **Testing Requirements**`: Three sub-blocks naming the test artifacts the dev needs to produce. Test coverage is a first-class deliverable on this team: Valentina is QA-only and does not write code, so every line of automated test that covers this story has to be written by the engineer. Burying that work in a single P&Q bullet hides the actual scope of what's being asked.
   - **Unit Tests**: Numbered list of unit test surfaces (parsers, match logic, state reducers, utility functions). Each item is a single observable behavior, testable in isolation.
   - **Integration Tests**: Numbered list of end-to-end flows that exercise multiple components together (modal flows, file upload flows, post-submit state changes, re-open flows, clear flows, error paths).
   - **Manual QA Scenarios**: Numbered list of things that are hard to automate but still need verification: tooltip text content, cross-browser visual parity, accessibility audits (keyboard navigation, screen reader announcements, focus management), visual conformance against the design system.

**Standing emoji set (5 entries)**

| Section | Emoji |
|---|---|
| Story Summary | 🎯 |
| User Story | 👤 *(unique to story scale)* |
| Scope | 🗺️ |
| Acceptance Criteria | ✅ |
| Testing Requirements | 🧪 *(unique to story scale)* |

**Required content rules (Story specific: universal rules in 7b-shared also apply)**

- **Story Summary names the surface.** Same as epic Surface Area / live URL: name the page or component the story touches (e.g., "Explore Dashboard," "Local Find Box on the Explore Dashboard," "Cart drawer").
- **Parent Epic field set on the ticket itself.** Use `customfield_12350` per Section 10. There is no Parent Epic line in the body: the field is the only link.
- **No Jira ticket keys anywhere in the body.** Design tasks, predecessor stories, and sibling stories are `Relates` issue links; Figma and upstream-package references are remote links or comments. Jira's Links panel is the single source; repeating keys in the text is redundant and goes stale. There is no Notes section.
- **Acceptance Criteria are testable.** Each Functional item must be a single observable behavior QA can pass/fail in one check. If an item has two clauses joined by "and," consider splitting.
- **Testing Requirements is the dev's checklist before "Ready for QA."** It is not optional and not a duplicate of P&Q. P&Q says "the work must meet the bar"; Testing Requirements says "these are the test artifacts the dev produces to demonstrate the work meets the bar." Different audiences, different verification paths.
- **Performance & Quality is non-negotiable**: every user-facing story carries the same bar. Don't trim it because the story feels small.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text: same rule as epics.

**Writing-and-publishing workflow**

1. Pull the existing ticket via `jira_get_issue` to capture the original requirements text: preserve the substance, restructure the form. Fix typos and tighten wordings; don't drop requirements.
2. Confirm the parent epic link via `customfield_12350` on the ticket itself; if missing, set it before pushing the description.
3. Draft the description in Markdown with all 5 sections in order, applying the section emojis and content rules above.
4. Push the description via `jira_update_issue` with the `description` field.
5. Add `Relates` issue links to the design task and any predecessor or sibling stories via `jira_create_issue_link`.
6. Verify the rendered description with a UI screenshot from the user: wiki source is unreliable as a render preview (per 7b-shared).
7. If rendering is broken, first re-check the Markdown source for any unescaped `{...}`. Then check for the Markdown-vs-Jira-wiki authoring confusion (asterisks rendering as italic instead of bold).

**When to expand vs trim**

- **Story has fewer than 5 functional ACs and no test surface complexity** → keep all 5 sections; the structure earns its keep on small stories by giving the engineer a checklist.
- **Story is a pure documentation update or copy change with no code path** → Testing Requirements may legitimately be light (manual QA only, no Unit or Integration sub-blocks needed). State so explicitly with "None at this time" under the empty sub-blocks rather than dropping them.
- **Story is a spike or research task with no acceptance criteria** → this template is the wrong shape. Use the Bug Format pattern adapted for spikes, or write a free-form Task description with explicit "deliverables" rather than "acceptance criteria."

### 7b. 🏛️ Epic Template (Lean v2)

> **Use this template for every CTDC epic**, whatever its grouping (application page, microservice, feature, product, infrastructure, security), except the two standing data modeling epics, which use the data variant in 7b-D, and data submission epics, which use the Data Submission Epic template (DO-EPIC). The canonical example is **CTDC-1802 (CTDC Export to the Cancer Genomics Cloud)**, rewritten to this shape and Closed on 2026-09-14.
>
> **v2 (2026-09-14): one five-section template replaces the per-grouping templates.** The v1 templates (7b-1 Application Pages at 15 sections, 7b-2 Microservices at 20, 7b-3 Features at 18, and the 7b-4 through 7b-7 stubs) produced 3,000-word epic bodies that nobody read in Jira. Per Gina's direction the standard is now what the Agile Alliance actually prescribes: an epic is "a large user story that cannot be delivered as defined within a single iteration," and "there is no standard form to represent epics." CTDC's form is a goal-and-objectives statement, not the As a / I want / So that story form, which is reserved for the child stories. Everything the v1 templates carried beyond that (Upstream Provenance, Surface Area, Stakeholders, Key Definitions, Composition, Dependencies, Assumptions, Constraints, Risks, User Impact, Components Breakdown, Documentation & Compliance) now lives in one of three places: a child story, a Jira comment, or the leadership `.docx` epic summary (Section 5). The seven groupings survive only as a classification aid for deciding what to verify before drafting (see 7b-shared, "Verification & ground truth"); they no longer select a template.

**Why this template**

An epic is a container over its child stories, not a design document. The body needs to do four things: state the goal, objectives, and why, describe the mechanism in one breath, draw the scope line, and state how we know it is done. Anything longer either duplicates the children or goes stale. The target is **under 600 words**; if content does not fit, it belongs in a child story, a comment, or the epic summary `.docx`.

**Card line + section order (card line, then 5 sections, exactly this sequence)**

Each section header is an `h3.` Jira wiki heading using the emoji + bold title format shown (`h3. 🎯 *Epic Statement*`; see "Authoring format" in 7b-shared). Do not omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header.

0. **Card line** (added 2026-09-24): the first line of the description, with no header. One plain sentence, 140 characters or fewer, saying what the epic delivers in terms Federal Leadership would use. No markup, emoji, ticket keys, or heading; follow it with one blank line, then the 🎯 heading. It is what the Federal Leadership epic Kanban board shows on each card (Board settings, Card layout: Description shows the first line). Example (CTDC-1926): *"Lets users download all files of one type for a study as a single zip, built on request instead of stored in the cloud."* When the admins add the existing Business Value text field (`customfield_10502`) to the CTDC and ICDC epic screens, the same sentence moves into that field and the card layout switches to it.
1. `h3. 🎯 *Epic Statement*`: One paragraph stating the **goal**, the **objectives**, and **why** the work is being done. Not a user story: the As a / I want / So that form belongs to the child stories, where one user and one behavior are in view. The epic statement is the umbrella over those stories: the outcome the team is delivering, the two to four objectives that define it, and the problem or value that justifies it. Example (CTDC-1802): *"Give CTDC researchers a one-click path from the files they have gathered in the Cart to a project on the Seven Bridges Cancer Genomics Cloud, so that CTDC data can be analyzed in the cloud without being downloaded or manually re-uploaded. Objectives: an export that needs no manual manifest handling; a manifest CGC can import as-is with CTDC clinical metadata attached; file access still governed by the researcher's own dbGaP authorization; identical behavior on every tier. Why: finding files is only half of FAIR. Without a direct hand-off to a CRDC Cloud Resource, researchers had to download a manifest, log in to CGC separately, and upload it by hand."*
2. `h3. 🧬 *Description*`: Two short paragraphs. (a) How it works: the surface(s) it lives on with the route, the services and data stores involved (Memgraph, never Neo4j; OpenSearch when relevant), and where authorization is enforced. (b) What this epic's phase delivers (design, frontend, backend, DevOps, defect fixes) and the release it shipped in or targets. Ground (a) in the canonical repo and the live UI, not in ticket titles.
3. `h3. 🗺️ *Scope*`: Two sub-blocks, **In Scope** and **Out of Scope**, about five bullets each. Out of Scope names adjacent or excluded work in prose (never by ticket key) and ends with the bullet that future enhancements will be scoped as a new phase epic.
4. `h3. ✅ *Acceptance Criteria*`: A numbered list of five to eight items, each a single observable outcome the epic delivers. The epic itself is not tested: verification happens in its child stories and tasks, so these criteria state what must be true when the children are done (Gina, 2026-09-25). The quality bar (WCAG 2.1 AA, design system conformance, automated test coverage for user-facing work; contract stability, test coverage, observability, and cross-environment parity for backend work) is one item, not a sub-block.
5. `h3. 📝 *Notes*`: Two or three bullets at most: the phase statement (complete and the release it shipped in, or what closes it), and the canonical repos with branch. An upstream Bento package goes here when one is involved. Nothing else.

**Standing emoji set (5 entries)**

| Section | Emoji |
|---|---|
| Epic Statement | 🎯 |
| Description | 🧬 |
| Scope | 🗺️ |
| Acceptance Criteria | ✅ |
| Notes | 📝 |

**Required content rules (universal rules in 7b-shared also apply)**

- **Card line first.** Line 1 is the card line (item 0), exactly one sentence of 140 characters or fewer, then a blank line.
- **Under 600 words** for the five sections (the card line is not counted). Count before pushing. CTDC-1802 is 597.
- **No ticket keys in the body** (7b-shared, "Cross-ticket references"). Related work is named in prose; Jira links are for parent/child only.
- **Grounded before drafting.** Verify the live UI with Playwright for anything user-facing, and read the canonical repo (confirmed via `CBIIT/crdc-ctdc-starter-kit/.gitmodules`, Section 17) for the mechanism in the Description. The child tickets' summaries are not a substitute.
- **Memgraph, never Neo4j; OpenSearch named when relevant; FAIR stated where it falls naturally** (usually in the Epic Statement's "why").
- **Underscored identifiers in `{{...}}` monospace** (`{{drs_uri}}`, `{{REACT_APP_INTEROP_SERVICE_URL}}`) so Jira does not open italic spans.
- **Curly braces escaped as `\{...\}`** anywhere they appear.
- **Not evergreen** (feature, page, service, and infrastructure epics; the standing data epics in 7b-D are the exception). The Notes phase statement says what this epic delivered; the epic is Closed with resolution `Completed` when its children are done (7b-shared, "Epic posture defaults").

**Writing-and-publishing workflow**

1. **Verify** the hosting surface (Playwright) and the canonical repo before drafting.
2. **Draft** the card line and all 5 sections in Jira wiki markup, applying the rules above. Check the word count.
3. **Push** via `jira_update_issue` with the description in `additional_fields` (`{"description": "..."}`, raw Jira wiki; see "Authoring format" in 7b-shared) (for a new epic, `jira_create_issue` with a one-line placeholder first, then update). Set `priority` to Major in the same call; never touch `labels` unless deliberately changing them.
4. **Verify the render with a UI screenshot** from the user; wiki source is not a render preview.
5. **Close the epic** (transition `Close`, resolution `Completed`) with a short comment naming the release once every child ticket is Closed.

**Migration of v1 epics**

*Data-related epics use the data variant in 7b-D* (Gina, 2026-09-25). Submission Data Modeling and Internal Data Modeling are standing epics that stay Open and carry a Risks table; they are migrated with 7b-D, not this template. The standing Data Integration epic (CTDC-1664) is being retired: each study submission becomes its own closing epic on the Data Submission Epic template (DO-EPIC, 2026-09-25), piloted on CTDC-2110 (CMB v6). Mock Data Generation was reclassified the same day as a feature epic (its outputs serve data submitters, but it closes when delivered) and is migrated with this template.

Superseded 2026-09-24: Gina directed a sweep of every active CTDC epic to this template (the earlier "condense when next edited, no sweep" rule is retired). Order: In Progress epics first, then Ready for Review, then On Hold, with drafts reviewed by the TPM before each batch is written. Drafts condense the existing description only (no new facts); stale items are flagged to the TPM rather than guessed. Cut detail is not copied into comments: Jira's History tab keeps every prior description, and a JSON backup of the pre-sweep descriptions was taken on 2026-09-24. Final Design QA epics keep their own container template (cloned from the Final Design QA template epic) and only gain the card line.

#### 7b-D. Data Epic Variant (Lean v2-D)

> **Use this variant for the two standing data modeling epics only**: Submission Data Modeling and Internal Data Modeling (Gina, 2026-09-25). Data submissions no longer have a standing epic: each study submission is its own closing epic on the Data Submission Epic template (DO-EPIC, 2026-09-25). Everything in 7b and 7b-shared applies except where this subsection says otherwise. Mock Data Generation is a feature epic and uses 7b; Bento Data Retriever is a microservice epic and uses 7b.

**Why a variant**

A feature epic delivers something and closes. A data epic is a standing pipeline: submissions, model releases, and data loads keep arriving, and each one is a child ticket that opens and closes on its own. The body therefore describes how work flows through the epic, not what one phase delivers. The two modeling epics also feed a chain that no feature epic has:

```
Submission Data Modeling ─┐
                          ├─> model release ─┬─> Data submission epics (validate, index, load, test)
Internal Data Modeling ───┘                  └─> Mock Data Generation and other consumers
```

**Card line + section order (card line, then 7 sections, exactly this sequence)**

0. **Card line**: same rule as 7b item 0.
1. `h3. 🎯 *Epic Statement*`: same as 7b (goal, objectives, why; not a user story).
2. `h3. 🧬 *Description*`: Two short paragraphs. (a) How the work runs: the repos and branches, the CRDC Submission Portal where relevant, and the data stores (Memgraph, OpenSearch). (b) How child work is tracked: the child naming pattern (for example `{{Submission Data Modeling: <submission>}}`) and the event that closes each child. Part (b) replaces 7b's "what this phase delivers." No list of active studies or releases in the body: the children carry that, and a list in the body goes stale.
3. `h3. 🗺️ *Scope*`: **In Scope** and **Out of Scope**, about five bullets each. Out of Scope names the sister data epics in prose. Omit 7b's closing "new phase epic" bullet.
4. `h3. 🔗 *Upstream & Downstream*`: Two labeled sub-lists, *Upstream* (what must be true before this work starts) and *Downstream* (who consumes what it produces), two to four bullets each, named in prose.
5. `h3. ✅ *Acceptance Criteria*`: Five to eight **standing** criteria, each phrased "Every <child, release, or load> ..." so it holds for every cycle rather than once at close. The epic itself is not tested. A data promotion constraint (for example, no direct loads to Prod) is one item here.
6. `h3. ⚠️ *Risks & Mitigations*`: Required. A Jira wiki table `||Risk||Impact||Mitigation||` with three to six rows. Only standing risks that apply to the epic as a whole; a risk tied to one study or release goes on that child ticket. PHI handling is a row when the epic touches patient-level data.
7. `h3. 📝 *Notes*`: Two or three bullets: the Standing line ("Standing: stays Open for the life of the CTDC project; each child closes when <event>.") and the canonical repos with branch. Never the word "evergreen."

**Standing emoji set (7 entries)**

| Section | Emoji |
|---|---|
| Epic Statement | 🎯 |
| Description | 🧬 |
| Scope | 🗺️ |
| Upstream & Downstream | 🔗 |
| Acceptance Criteria | ✅ |
| Risks & Mitigations | ⚠️ |
| Notes | 📝 |

**Content rules that differ from 7b**

- **Under 800 words** for the seven sections, including the Risks table text (the card line is not counted).
- **Standing, not closed.** These epics stay Open for the life of the CTDC project. The wording is "Standing"; the sweep check for "evergreen" still applies.
- **Not restored from the v1 data templates**: Stakeholders, Key Definitions, Assumptions, Constraints, User Impact, Components Breakdown, Documentation & Compliance. A constraint worth keeping becomes one acceptance criterion or one risk row.
- **Migration moves, never invents.** Drafts condense existing text only. A risk that belongs to a sister data epic moves to that epic (for example, "the model lags the incoming dataset" belongs to the modeling epics, not to a data submission epic).

---

#### 7b-shared. Universal Epic Conventions

These rules apply to every CTDC epic. The lean template in 7b-lean inherits them without restating them.

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
- **Status and lifecycle:** Open while any child work is in flight; **Closed with resolution `Completed`** once every child ticket is delivered. Epics are NOT evergreen containers. When a delivered feature, page, or service needs further work later, open a **new phase epic** for that work rather than reopening or extending the finished one. The closing transition on this tracker is `Close` (transition id 231) and it requires the resolution to be set in the same call; `Completed` is the resolution the CTDC project uses for finished epics (`Done` is rejected on this transition). Verified on CTDC-1802, 2026-09-14.
- **Assignee:** Unassigned, unless directed otherwise. Individual child tickets carry the actual ownership.
- **Labels:** Preserve any existing label (e.g., `Task-1.3.8.X`). Do not add or remove labels in epic-normalization passes unless deliberately changing them.

**Cross-ticket references (applies to epics, user stories, and tasks)**
- **Never write another Jira ticket key in the body of an epic, user story, or task.** Related, adjacent, or excluded work is named in prose by what it is (e.g., "the Cart page", "the RAS-enabled object file download initiative", "the per-submission IndexD registration work"), never by key.
- **Jira issue links are used only for parent/child relationships** (Epic Link and sub-task parentage). Do not add "Relates" links between epics to stand in for prose references.
- Ticket keys are still fine in Jira **comments**, in this SKILL.md, in the templates repo, and in sprint or stakeholder artifacts; the rule is about the description body of the ticket itself.
- This supersedes the earlier "cross-epic linkage" rule that asked Out of Scope, Dependencies, and Notes to cite sibling epic keys. Epics written before 2026-09-14 still carry inline keys; strip them the next time each one is edited rather than in a sweep.

**Quality bar**
- User-facing epics fold WCAG 2.1 AA, design system conformance, and automated test coverage into a single Acceptance Criteria item. Backend or infrastructure epics use the equivalent service bar (contract stability, test coverage, observability, secrets handling, cross-environment parity) in one item. The bar is stated once, not expanded into its own section.

**Verification & ground truth**
- **For Application Pages and Features:** verify the live UI with Playwright (`browser_navigate` + `browser_snapshot`) before drafting. Ground In Scope claims in what the page actually renders.
- **For Microservices, Infrastructure, Security, Data:** verify against the source repo, deployed config, or live system before drafting. Do not infer scope from imagination.
- **Verify the canonical repo before reading code.** When an epic touches CTDC code, **the first verification step is confirming you are in the project-canonical repo**, not just any repo whose name matches. CTDC has historical/abandoned repos that share name fragments with the active canonical repo (e.g., `bento-ctdc-frontend` is the abandoned 2021–2023 frontend; `crdc-ctdc-ui` is the active starter-kit-canonical frontend). The single source of truth for which repos are canonical is the `.gitmodules` file in `CBIIT/crdc-ctdc-starter-kit`. See Section 17 for the full list.
- **For all groupings:** the rendered Jira UI is the only ground truth for description rendering. Wiki source returned by `jira_get_issue` does not reveal rendering state and looks identical for working and broken tickets — only a UI screenshot tells the truth.

**Authoring format (Jira wiki, since 2026-09-24): overrides the Markdown guidance below**

The Jira connector (`mcp-atlassian`, Docker, configured in the Claude desktop app) now runs with `DISABLE_JIRA_MARKUP_TRANSLATION=true`. Nothing converts Markdown any more: whatever is sent is stored exactly. Author every description and comment in **Jira wiki markup**, and pass descriptions in `additional_fields` as `{"description": "..."}`.

- h3 heading: `h3. 🎯 *Title*`
- Bold: `*bold*`; italic: `_italic_`
- Bullet: `* item` at column 0; numbered: `# item` at column 0
- Monospace: `{{code_name}}`
- Table: header row `||h||h||`, data rows `|c|c|`
- Literal braces: `\{` and `\}`

Why the change: prepending a card line to existing epics meant sending back text already in Jira wiki, and the connector's Markdown converter re-translated it (bold headings became italics, `#` lists became `h1.` headings, underscores gained backslashes). Turning translation off makes every write exact. The Markdown-era guidance below (and in 7a, the templates folder, and Section 9) is historical: read it through the list above. If the setting is ever removed, the Markdown rules apply again.

**Markdown conventions (historical: applied while translation was on)**
- **The `description` field expects Markdown input, not Jira wiki markup.** The `jira_update_issue` MCP tool docs are explicit: "for 'description', provide text in Markdown format." Markdown is converted server-side into Jira-wiki for storage. **If you write Jira-wiki by mistake (`*bold*`, `h3.`, `#` for ordered lists), the tool interprets the asterisks as Markdown italic emphasis and converts them to underscores — so your bold headers render as italic.** Always author in Markdown:

  | Want | Write (Markdown) | Don't write (Jira-wiki) |
  |---|---|---|
  | h3 heading | `### **Title**` | `h3. *Title*` |
  | Bold | `**bold**` | `*bold*` |
  | Italic | `*italic*` | `_italic_` |
  | Bullet | `- item` | `* item` |
  | Numbered | `1. item` (auto-increments) | `# item` |
  | Table | Jira-wiki `||h||` and `|c|` (passes through unchanged — see "Risk format" below) | (same — tables are the exception) |

  Verified on CTDC-1960, 2026-04-30. The first push used Jira-wiki `*bold*` and section headers rendered as italic. The second push used Markdown `**bold**` and rendered correctly.
- Section headers: `### {emoji} **{Title}**` (h3, emoji + bold).
- Bullets are flush-left; no indented `-` bullets with bold labels.
- Use `**bold**` for emphasis (matched delimiters).
- **Glossary entries are flush-left bullets with matched-asterisk bold: `- **Term:**` (matched `**...**`), not `  - **Term:*` (indented, mismatched `**...:*`).** The MCP's Markdown→Jira-wiki converter reads the six-space indent as a level-two bullet and the mismatched closing single asterisk as italic-underline, producing broken garbage in the rendered Key Definitions section. The flush-left, matched-asterisk pattern is the canonical authoring form for any glossary-style bullet in a Jira body (the lean epic template has no glossary section; this applies to stories and comments that use one). Verified on CTDC-1922, 2026-05-11. Some older epics (e.g., CTDC-2040) use the broken pattern — treat that as a known inconsistency, not a template you should copy.
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

**Comment body wiki-rendering quirks.** Comment bodies (added via `jira_add_comment` or `jira_transition_issue` with a `comment` parameter) also pass through the Jira-wiki renderer and have their own quirks separate from descriptions:

- **Plus signs (`+`) get silently mangled.** The wiki renderer interprets `+` as inserted-text markup. A comment that says "Memgraph + OpenSearch" will render as "Memgraph  OpenSearch" with the `+` stripped. **Fix:** use plain English connectives like "and" or "plus" in comment bodies, or escape with a backslash if the literal `+` is essential. Verified on CTDC-1658 closure comment, 2026-05-06.
- **Curly braces have the same macro-syntax issue as descriptions.** Apply the same `\{...\}` escaping rule.

**Risk format (when a risk table is needed at all)**

The lean epic template (7b-lean) has no Risks section: risks belong in the `.docx` epic summary (Section 5) or in a comment. When a table is needed anywhere in a Jira body, it renders as a three-column **table**, not a bullet list.

**Format (Jira wiki table syntax — passes through Markdown unchanged):**

```
### ⚠️ **Risks & Mitigations**
||Risk||Impact||Mitigation||
|Schema changes break generation scripts|Mock files become outdated|Implement automated generation triggered by schema version updates|
|Mock data does not reflect real-world complexity|Submitters misinterpret requirements|Include edge-case examples and relational integrity scenarios|
```

**Why Jira-wiki tables in a Markdown document?** The MCP's Markdown→Jira-wiki conversion does not handle GitHub-flavored Markdown tables (`| h | h |` / `|---|---|`) reliably for this MCP server's converter. Jira-wiki table syntax (`||h||` for headers and `|c|` for cells) passes through the converter unchanged and lands as a native Jira table. This is the one intentional exception to the "always author in Markdown" rule above. Verified on CTDC-1960, 2026-04-30 — the risk table was the only section that rendered correctly across all three pushes regardless of the surrounding Markdown/Jira-wiki confusion.

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

### 📚 Data Management Templates (Component Library)

Data management ticket templates live in the component library at **`claude/templates/`**, not inline in this SKILL.md. The component library is the canonical location; this section is the entry-point pointer.

> **Data Operations.** The data operations templates carry stable **DO codes** — permanent identities that never change when the flow is reordered (see the Template ID crosswalk and ID governance note below). Operational order is carried by the **Step** column. These IDs are referenced throughout this section and §9a; each template lives as its own file in `claude/templates/`:
>
> **Study Submission Flow**
>
> | Step | ID | Template | File |
> |---|---|---|---|
> | 1 | **DO-EPIC** | Data Submission Epic | `data-submission-epic-template.md` |
> | 2 | **DO-MODEL** | Data Modeling for Study Submission | `data-modeling-study-submission-template.md` |
> | 3 | **DO-DBGAP** | dbGaP Validation Task | `dbgap-validation-task-template.md` |
> | 4 | **DO-INDEX** | IndexD Registration Task | `indexd-registration-task-template.md` |
> | 5 | **DO-LOAD** | Data Loading Task | `data-loading-task-template.md` |
> | 6 | **DO-ZIP** | Megazip Creation Task | `megazip-creation-task-template.md` |
> | retired | ~~DO-STORY~~ | Data Submission User Story (retired 2026-09-25, replaced by DO-EPIC) | `data-submission-user-story-template.md` |
>
> **Internal Modeling**
>
> | ID | Template | File |
> |---|---|---|
> | **DO-INTMODEL** | Data Model Update Task | `data-model-update-template.md` |
>
> **Template ID crosswalk (legacy 7x -> DO code).** Renumbered from the legacy Section 7 letter scheme to stable Data Operations codes on 2026-07-23. Legacy references resolve as: 7e = DO-STORY, 7f = DO-MODEL, 7g = DO-INDEX, 7h = DO-LOAD, 7i = DO-ZIP, 7j = DO-INTMODEL (internal), 7k = DO-DBGAP. DO-EPIC (Data Submission Epic) was added on 2026-09-25 with no legacy letter; DO-STORY was retired the same day and stays a deprecated code, never reused.
>
> **ID governance.** DO codes are permanent identities: assigned once, never reused, never renumbered when the flow is reordered. Operational order is carried by the Step column and is the only thing edited when the sequence changes. A new template receives a new DO code and is placed by its Step, not inserted into a letter sequence. A retired template keeps its code, marked deprecated, and the code is never recycled. This crosswalk is maintained permanently so historical 7x references stay resolvable.

**Templates available:**

| Template | File | Use When |
|---|---|---|
| **Data Submission Epic** | [`data-submission-epic-template.md`](./templates/data-submission-epic-template.md) | One **epic per study submission** (DO-EPIC, v1 2026-09-25): the Data Concierge's record from request to Production. Carries study identity (Submission Details), lifecycle, acceptance criteria, risks, and chronology; the dbGaP validation, IndexD, loading, and megazip tasks are its children through the Epic Link. Closes when the study is verified in Production. |
| **Data Submission User Story** *(retired 2026-09-25)* | [`data-submission-user-story-template.md`](./templates/data-submission-user-story-template.md) | Retired: replaced by the Data Submission Epic (DO-EPIC). Kept for history; do not create new ones. |
| **Data Loading Task** | [`data-loading-task-template.md`](./templates/data-loading-task-template.md) | Loading a CRDC submission's contents into CTDC's databases. Schema is stable; data is changing. |
| **IndexD Registration Task** | [`indexd-registration-task-template.md`](./templates/indexd-registration-task-template.md) | Minting GUIDs for a submission's files via the external CTDS/DCFS handoff. Runs in parallel with the paired Data Loading Task (linked with `Relates`, not blocking) — file downloads for the study resolve once the GUIDs are minted and spot-checked. |
| **dbGaP Validation Task** | [`dbgap-validation-task-template.md`](./templates/dbgap-validation-task-template.md) | Runs the established `dbgap_validatation_prod` Prefect deployment (`check_consent_group` on) to reconcile a released submission's consent group / ACL values against dbGaP. A consent-group gate that runs after release and **before** IndexD registration (DO-INDEX) and Data Loading (DO-LOAD). Since v2 (2026-09-16) it links to both with `Relates`, never `Blocks`: the gate is a process rule, not a Jira link. |
| **Data Modeling for Study Submission** | [`data-modeling-study-submission-template.md`](./templates/data-modeling-study-submission-template.md) | An incoming **study submission** drives schema additions (new properties, enums, permissible values). Study-driven; records in that study's CDE Request Workbook (owned by the study's Data Concierge). |
| **Data Model Update Task** | [`data-model-update-template.md`](./templates/data-model-update-template.md) | Model changes initiated by the **CTDC project itself** (application roadmap or data team) — the full range from a single additive optional property to a breaking multi-repo refactor. Internally/CTDC-driven; records in the internal CTDC CDE Request Workbook (owned by the project, no individual owner). |
| **Megazip Creation Task** | [`megazip-creation-task-template.md`](./templates/megazip-creation-task-template.md) | A study's object files are bundled into one downloadable `.zip` **per `data_file_type`** (v3, 2026-09-17: one ticket per study, filename `<study>_<data_file_type>.zip`, no program prefix), each GUID **self-minted** (`dg.4DFC/`) and its `indexd.tsv` team-authored, then indexed through the **standard DCF Google Drive + CRINTAKE handoff like any file**, and the metadata loaded across all tiers — created after the study passes its Dev spot-check testing and the submission is marked **Complete** (the trigger that moves the object files into the S3 bucket) — build it before the QA load so downloads resolve, though it can technically be done at a later tier. CTDC-*created* artifact; the only megazip-specific difference from a normal DO-INDEX registration is the self-minted GUID (vs. one the Submission Portal assigns). |

**Submission Portal states → load & megazip sequencing.** The CRDC Submission Portal moves **Released → Complete**. At **Released**, the release package (metadata) is generated — per-file indexing (DO-INDEX) runs downstream, and the study's metadata is loaded **to Dev only** (object files not yet in the bucket) so the team can spot-check the study in the application and iterate with the submitter. Only after Dev testing passes is the submission marked **Complete**, the trigger that moves the physical object files into the AWS S3 bucket. The **megazip (DO-ZIP)** is then created (it needs the files in the bucket) — preferably **before the QA load** so downloads resolve, though it can be built at a later tier. **QA, Stage, and Prod loads all occur after Complete.** The megazip links to its paired Index/Load tasks with `Relates`, never `Blocks`.

**Sprint rule (2026-09-17).** Every ticket created from a DO-* template goes into the standing `CTDC Data Related` sprint (board 641, sprint id 8612) at creation, via `jira_add_issues_to_sprint`. Never leave one in the backlog and never place one in a numbered development sprint. Links within a study's task family are always `Relates`.

**Decision tree** (matches `claude/README.md`):

- *Is a new study submission (or a new version of a study) starting?* → **Data Submission Epic** (DO-EPIC); every task below for that submission hangs off it
- *Is new data being loaded into the existing schema?* → **Data Loading Task** (DO-LOAD)
- *Are the files in the submission still pending IndexD GUID minting via the external CTDS/DCFS team?* (paired with a planned Data Loading Task — the two run in parallel) → **IndexD Registration Task** (DO-INDEX)
- *Is the schema changing because an incoming **study submission** needs new properties/enums/permissible values, with that study's CDE Request Workbook as the spec?* → **Data Modeling for Study Submission** (DO-MODEL) — study-driven; records in the study's workbook
- *Is the schema changing because the **CTDC project itself** decided to change it (application roadmap or data team), anywhere from a single additive optional property to a breaking multi-repo refactor?* → **Data Model Update Task** (DO-INTMODEL) — internally/CTDC-driven; records in the internal CTDC workbook
- *(Either way, the change always records in a CDE Request Workbook — the driver just decides which one. There is no "no workbook" path. As of DO-MODEL v11 the two templates diverge by exactly one section: DO-MODEL carries a study-submission-specific **⭐ Data Concierge** section — 6 sections — while DO-INTMODEL has no study Data Concierge and deliberately stays 5-section. Otherwise identical; pick by driver, then follow the matching template's context.)*

**Universal pattern for data submissions** (revised 2026-09-25 with DO-EPIC on CTDC-2110; first verified 2026-05-20 on CTDC-1666 ↔ CTDC-2051 and CTDC-1804 ↔ CTDC-1799):

Every CTDC data submission generates multiple tickets: the **submission epic** for the submission as a whole, a dbGaP validation task, an IndexD task, a loading task, a megazip task, usually a modeling task for schema additions, and possibly supporting tasks (documentation review, mapping work, anonymization options). The **submission epic carries study identity** (program, study name, dbGaP ID, submitter, chronology, document references, study context). The validation, IndexD, loading, and megazip tasks are its children through the Epic Link and relate to each other with `Relates`; the modeling task stays a child of the Submission Data Modeling epic and links to the submission epic with `Relates`. Every task reuses the epic's title token (`<Program Short Name> <Study Short Name> <version>`) and **does not duplicate study identity** in its description. No `Supports` links.

See `claude/README.md` for the full library overview, `claude/workflows/data-submission-workflow.md` for the internal end-to-end Data Submission Process SOP (the process order and which artifact/template to create at each step), and `claude/lessons-learned/2026-05-20-data-modeling-templates.md` for the methodology lessons that produced this convention.

---

## 8. 👥 Key Contacts & Roles

### Team Roster

| Name | Role | Username | Account Key | Slack ID |
|---|---|---|---|---|
| Gina Kuffel | Senior TPM | `kuffelgr` | `JIRAUSER57501` | `U025MCK7MD3` |
| Stephanie Singleton | Co-TPM | `singletonss` | `JIRAUSER62602` | `U073X1J8FM2` |
| Yizhen Chen | Tech Lead / Full Stack | `cheny39` | `cheny39` | `UJ59LJYU9` |
| Nahom Tesfatsion | Frontend Engineer | `tesfatsionnh` | `JIRAUSER56020` | `U01TUNBB02K` |
| Adam Davenport | Backend Engineer | `davenportaw` | *(not confirmed)* | `U06GM3BFWLU` |
| Eric Miller | Backend Engineer | `millerer` | `JIRAUSER60101` | `U03SEJFGU7L` |
| Nathan Huang | Frontend Engineer  | `huangn4` | `JIRAUSER65560` | `U07LB6W2RMH` |
| Valentina Epishina | QA Engineer | `epishinavv` | `JIRAUSER58300` | `U02PMPFHBRN` |
| Patrick Breads | Data Engineer | `breadsp2` | `JIRAUSER55423` | *(not confirmed)* |
| Charles Ngu | DevOps Engineer | `nguca` | `JIRAUSER61006` | `U04L21QJGNM` |
| Michael Fleming | DevOps Engineer | `flemingme` | `flemingme` | *(not confirmed)* |

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
- When the epic template (7b, or the data variant 7b-D) evolves: new sections, emoji changes, content rules, or new gotchas: update the template here. Existing epics follow the sweep rules in 7b, "Migration of v1 epics" (swept 2026-09-24/25)
- When a new active CTDC repo is added to (or an old one removed from) the starter-kit `.gitmodules` file, update Section 17 to keep the canonical-repo list in sync

### 9a. Template Status Tracker

Tracks the status of every CTDC ticket template — software-development lane and data-management lane. Each future session that drafts a new template or iterates an existing one fills in that row.

**Canonical Example Index** — the single reference ticket (or pair) each template was built from, at a glance. The `canonical-example` Jira label marks these tickets in the tracker itself; this table and the label are kept in agreement.

| Template | Canonical Jira ticket(s) |
|---|---|
| 7a · User Story | CTDC-1691 |
| 7b · Epic (lean v2) | CTDC-1802 |
| 7b-D · Data epic variant | Submission Data Modeling, Internal Data Modeling (first drafts in progress, 2026-09-25) |
| 7c · Bug Format | n/a |
| 7d · Design Task | CTDC-2044 |
| DO-EPIC · Data Submission Epic | CTDC-2110 (CMB v6) |
| DO-STORY · Data Submission User Story *(retired 2026-09-25)* | CTDC-1666 *(historical)* |
| DO-MODEL · Data Modeling for Study Submission | CTDC-2051 *(current link pattern: CTDC-2111 Relates submission epic CTDC-2110)* |
| DO-DBGAP · dbGaP Validation Task | CTDC-2141 *(first under a submission epic: CTDC-2233)* |
| DO-INDEX · IndexD Registration Task | CTDC-2060 |
| DO-LOAD · Data Loading Task | CTDC-2063 *(first v9 with Expected Counts: CTDC-2205)* |
| DO-ZIP · Megazip Creation Task | CTDC-2220, CTDC-2221 *(v2 ancestor: CTDC-2104)* |
| DO-INTMODEL · Data Model Update Task | CTDC-2068 |

**Software development lane**

| Template | Section / File | Status | Canonical Example |
|---|---|---|---|
| User Story | Section 7a | ✅ Drafted v2 (2026-09-04) — slimmed from 7 to 5 sections; removed Parent Epic & Context and Notes (now carried by the Epic Link field, native Jira links, and comments); aligned with ICDC's `user-story-template.md` (canonical ICDC-4244) | CTDC-1691 (Upload Participant Set, child of CTDC-2042 — still carries the v1 7-section body; new stories use v2) |
| Epic (all groupings) | Section 7b | ✅ Drafted v2 (2026-09-14): one five-section lean template (Epic Statement · Description · Scope · Acceptance Criteria · Notes), under 600 words, no ticket keys, closes when delivered. Replaces v1 per-grouping templates (Application Pages 15 sections, canonical CTDC-2025; Microservices 20 sections, CTDC-1968; Features 18 sections, CTDC-2042; Products/Infrastructure/Security/Data stubs). Active epics were swept to v2 on 2026-09-24/25; the two standing data modeling epics use the 7b-D variant (card line plus 7 sections with Upstream & Downstream and a required Risks table, under 800 words), and Final Design QA epics keep their container template; data submissions use one DO-EPIC epic per submission (2026-09-25) | CTDC-1802 (Export to the Cancer Genomics Cloud) |
| Bug Format | Section 7c | ✅ Lightweight format | n/a |
| Design Task (7d) | `claude/templates/design-task-template.md` | ✅ Drafted v3 (2026-09-04): no Jira ticket keys in the body (Links holds external references only), colons instead of em dashes in labeled bullets. v2 (2026-07-07) slimmed from 10 to 7 sections; removed Linked Work, Collaboration & Reviews, Open Design Questions, and Notes (now captured via native Jira links, workflows/handoffs, and comments); added Links for reference materials | CTDC-2044 |

**Data Operations lane** — templates live in the component library at `claude/templates/`; see also the Data Management Templates (Component Library) section.

**Study Submission Flow**

| Step | ID | Template | File | Sub-function | Status | Canonical Example |
|---|---|---|---|---|---|---|
| 1 | **DO-EPIC** | Data Submission Epic | `claude/templates/data-submission-epic-template.md` | Submission: one epic per study submission | ✅ Drafted v1 (2026-09-25): card line plus 9 sections (Epic Summary · Context & Background · Goal / Objectives · Submission Details · Submission Lifecycle · Acceptance Criteria · Risks & Mitigations · Submission Chronology · Notes); built from CTDC-1664's structure plus every field across its user stories; Process Documentation link first in Submission Details; chronology holds milestones and decisions only, never data model versions; counts live on DO-LOAD Expected Counts; title `CTDC Data Submission: <Program Short Name> <Study Short Name> <version>`, reused as the token in every child title; children via Epic Link, modeling task via `Relates`, no `Supports` links; replaces DO-STORY and the standing Data Integration epic | CTDC-2110 (CMB v6, pilot) |
| 2 | **DO-MODEL** | Data Modeling for Study Submission | `claude/templates/data-modeling-study-submission-template.md` | Modeling — study-driven | ✅ Drafted v11 (2026-07-22) — **6-section shape** (Modeling Summary · CDE Request Workbook · **⭐ Data Concierge** · DM Federal Lead & SME Review · Steps to Completion · Verification Surfaces). v11 added the study-submission-specific **⭐ Data Concierge** section as new Section 3 — a **deliberate divergence from DO-INTMODEL**, which stays 5-section (an internally-driven update has no study Data Concierge); the section names the responsible party (Primary + Backups) via a Name/Role roster. Carries forward all v10 content: Section 4 row 3 is the **caDSR II Help Desk Request Form** (owner: Data Concierge), replacing the earlier "ServiceNow Ticket / DM Fed Lead"; conditional watcher rule (DM Fed Lead watches the caDSR ticket when one is filed, the CTDC Jira ticket only when none is required; both TPMs added to the caDSR ticket); Steps to Completion is the 11-step canonical workflow (TPM verification gate, multiple caDSR tickets per node, SI curation, Slack #data-modeling PR review, QA send-back); milestone tracker — no specifics, no counts | CTDC-2051 ↔ CTDC-1666 (canonical, drove v11 — already carries the ⭐ Data Concierge section); CTDC-1799 ↔ CTDC-1804 (pending retrofit to v11) |
| 3 | **DO-DBGAP** | dbGaP Validation Task | `claude/templates/dbgap-validation-task-template.md` | Loading data — consent-group gate | ✅ Drafted v2 (2026-09-16): 4-section shape (Validation Summary · Submission & Artifacts · Validation Workflow · Verification); runs the established `dbgap_validatation_prod` Prefect deployment (`check_consent_group` on) to reconcile a released submission's ACLs against dbGaP; v2 links the paired IndexD (DO-INDEX) and Data Loading (DO-LOAD) tasks with `Relates`, never `Blocks` (the consent-group gate is a process rule in Verification), title `PREFECT dbGaP Validation: <Study Name vN>`, and the `CTDC Data Related` sprint at creation; `Data-Concierge` label; parent is the study's submission epic (Epic Link) since 2026-09-25 | CTDC-2141 (first ticket of the pattern); CTDC-2142 and CTDC-2218 (v2 instances) |
| 4 | **DO-INDEX** | IndexD Registration Task | `claude/templates/indexd-registration-task-template.md` | Loading data — upstream artifact creation | ✅ Drafted v7 (2026-06-11) — external CTDS/DCFS handoff for GUID minting; runs in parallel with the paired Data Loading Task (linked with `Relates`, not blocking); carries the `Data-Concierge` label; v6 slimmed the task to 4 sections (one-sentence Registration Summary; Pre-registration + External handoff workflow only — the Confirmation-and-verification phase folded into the Verification section; no Notes section), building on the v5 Submission & Artifacts table that mirrors the Data Loading Task; v7 replaced em-dashes with colons (rendering-safe `* *Label*:` bullets), no structural change | CTDC-2060 (AHEP0731 Images-Only — canonical, v6 shape); the 11 paired NCTN-NCORP Index tickets (CTDC-2072–2092, even) aligned to v6; CTDC-1907 referenced for historical context only |
| 5 | **DO-LOAD** | Data Loading Task | `claude/templates/data-loading-task-template.md` | Loading data — end-to-end load | ✅ Drafted v9 (2026-09-25): 5-section shape (Load Summary · Submission & Artifacts · Expected Counts · Loading Workflow · Testing Signoff); v9 added a one-column Expected Counts table filled once from the Release Package and checked at Dev, QA, Stage, and Prod, and made the submission epic the parent (Epic Link); v8 (2026-06-11) replaced em-dashes with colons (rendering-safe `* *Label*:` bullets), no structural change; a dedicated Jenkins job per tier (Dev/QA/Stage/Prod), no lower/upper grouping; routes schema work to the modeling templates and IndexD work to DO-INDEX | AHEP0731 load (CTDC-2063); CTDC-2205 (CMB v6, first v9) |
| 6 | **DO-ZIP** | Megazip Creation Task | `claude/templates/megazip-creation-task-template.md` | CTDC-created artifact (megazip) | ✅ Drafted v3 (2026-09-17): lean rewrite after assignees reported v2 was too long to read. One ticket per study and one megazip **per `data_file_type`** (filename `<study>_<data_file_type>.zip`, no program prefix); release package copied from the study's DO-DBGAP / DO-INDEX tickets; object-files directory (in `nci-crdc-data-bucket-prod`, a different bucket) read from the `urls` column of `indexd.tsv` as the first workflow step; 4 sections (Summary · Artifacts · Workflow · Testing Signoff), with the GUID spot-check folded into the Index phase; GUIDs self-minted (`dg.4DFC/`) and handed to CTDS through the standard DCF Google Drive + CRINTAKE path; `Relates` to every study task, no feature user story link; `CTDC Data Related` sprint at creation | CTDC-2220 (AHOD0831) and CTDC-2221 (S0819), v3 canonical; CTDC-2104 (AHEP0731), v2 ancestor |
| retired | ~~DO-STORY~~ | Data Submission User Story | `claude/templates/data-submission-user-story-template.md` | Submission: parent user story | ⛔ Retired 2026-09-25, replaced by DO-EPIC. Last version v2 (2026-06-16, Data Concierge POV, 6 sections). Existing stories convert with Move → Epic | CTDC-1666 (historical) |

**Internal Modeling**

| ID | Template | File | Sub-function | Status | Canonical Example |
|---|---|---|---|---|---|
| **DO-INTMODEL** | Data Model Update Task | `claude/templates/data-model-update-template.md` | Modeling — internally / CTDC-driven | ✅ Drafted v14 (2026-07-20) — all internally-driven model changes, any SemVer level (additive through breaking); **5-section shape** (DO-MODEL diverged to 6 sections at v11 by adding a study-only ⭐ Data Concierge section — DO-INTMODEL deliberately stays 5-section, as an internally-driven update has no study Data Concierge); re-synced to finalized CTDC-2068 in lockstep with DO-MODEL: Section 3 row 3 is now the **caDSR II Help Desk Request Form** (owner: Data Concierge), replacing "ServiceNow Ticket / DM Fed Lead"; Steps to Completion expanded to the fuller canonical workflow with conditional watcher logic (DM Fed Lead added on the Jira issue when no CDEs/PVs change, on the caDSR ticket when they do); milestone tracker — no specifics, no counts; records in the internal CTDC CDE Request Workbook (owned by the project, no individual owner); DM Federal Lead & SME Review (caDSR II Help Desk Request Form + DHDM Jira Issue) gates prod | CTDC-2068 (Program curation properties — canonical pilot) |

### 9b. Lessons Learned from 2026-04-30 Normalization Pass

- **Wiki source is not ground truth for rendering.** Only a UI screenshot from the user reveals whether a Jira description renders correctly. The wiki source returned by `jira_get_issue` looks identical for working and broken tickets.
- **The verified description-render bug is unescaped curly braces.** Jira-wiki treats `{...}` as macro syntax (`{code}`, `{noformat}`, `{panel}`, etc.). When the renderer hits an unknown macro name like `{program_short_name}`, parser state corrupts for the surrounding block and the damage propagates *forward* through the document — adjacent headers lose their `h3.` recognition, bullets lose their list context, and some headers disappear entirely. The fix is to escape every curly brace in description text as `\{...\}`. CTDC-2040 (Program Details Page) was the test case that proved this; pre-fix push corrupted ~half the document, post-fix push rendered cleanly.
- **Earlier suspected causes were wrong.** Before the curly-brace finding, several theories were advanced and abandoned: mismatched bold delimiters, indented bullets, backtick code spans, asterisk-vs-underscore emphasis, and most prominently CRLF/LF line endings. Each theory was confidently asserted in SKILL.md until the next data point falsified it. CTDC-1645's stored description has every one of those "defects" and renders perfectly. The line-endings theory was particularly seductive because it explained the apparent nondeterminism — but the real explanation is that some epic descriptions happened to contain `{...}` patterns and others didn't, which looked random because nobody was inspecting the body text for macro syntax.
- **Cascade-from-a-point pattern indicates parser-state corruption, not a transport bug.** When render breakage propagates *forward* from a specific point in the document — paragraphs fracturing right where some specific token appears, then everything after that point breaking — the cause is parser-state corruption from that token, not anything to do with how the bytes were transported. Look for unescaped wiki macro syntax first.
- **Theory thrash is a smell.** When debugging without ground-truth screenshots, multiple "this is the bug" theories in a row, each falsified by the next data point, mean the methodology is wrong, not just the theory. Stop pushing changes and ask for a screenshot before continuing. The 2026-04-30 session went through three wrong theories (CRLF/LF, underscore-italic, then a confused mix of both) before the correct cause was identified by reading the actual cascade pattern in a screenshot dump.
- **Markdown vs Jira-wiki authoring confusion (verified 2026-04-30 on CTDC-1960).** The MCP `description` field expects Markdown; if you write Jira-wiki by mistake, asterisks get re-interpreted as Markdown italic emphasis and bold headers render as italic. The fix is in 7b-shared "Markdown conventions" — always author in Markdown (`### **Heading**`, `**bold**`, `1.` for numbered, `-` for bullets), and use Jira-wiki table syntax (`||h||` and `|c|`) only for tables, which passes through the converter unchanged. Confirmed working: CTDC-1960 second push (Markdown) rendered correctly with bold headers; first push (Jira-wiki) rendered headers as italic. The risk table from CTDC-1960 is now the canonical example for the "Risk format" subsection in 7b-shared.

### 9c. Lessons Learned from 2026-05-06 Local Find Epic Creation (CTDC-2042)

- **Verify the canonical repo before reading code, not just whether the repo exists.** When investigating a feature that touches code, the first verification step is confirming the repo's role in the canonical project structure — not just confirming a repo with a matching name exists. CTDC has a historical/abandoned frontend repo (`bento-ctdc-frontend`, last pushed 2023) and an active starter-kit-canonical frontend repo (`crdc-ctdc-ui`, actively developed). A "search for the repo, pick the most-recently-updated" heuristic happens to land on the right answer here, but only by coincidence. **The canonical entry point is `CBIIT/crdc-ctdc-starter-kit/.gitmodules`** — that file is the source of truth for which repos are part of the active CTDC system. See Section 17. The methodology improvement: when an epic touches code, read the starter-kit `.gitmodules` first, then read inside the canonical repo. This avoids a class of error where the right answer happens by luck rather than by methodology.
- **Resolution naming differs by Jira instance age.** The NCI tracker (`tracker.nci.nih.gov`) uses the older `Won't Fix` resolution name; modern Jira Cloud uses `Won't Do`. Specifying `Won't Do` on a transition that requires a resolution returns "The selected resolution cannot be chosen during this action." Always use `Won't Fix` on this instance. `Duplicate` is also valid and accepted on the `Open → Close` transition. Verified on CTDC-826/827/828/722/1658 closures, 2026-05-06.
- **Resolution requirements differ by source state.** The same workflow has different resolution-requirement rules depending on which status the issue is leaving. From `Open` (transition id 191 "Close"), a resolution is **required** and must be one of the allowed values. From `Testing Hold` (transition id 731 "Move to Closed"), a resolution is **not required** — passing `null` is accepted, and the resulting issue is closed with no resolution recorded. This means closing a `Testing Hold` ticket via the API leaves a "closed without resolution" hygiene gap unless you manually edit it in the Jira UI afterwards. Verified on CTDC-761 closure, 2026-05-06. **Recommendation:** when closing a `Testing Hold` ticket, follow up with a manual UI edit to set the resolution if reporting hygiene matters.
- **Epic creation requires a non-null description at create time.** `jira_create_issue` rejects a `null` or missing description with "Description is required." The clean workaround that works on this tracker: seed a placeholder one-line description at creation, then immediately overwrite with the real Markdown description via `jira_update_issue`. This two-step pattern also produces cleaner Markdown→Jira-wiki conversion than trying to deliver the full description in one call. Verified on CTDC-2042 creation, 2026-05-06.
- **Comment body wiki-rendering quirks (separate from description quirks).** Comment bodies pass through the same Jira-wiki renderer as descriptions and have their own gotchas. Verified failure mode: plus signs (`+`) get silently stripped — the renderer interprets them as inserted-text markup. A comment that says "Memgraph + OpenSearch" renders as "Memgraph  OpenSearch." Use plain English connectives like "and" or "plus" in comment bodies. Same `\{...\}` curly-brace escaping rule applies. Verified on CTDC-1658 closure comment, 2026-05-06.
- **Multiple resolution values are allowed on the same transition.** The `Open → Close` transition (id 191) on this tracker accepts both `Won't Fix` and `Duplicate`. There is no API endpoint that enumerates the per-transition allowed resolutions ahead of time; discovery is by trial. **Recommendation:** when closing tickets, pick the most semantically appropriate resolution first; if it fails, fall back to `Won't Fix` as the safe default. Verified on CTDC-1658 closure with `Duplicate` resolution, 2026-05-06.

### 9d. Lessons Learned from 2026-05-06 User Story Template Drafting (CTDC-1691)

- **The bare-bones As-a/I-want/So-that format is too thin for active engineering work.** The original 7a Story Format was a 12-line stub: As-a/I-want/So-that, a flat checkbox AC list, a Technical Notes line, and a Related links line. It worked as a template *shape* but it didn't tell engineers when a story was *done* — there was no defined quality bar, no test artifact requirement, and no link back to the parent epic's context. Stories were getting written with the substance from one engineer's head and nothing else, which made review uneven. The new 7-section template adds: a one-sentence Story Summary the engineer can read in five seconds; an explicit Parent Epic link in the body (in addition to the `customfield_12350` field); a Scope split that prevents adjacent-feature creep; a Functional+P&Q AC split mirroring the epic AC pattern; a dedicated Testing Requirements section; and a Notes section for upstream provenance and predecessor context.
- **Testing as a P&Q bullet hides the dev's deliverable surface area.** During the design pass, the question came up whether test coverage should live as one bullet inside Performance & Quality (option A — keeps the section count to 6) or as a dedicated section (option B — section count goes to 7). The decision was option B, with the reasoning: Valentina is QA-only and does not write code, so every line of automated test that covers a story has to be written by the engineer who picks up the story. Burying test coverage in a single P&Q bullet hides the actual scope of what's being asked — by the time the engineer reads "automated test coverage added for new functionality," they have no way to know whether that means three unit tests or thirty. Splitting into Unit / Integration / Manual QA gives the engineer a checklist they can mark complete before moving the ticket to Ready for QA.
- **The Functional+P&Q split mirrors the epic AC pattern intentionally.** The same shape (Functional ACs + Performance & Quality bar) appears in 7b-1 (Application Pages) and 7b-3 (Features) epic templates. Adopting it at the story level means a reader scanning a story and its parent epic side-by-side sees the same vocabulary and structure — visual continuity that "feels right." The emoji set was chosen the same way: 🎯 🔗 🗺️ ✅ 📝 are shared with epic templates; 👤 (User Story) and 🧪 (Testing Requirements) are unique to story scale.
- **CTDC-1691 was the right canonical example because it has real complexity at story scale.** The original ticket had 16 functional requirements — a button, a modal, file upload, a parser, a match-against-database step, two tabs, post-submit state changes, and composition with facets. That's enough surface area to exercise every section of the new template without being so big that the template feels like overkill. Smaller stories (e.g., a single tooltip wording change) can legitimately have shorter Testing Requirements sub-blocks; the template explicitly allows "None at this time" entries. But the ceiling case for a story should still fit cleanly inside the template — and CTDC-1691 did.
- **Numbering split is OK when an original AC has two independently testable clauses.** The original CTDC-1691 had 16 ACs; the rewrite has 19. The increase came from splitting two combined ACs (the original "if invalid → button doesn't change AND IDs stay in modal" became two separate testable ACs) and reordering for readability (visual presence first, behavior second, post-submit state third). No requirements were dropped. Splitting clauses joined by "and" is a recurring rewrite pattern when normalizing legacy ACs into the new template — applies broadly, not just to this ticket.
- **Three-question elicitation pattern works well for template design.** The session opened with three multiple-choice questions: (1) how heavy should the template be, (2) how to handle parent epic context, (3) how to structure ACs. Answer 3 came back as "I am unsure," which was the right answer to give — that question deserved more deliberation. Having explicit reasoning written for each option after the user picked option 1 and option 2 (and especially when they declined to pick option 3) let the design conversation focus on the one decision that actually mattered. **Pattern:** when designing a new template, elicit a small number of structural choices up front; if the user defers on one, treat it as a signal that *that* question deserves the most reasoning effort, not that the user doesn't have an opinion.

### 9e. Lessons Learned from 2026-05-11 (CTDC-1922 Programs Page Rewrite)

The CTDC-1922 (Programs Page) rewrite went through three render-failure debugging cycles before landing cleanly. All three root causes were diagnosable from SKILL.md before drafting, which is itself the most important lesson: **SKILL.md is the first read on any CTDC drafting task, not the recovery step after rendering fails.** Sections 9b and 9d were both consulted only after broken pushes — reading 7b-1 and 7b-shared at the start of the session would have prevented at least two of the three cycles.

- **Glossary entries: flush-left, matched-asterisk bold.** The pattern `  - **Term:*` (six-space indent + mismatched `**...:*` asterisks) was copied verbatim from the CTDC-2040 description and propagated into CTDC-1922 by reflex. The MCP's Markdown→Jira-wiki converter interprets the six-space prefix as a level-two bullet and the mismatched closing single asterisk as italic-underline, producing broken garbage in the Key Definitions section. The correct authoring form is flush-left `- **Term:**` with matched closing `**`. This rule is now codified in 7b-shared "Markdown conventions" and 7b-1 "Required content rules." Verified on CTDC-1922 third push, 2026-05-11. The lesson reinforces a broader principle: **canonical examples can carry forward bugs.** Cross-check against the template spec, not just the example. When the spec and the example disagree, the spec wins.
- **Risks are a Jira-wiki table, not a bullet list — even when the canonical example uses bullets.** 7b-shared "Risk format" specifies a three-column table (`||Risk||Impact||Mitigation||`). CTDC-2040 (listed as a canonical example in 7b-1) uses a bullet list with the same mismatched-asterisk pattern as its glossary — which both violates the format rule AND triggers the same broken-bold rendering. CTDC-1922's nine risks were rewritten into the proper table on the third push and rendered correctly. This rule is now reinforced explicitly in 7b-1 "Required content rules." Same broader principle as the glossary rule: **format spec wins over canonical example.**
- **Curly-brace cascade from `{N}` units of measure (reinforcement of 9b).** Two `"{N} STUDIES"` strings in the CTDC-1922 first draft — one in Scope, one in Functional AC #5 — corrupted parser state and cascaded forward through Stakeholders → Key Definitions → Success Metrics on the first push, masking the underlying glossary-format issue beneath the cascade damage. Fix is the same as 9b: escape every curly brace in description text as `\{...\}`. The new reinforcement: **any time `{...}` could plausibly appear in body text — including units, counters, placeholders, template variables, or single-letter macro names like `{N}` — escape with backslashes.** Even single-letter unknown macro names corrupt parser state. The 9b lesson was technically already sufficient; this session confirms the rule covers a wider surface than the original CTDC-2040 finding suggested.
- **Older Application Pages epics are NOT being retroactively normalized.** Per Senior TPM direction 2026-05-11, the broken glossary/risks patterns in older epics (CTDC-2040 and any others that share them) are left alone — going-forward template rules only. The 7b-1 "Required content rules" updates and this 9e record carry the new standard for any new or rewritten epic; existing epics stay as-is. This means the canonical-example list in 7b-1 contains epics that do NOT fully conform to the template — readers must cross-check examples against the spec, not the other way around. A future normalization sweep could revisit this decision; this session deliberately scopes it out.
- **Read SKILL.md first.** The three failure cycles on CTDC-1922 cost more time than the original drafting would have if 7b-1, 7b-shared, and 9b had been read at session start. SKILL.md is the first read on any CTDC drafting task — not the recovery step after a broken push. Treat the "always check SKILL.md before drafting CTDC communications or documents" memory as a hard precondition, not a soft preference.

---

### 9f. Lessons Learned from 2026-05-20 (Data Modeling Template Iteration)

Seven methodology lessons from the same-day iteration of the Data Modeling for Study Submission template (v1 → v2 → v3) and the retrofit of CTDC-1799 / CTDC-1804 to match the canonical CTDC-2051 / CTDC-1666 pair. Lessons cover template authorship discipline (when canonical examples are upstream of template files, when not to import sections from sibling templates), the "source of truth is a structural rule" principle, the parent-user-story pattern as a CTDC-wide convention, and clean-restore as a valid recovery move when a pattern drifts.

Full record: [`claude/lessons-learned/2026-05-20-data-modeling-templates.md`](./lessons-learned/2026-05-20-data-modeling-templates.md)

The most universal rule from the session, worth keeping in mind on every CTDC drafting task: **The canonical example is upstream of the template. When in doubt, pull the approved example fresh from its source and match it exactly.**

### 9g. Lessons Learned from 2026-09-14 (CTDC-1802 Export to CGC Epic Close-Out)

Two convention changes came out of writing the CTDC-1802 description against the 7b-3 Features template, both now recorded in 7b-shared:

- **Epics are not evergreen.** CTDC-1802 had five Closed children (four Tasks, one Bug; three tagged to CTDC 1.1.0.322) and an epic still sitting In Progress with a "TBD" description. The template's standing-epic language would have kept it Open forever. The rule is now: an epic scopes one phase, closes with resolution `Completed` when its child work ships, and later work gets a new phase epic. On this tracker the `Close` transition rejects resolution `Done`; use `Completed`.
- **No ticket keys in ticket bodies.** Related work is named in prose; Jira links are reserved for parent/child. The description was drafted with zero inline keys and read fine: "the Cart page", "the RAS-enabled object file download initiative", and "the per-submission IndexD registration work" carry the meaning without a key.

Method notes worth repeating:

- **Ground the description in code, not ticket titles.** The child ticket summaries said "Export to CGC button" and "Redirection to SBG-CGC"; the actual mechanism (frontend builds a DRS-URI manifest from `filesInList`, posts it to the Interoperation service, which writes to S3 and returns a CloudFront signed URL, then the browser opens CGC's DRS CSV import-redirect) only became clear from `crdc-ctdc-ui` and `crdc-ctdc-interoperation`. That mechanism is what explains why a DevOps secrets ticket and a "manifest imported but files did not" bug both belong to the same epic.
- **Wrap underscored identifiers in backticks in prose** (`drs_uri`, `SIGNED_URL_EXPIRY_SECONDS`, `REACT_APP_INTEROP_SERVICE_URL`). The Markdown-to-wiki converter turns them into `{{monospace}}`, which sidesteps the bare-underscore italic-span hazard.
- **Code review surfaces risks the tickets never mention.** The Interoperation endpoint's error path returns a "signed URL" built from an error string instead of a 4xx/5xx, so the frontend can open CGC with a broken URL. That was recorded in the first (long-form) draft's risk table and is preserved in the ticket history; a fix would be a bug under a new phase epic, not the closed one.
- **The long-form templates were retired the same day.** The first CTDC-1802 draft followed the 18-section Features template and came out at 3,424 words. Gina's reaction ("it is sooooo long") led to the v2 lean template in 7b: the same epic at under 600 words and five sections, which is what the Agile Alliance definition of an epic actually calls for. Lesson: template length should be set by who reads the ticket in Jira, not by how much is knowable about the feature. Depth belongs in the `.docx` epic summary.
- **An epic statement is a goal, not a user story.** The first lean draft opened with an As a / I want / So that paragraph; Gina corrected it the same day: that form belongs to child stories, and the epic should state the overall goal, its objectives, and why. The 7b template now says so explicitly.

---

## 10. 🗃️ Jira Custom Field Reference

> **Critical.** The NCI tracker (`tracker.nci.nih.gov`) has non-standard field configurations. Standard Jira field IDs and the `jira_link_to_epic` tool do NOT work reliably. Always use the confirmed field IDs below.
>
> These fields are confirmed on **Task** and **Epic** issue types in both **CTDC** and **ICDC** projects — they are consistent across both.

### ✅ Confirmed Writable Fields (via `jira_update_issue`)

| Field Name | Custom Field ID | Value Format | Notes |
|---|---|---|---|
| **Epic Link** | `customfield_12350` | `"CTDC-XXXX"` (plain string) | Set via `fields` parameter. Confirmed working on Task type. Re-parent existing children by setting this on each child, not on the epic. Verified on CTDC-1691 re-parent to CTDC-2042, 2026-05-06. |
| **Epic Name** | `customfield_12351` | `"Epic display name"` (plain string) | The Epic Name field is separate from Summary on this tracker. Set this on epic creation via `additional_fields`. Verified on CTDC-2042 creation, 2026-05-06. |
| **Developer** | `customfield_23650` | `[{"name": "username"}]` (array of objects) | Multi-value user picker. Set via `fields` parameter. |

### ⚠️ Known Gotcha — Batch Read Inconsistency

**`jira_search` batch queries sometimes return `null` for populated custom fields.** This was observed with `customfield_23650` (Developer) on CTDC-1718 — the batch query returned `null`, but a single-ticket `jira_get_issue` confirmed the field was populated with `huangn4`.

**Rule:** When a custom field appears empty in a batch `jira_search` result, **always verify with a single-ticket `jira_get_issue` call before acting on it as "empty."** Flagging a field as empty in a stakeholder document (or asking the user to investigate) is a real cost — don't pay it on a false negative.

### ⚠️ Description Field — Escape Curly Braces

**Curly braces `{...}` in description text are treated as Jira-wiki macro syntax** and corrupt parser state for the surrounding block when the macro name is unknown. Symptoms include adjacent headers rendering as literal `h3.` text, bullets losing list context, and entire sections disappearing. **Fix:** escape every curly brace in description text as `\{...\}`. See Section 7b-shared "MCP write notes" for the full diagnostic and workflow. Other fields (priority, summary, custom fields, status, labels) are not routed through the wiki renderer and are unaffected.

### ⚠️ Comment Body Quirks

Comment bodies (added via `jira_add_comment` or `jira_transition_issue` with a `comment` parameter) pass through the same Jira-wiki renderer as descriptions:

- **Plus signs (`+`) get silently stripped** — interpreted as inserted-text markup. Use "and" or "plus" instead, or escape with backslash.
- **Curly braces** must be escaped as `\{...\}` per the description rule above.

Verified on CTDC-1658 closure comment, 2026-05-06.

### 🧯 Closing Tickets via Transitions

The NCI tracker workflow has two relevant gotchas when closing tickets via the MCP:

**Resolution naming:** Use `Won't Fix`, not `Won't Do`. The newer `Won't Do` resolution does not exist on this older Jira instance. `Duplicate` is also valid for genuine duplicates. Both have been verified on the `Open → Close` transition (id 191).

**Resolution requirement varies by source state:**

| Source Status | Transition ID | Resolution Required? | Notes |
|---|---|---|---|
| `Open` | 191 (`Close`) | Yes — must be one of the allowed values | Confirmed: `Won't Fix`, `Duplicate` both work. `Won't Do` does NOT work. |
| `Testing Hold` | 731 (`Move to Closed`) | No — accepts `null` | Issue closes with no resolution recorded. Follow up with a manual UI edit if reporting hygiene matters. |
| `In Progress` (Epic) | 231 (`Close`) | Yes: must be one of the allowed values | Confirmed: `Completed` works; `Done` returns "The selected resolution cannot be chosen during this action"; omitting the resolution returns "Resolution should be modified during this transition". Verified on CTDC-1802, 2026-09-14. |

**Discovery pattern:** there is no API that enumerates allowed resolutions per transition. Discover by trial. When closing, pick the most semantically appropriate resolution first; fall back to `Won't Fix` if it fails. After a successful transition, the resolution field is **not editable** via `jira_update_issue` ("Field 'resolution' cannot be set") — you must set it during the transition itself, or accept the gap.

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

**Create Epic with Epic Name and Placeholder Description:**
```json
jira_create_issue(
  project_key = "CTDC",
  summary = "CTDC Local Find (Participant)",
  issue_type = "Epic",
  assignee = "tesfatsionnh",
  description = "_Description being applied via update — see next revision._",
  additional_fields = {"priority": {"name": "Major"}, "labels": ["Task-1.3.7.3"]}
)

# Then immediately:
jira_update_issue(
  issue_key = "CTDC-XXXX",
  fields = {"description": "<full Markdown body>"},
  additional_fields = {"customfield_12351": "CTDC Local Find (Participant)"}
)
```

**Close a ticket with resolution:**
```json
jira_transition_issue(
  issue_key = "CTDC-826",
  transition_id = "191",
  fields = {"resolution": {"name": "Won't Fix"}},
  comment = "Closed as stale per CTDC Local Find epic restart 2026-05-06..."
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
| **Canonical?** | Yes — pinned as a submodule in `CBIIT/crdc-ctdc-starter-kit`. See Section 17. |

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
| **Canonical?** | Yes — pinned as a submodule in `CBIIT/crdc-ctdc-starter-kit`. See Section 17. |

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

---

## 17. 🗂️ CTDC Repo Identity & Canonical Disambiguation

> **Critical when an epic or ticket touches CTDC code.** CTDC has historical/abandoned repos that share name fragments with the active canonical repos. Reading code from the wrong repo silently produces wrong technical claims in tickets, epics, and stakeholder documents. Always verify the canonical repo first.

### 17a. The Canonical Entry Point

The single source of truth for which repos are part of the active CTDC system is the **`.gitmodules`** file in `CBIIT/crdc-ctdc-starter-kit`. The starter-kit is a meta-repo that pins each CTDC component as a git submodule. If a repo is listed in that file, it is canonical. If it is not, it is either historical, deprecated, or unrelated to active CTDC delivery.

**Starter-kit URL:** `https://github.com/CBIIT/crdc-ctdc-starter-kit`
**`.gitmodules` URL:** `https://github.com/CBIIT/crdc-ctdc-starter-kit/blob/main/.gitmodules`

### 17b. Active Canonical Repos (from starter-kit `.gitmodules`)

| Component | Canonical Repo | Default Branch | Purpose |
|---|---|---|---|
| **Frontend** | `CBIIT/crdc-ctdc-ui` | `main` | React web application — see all earlier sections referencing frontend code |
| **Backend** | `CBIIT/crdc-ctdc-backend` | `master` | GraphQL backend — see Section 15 |
| **File service** | `CBIIT/crdc-ctdc-files` | (verify before assuming) | File delivery service |
| **Auth service** | `CBIIT/crdc-ctdc-authn` | (verify before assuming) | Authentication service (renamed from `crdc-ctdc-auth`; the old name still resolves via GitHub redirect) |
| **Data loader (CTDC-specific)** | `CBIIT/crdc-ctdc-dataloader` | (verify before assuming) | CTDC-specific data ingestion |
| **Data loader (shared ICDC base)** | `CBIIT/icdc-dataloader` | (verify before assuming) | Shared loader code |
| **Interoperation** | `CBIIT/crdc-ctdc-interoperation` | (verify before assuming) | Cross-CRDC interoperation |
| **Static content** | `CBIIT/bento-ctdc-static-content` | (verify before assuming) | Static HTML/text content |
| **Deployment** | `CBIIT/ctdc-deployment` | `main` | Deployment manifests |
| **Data model** | `CBIIT/ctdc-model` | `prod` | Graph model — see Section 16 |
| **ReadMe / help content** | `CBIIT/ctdc-readMe-content` | `prod` | User-facing help content |

> **Important:** verify branch names against the `.gitmodules` file or each repo's GitHub default before assuming. The frontend uses `main`, the backend uses `master`, the data model uses `prod` — they are not consistent across components. Also note that a renamed repo keeps resolving under its old name via GitHub's redirect, so `.gitmodules` will not reveal a rename: the auth service is still pinned there as `crdc-ctdc-auth`, but the live repo is now `crdc-ctdc-authn`. Confirm the current repo name on GitHub, not just the submodule pin.

### 17c. Historical / Deprecated Repos (DO NOT use as a source of truth)

These repos exist on `org:CBIIT` and may surface in name-based GitHub searches, but they are **not** part of the active CTDC system. Reading code from them or citing their file paths in tickets is a methodology error.

| Repo | Why It Surfaces in Searches | Status |
|---|---|---|
| `CBIIT/bento-ctdc-frontend` | Created June 2021, last pushed August 2023. Was the original CTDC frontend before the 2023 reboot. | **Abandoned.** The CTDC project was rebooted in July 2023 around the starter-kit + `crdc-ctdc-ui`. Any code or PRs in this repo predate the active production system. |
| `CBIIT/QA_TestComplete` | Holds 2020-era TestComplete scripts for ICDC + CTDC. | **Inactive** as of 2023. |
| `CBIIT/ctdc_uitesting` | 2020-era JUnit test scaffolding. | **Inactive** as of 2020. |
| `CBIIT/ctdc-data-processing` | 2019-era data processing scripts; superseded by `ctdc-dataloader`. | **Inactive** as of 2022. |
| `CBIIT/ctdc-deployments` (with the trailing `s`) | Older deployment artifacts. The canonical deployment repo is `ctdc-deployment` (no `s`). | **Inactive.** Confirmed by `.gitmodules` pin to `ctdc-deployment` (singular). |

When in doubt, the rule is: **if the repo is not in the starter-kit `.gitmodules`, do not treat it as part of active CTDC.**

### 17d. Verification Workflow When an Epic or Ticket Touches Code

1. **Open `CBIIT/crdc-ctdc-starter-kit/.gitmodules`** as the first step. Confirm the component you're investigating is pinned there and note the repo URL.
2. **Read inside the canonical repo only.** Do not read code from a name-similar repo without first checking it appears in `.gitmodules`.
3. **Verify the branch.** Use the default branch listed in Section 17b above, or check the repo's GitHub default if the table lists "verify before assuming."
4. **Cite repo paths in epics/tickets in the form `CBIIT/<repo-name>` with branch where ambiguity matters** (e.g., "branch `main`," "branch `master`," "branch `prod`"). This makes wrong-repo errors easier to catch in review.
5. **If a code search returns a name-matching repo not in `.gitmodules`, ignore it.** This is the most common path to a wrong-repo error — GitHub search treats the historical repo and the canonical repo as equivalent matches by name, but they are not.

### 17e. Why This Section Exists

This section was added 2026-05-06 after a near-miss during the CTDC-2042 (Local Find — Participant) epic creation. The session author searched GitHub for CTDC frontend repos, found both `bento-ctdc-frontend` (last pushed 2023) and `crdc-ctdc-ui` (actively developed), and used the recently-updated heuristic to pick `crdc-ctdc-ui`. That happened to land on the correct canonical repo, but only by coincidence. The user (Senior TPM) caught the methodology gap and pointed at the starter-kit as the source of truth. Section 17 formalizes that as the verified-first-step methodology so the next person doesn't have to rely on coincidence. See also Section 9c for the lessons-learned record.
