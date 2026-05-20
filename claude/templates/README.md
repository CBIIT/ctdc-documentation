# 📚 CTDC Templates Library

This folder is the **component library** for CTDC ticket templates and recurring workflow SOPs. Each component is a discrete `.md` file that can be linked from individual tickets, evolved independently, and read directly without paging through the much larger SKILL.md.

The SKILL.md remains the operational knowledge base (SOPs, JQL recipes, deck standards, session recovery, etc.). Templates and workflows that have outgrown an inline section in SKILL.md, or that are large enough to deserve their own home, live here.

---

## 🧭 The Two Functions of the CTDC Team

Templates in this library map to one of the team's two primary functions. Knowing which lane a piece of work belongs in is the first step in picking the right template.

### Software development
Designing, coding, testing, and releasing the React frontend, the Java backend, the microservices, and the infrastructure. Verified against application *behavior*. Tracked with the User Story, Features Epic, Application Pages Epic, Microservices Epic, Design Task, and Bug templates.

### Data management
Managing CRDC submissions, modeling the shape of CTDC's data, and loading data into CTDC's databases. Verified against application *contents* (row counts, page renders, downloadable artifacts) and against schema state (node types, properties, relationships, version numbers).

Data management has **two sub-functions**, and the modeling sub-function further splits into **two work patterns** — three templates total in the data management lane:

- **Loading data** — taking a CRDC submission's *contents* into CTDC's databases. Use the **Data Loading Task** template (7e).
- **Modeling data** — changing the *shape* of what CTDC's databases can hold. Two work patterns:
  - **Study-driven model additions** — properties, enums, or permissible values requested by an incoming study submission, anchored on a CDE Request Workbook. Almost always MINOR-version additions. Common — one per study onboarding or property request. Use the **Data Modeling for Study Submission** template (7g).
  - **Infrastructure-level model changes** — breaking schema changes, framework upgrades, multi-repo refactors initiated by the data team itself. Rare. Use the **Data Model Update Task** template (7f).

Application pages updating when new data is loaded is the application working as designed — it does not mean software work is involved. The model is stable, the code is unchanged; only the contents shift. Schema changes are a different sub-function within data management, not software development, because the *concern* is data shape — the cascading loader code and frontend rendering updates are owned by the data management ticket as children of the model change.

---

## 🎫 Template Inventory

### Software development lane

| Template | File | Status | Canonical Examples |
|---|---|---|---|
| **Design Task** | [`design-task-template.md`](./design-task-template.md) | ✅ Drafted v1 (2026-05-06) | CTDC-2044, CTDC-2045 |

### Data management lane

| Template | File | Sub-function | Status | Canonical Examples |
|---|---|---|---|---|
| **Data Loading Task** | [`data-loading-task-template.md`](./data-loading-task-template.md) | Loading data | ✅ Drafted v4 (2026-05-15) — loads CRDC submissions into the existing schema; routes schema work to the modeling templates | CMB v5 load (CTDC-1753 lineage) |
| **Data Modeling for Study Submission** | [`data-modeling-study-submission-template.md`](./data-modeling-study-submission-template.md) | Modeling data — study-driven | ✅ Drafted v1 (2026-05-20) — common case: study submission's CDE Request Workbook drives schema additions; close trigger is Submission Portal verification | CTDC-1799 (CIMAC-CIDC modeling) |
| **Data Model Update Task** | [`data-model-update-template.md`](./data-model-update-template.md) | Modeling data — infrastructure-level | ✅ Drafted v2 (2026-05-20) — rare case: breaking schema changes, framework upgrades, multi-repo refactors with loader / frontend code coordination | TBD — first pilot pending |

---

## 🔁 Workflow / SOP Inventory

Recurring per-cadence workflows (per-sprint, per-release, per-meeting) that produce stakeholder artifacts. These are different from ticket templates — they're instructions for *what to do*, not *how to format a ticket*.

| Workflow | File | Cadence | Status | Canonical Examples |
|---|---|---|---|---|
| **Post-Meeting Sprint Recap** | [`post-meeting-sprint-recap-workflow.md`](./post-meeting-sprint-recap-workflow.md) | Once per sprint, immediately after Review & Retro | ✅ Drafted v1 (2026-05-15) | Sprint 25 recap (2026-04-24), Sprint 26 recap (2026-05-15) |

---

## 📖 Templates Still Inline in SKILL.md (Not Yet Migrated)

These templates still live as sections inside `claude/SKILL.md`. They will be migrated here when they next get materially expanded, or in a focused migration pass. All of the below are software-development-lane templates.

