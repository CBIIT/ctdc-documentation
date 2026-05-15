# 📚 CTDC Templates Library

This folder is the **component library** for CTDC ticket templates and recurring workflow SOPs. Each component is a discrete `.md` file that can be linked from individual tickets, evolved independently, and read directly without paging through the much larger SKILL.md.

The SKILL.md remains the operational knowledge base (SOPs, JQL recipes, deck standards, session recovery, etc.). Templates and workflows that have outgrown an inline section in SKILL.md, or that are large enough to deserve their own home, live here.

---

## 🧭 The Two Functions of the CTDC Team

Templates in this library map to one of the team's two primary functions. Knowing which lane a piece of work belongs in is the first step in picking the right template.

- **Software development** — designing, coding, testing, and releasing the React frontend, the Java backend, the microservices, and the infrastructure. Verified against application *behavior*. Includes schema and data model changes (those touch backend loader code, frontend rendering, and a graph migration step). Tracked with the User Story, Features Epic, Application Pages Epic, Microservices Epic, Design Task, and Bug templates.
- **Data management** — managing CRDC submissions and loading data into CTDC's databases. Verified against application *contents* (row counts, page renders, downloadable artifacts). Tracked with the Data Loading Task template.

Application pages updating when new data is loaded is the application working as designed — it does not mean software work is involved. The model is stable, the code is unchanged; only the contents shift.

---

## 🎫 Template Inventory

| Lane | Template | File | Status | Canonical Examples |
|---|---|---|---|---|
| Software development | **Design Task** | [`design-task-template.md`](./design-task-template.md) | ✅ Drafted v1 (2026-05-06) | CTDC-2044, CTDC-2045 |
| Data management | **Data Loading Task** | [`data-loading-task-template.md`](./data-loading-task-template.md) | ✅ Drafted v3 (2026-05-15) — scope is data management only; software development work uses the templates above | CMB v5 load (CTDC-1753 lineage) |

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
3. Add a row to the appropriate inventory table above (Template Inventory or Workflow / SOP Inventory). Note the lane (software development vs. data management) for ticket templates.
4. If migrating from SKILL.md, leave a small cross-reference in SKILL.md pointing here (don't delete the section header — replace its body with a pointer).
5. Update `SKILL.md` Section 9a (Epic Template Status Tracker) with the new entry if it's a ticket template.
6. Add a lessons-learned subsection in `SKILL.md` Section 9 if the component's drafting produced any reusable methodology insights.

---

## 🧭 How Claude Should Use This Folder

When the user asks Claude to draft a ticket or run a recurring workflow:

1. **First, decide which lane the work is in** — software development or data management. The Data Loading Task template covers only the data management lane. Everything else is software development.
2. **Check if a component exists for that task** — look in this folder and in `SKILL.md` Section 7 (for ticket templates) or Section 12 (for meeting/sprint workflows).
3. **If yes, follow the component exactly** — section order, emoji set, content rules, step order, all of it.
4. **If no component exists yet**, fall back to the closest adjacent component and note the gap to the user so it can be drafted.
5. **Always link the canonical component file** in the ticket's Notes section or in chat replies if relevant, so the next person picking up similar work knows where the convention is documented.

---

*This folder was created 2026-05-06 alongside the Design Task template. The Post-Meeting Sprint Recap workflow was added 2026-05-15 from the Sprint 26 Review & Retro session. The Data Loading Task template was added 2026-05-15 and iterated through v3 the same day to clarify scope as data management only — schema and model changes belong in the software development lane and use the existing software development templates.*