| Template | SKILL.md Section | Status | Canonical Examples |
|---|---|---|---|
| **User Story** | Section 7a | ✅ Drafted v1 (2026-05-06) | CTDC-1691 |
| **Application Pages Epic** | Section 7b-1 | ✅ Drafted v1 | CTDC-2025 (gold standard) |
| **Microservices Epic** | Section 7b-2 | 🚧 Stub — TBD | TBD |
| **Features Epic** | Section 7b-3 | ✅ Drafted v1 (2026-05-06) | CTDC-2042 |
| **Products Epic** | Section 7b-4 | 🚧 Stub — TBD | TBD |
| **Infrastructure Epic** | Section 7b-5 | 🚧 Stub — TBD | TBD |
| **Security Epic** | Section 7b-6 | 🚧 Stub — TBD | TBD |
| **Data Epic** | Section 7b-7 | 🚧 Stub — TBD | TBD |
| **Bug Format** | Section 7c | ✅ Lightweight format (always was small) | n/a |

---

## 🧱 Why a Component Library?

Three reasons we're moving toward this pattern:

1. **Components are linkable from tickets and chats.** A Jira ticket can link directly to `claude/templates/design-task-template.md` so any reader (Claude, an engineer, the designer, a reviewer) lands on the canonical template without paging through the 120k-char SKILL.md. A workflow SOP can be linked from a session start so Claude reads only the relevant procedure.

2. **Components evolve independently.** When the Design Task template gets a new section or a new lessons-learned note, the change touches a focused file — not a sprawling monolith. PRs are easier to review.

3. **The SKILL.md stays leaner.** SKILL.md is still the right home for SOPs and reference data — the operational knowledge that's used as a working reference. Discrete components (ticket templates, recurring workflows) belong in this catalog.

---

## ➕ Adding a New Component Here

When migrating an inline SKILL.md section to this folder, or drafting a new component from scratch:

1. Create the file in this folder. Naming conventions:
   - Ticket templates: `<artifact-type>-template.md` (e.g., `bug-template.md`, `epic-features-template.md`).
   - Workflow SOPs: `<cadence>-<artifact-type>-workflow.md` (e.g., `post-meeting-sprint-recap-workflow.md`, `pre-release-tagging-hygiene-workflow.md`).
2. Inside the file, follow the standard structure: header with usage statement, "Why this template/workflow" rationale, section order or step order, standing emoji set, required content rules, writing-and-publishing or run-and-distribute workflow, when-to-expand-vs-trim guidance.
3. Add a row to the appropriate inventory table above (Template Inventory or Workflow / SOP Inventory). Note the lane (software development vs. data management) and the sub-function and work pattern (for data management templates) for ticket templates.
4. If migrating from SKILL.md, leave a small cross-reference in SKILL.md pointing here (don't delete the section header — replace its body with a pointer).
5. Update `SKILL.md` Section 9a (Epic Template Status Tracker) with the new entry if it's a ticket template.
6. Add a lessons-learned subsection in `SKILL.md` Section 9 if the component's drafting produced any reusable methodology insights.

---

## 🧭 How Claude Should Use This Folder

When the user asks Claude to draft a ticket or run a recurring workflow:

1. **First, decide which lane the work is in** — software development or data management. If data management, decide which sub-function and (for modeling) which work pattern. Use this decision tree:
   - *Is new data being loaded into the existing schema?* (new study, new files for an existing study) → **Data Loading Task (7e)**
   - *Is the schema changing because a study submission needs new properties / enums / permissible values, with a CDE Request Workbook as the spec?* → **Data Modeling for Study Submission (7g)** — this is the common case
   - *Is the schema changing in a breaking way or in a way that requires loader / frontend code changes (framework upgrade, multi-repo refactor, structural change initiated internally)?* → **Data Model Update Task (7f)** — the rare heavyweight case
   - *None of the above?* → software development family
2. **Check if a component exists for that task** — look in this folder and in `SKILL.md` Section 7 (for ticket templates) or Section 12 (for meeting/sprint workflows).
3. **If yes, follow the component exactly** — section order, emoji set, content rules, step order, all of it.
4. **If no component exists yet**, fall back to the closest adjacent component and note the gap to the user so it can be drafted.
5. **Always link the canonical component file** in the ticket's Notes section or in chat replies if relevant, so the next person picking up similar work knows where the convention is documented.

---

*This folder was created 2026-05-06 alongside the Design Task template. The Post-Meeting Sprint Recap workflow was added 2026-05-15 from the Sprint 26 Review & Retro session. The Data Loading Task template was added 2026-05-15 and iterated through v4 the same day to settle the scope: data management has two sub-functions (loading and modeling). The Data Model Update Task template was added 2026-05-15 as the first modeling template, designed around the heavyweight case (breaking changes, framework upgrades, multi-repo coordination). On 2026-05-20, the Data Modeling for Study Submission template was added as the common-case modeling template — most CTDC modeling work is study-driven additions anchored on a CDE Request Workbook (CTDC-1799 is the canonical example), and that pattern deserves its own lighter template separate from the heavyweight 7f. The Data Model Update Task template was iterated to v2 the same day to clarify its scope as infrastructure-level changes and route study-driven work to 7g.*
