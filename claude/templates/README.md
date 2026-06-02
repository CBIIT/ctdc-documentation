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

Data management has **two sub-functions**, and each sub-function splits into multiple work patterns — four templates total in the data management lane:

- **Loading data** — taking a CRDC submission's *contents* into CTDC's databases. Two work patterns:
  - **End-to-end load** — promoting metadata through Dev → QA → Stage → Prod after IndexD has minted the GUIDs. Use the **Data Loading Task** template (7e).
  - **Upstream artifact creation — IndexD registration** — minting GUIDs for the submission's files via the external CTDS/DCFS handoff. Prerequisite to the end-to-end load. Use the **IndexD Registration Task** template (7h).
- **Modeling data** — changing the *shape* of what CTDC's databases can hold. **Every model change records in a CDE Request Workbook — there is no "no workbook" path.** The two work patterns split on the *driver* of the change, and the driver selects which workbook holds the term-level truth:
  - **Study-driven model changes** — properties, enums, or permissible values requested by an incoming study submission, anchored on that **study's** CDE Request Workbook (owned by the study's Data Concierge). Use the **Data Modeling for Study Submission** template (7g).
  - **Internally / CTDC-driven model changes** — changes initiated by the CTDC project itself (the application roadmap or the data team), across the full weight range from a single additive optional property to a breaking multi-repo refactor, anchored on the single persistent **internal CTDC CDE Request Workbook** (owned by the CTDC project, no individual owner). Use the **Data Model Update Task** template (7f).

Application pages updating when new data is loaded is the application working as designed — it does not mean software work is involved. The model is stable, the code is unchanged; only the contents shift. Schema changes are a different sub-function within data management, not software development, because the *concern* is data shape — the cascading loader code and frontend rendering updates are owned by the data management ticket as children of the model change.

---

## 🎫 Template Inventory

### Software development lane

| Template | File | Status | Canonical Examples |
|---|---|---|---|
| **Design Task** | [`design-task-template.md`](./design-task-template.md) | ✅ Drafted v1 (2026-05-06) | CTDC-2044, CTDC-2045 |

### Data management lane

| Template | File | Sub-function / Work pattern | Status | Canonical Examples |
|---|---|---|---|---|
| **Data Loading Task** | [`data-loading-task-template.md`](./data-loading-task-template.md) | Loading data — end-to-end load | ✅ Drafted v4 (2026-05-15) — loads CRDC submissions into the existing schema; routes schema work to the modeling templates | CMB v5 load (CTDC-1753 lineage) |
| **IndexD Registration Task** | [`indexd-registration-task-template.md`](./indexd-registration-task-template.md) | Loading data — upstream artifact creation (IndexD) | ✅ Drafted v1 (2026-05-26) — external CTDS/DCFS handoff for GUID minting; prerequisite to the Data Loading Task | CTDC-1907 (TCIA CMB radiology images, 2,632 files) — canonical for workflow shape; predates parent-user-story discipline and is flagged for one-time retrofit |
| **Data Modeling for Study Submission** | [`data-modeling-study-submission-template.md`](./data-modeling-study-submission-template.md) | Modeling data — study-driven | ✅ Drafted v3 (2026-05-20) — canonical 6-section shape; the study's CDE Request Workbook (owned by the study's Data Concierge) is the source of truth for term-level state; ticket exists for milestone tracking only | CTDC-2051 (NCI-MATCH Arm Z1D) ↔ parent CTDC-1666; CTDC-1799 (CIMAC-CIDC) ↔ parent CTDC-1804 |
| **Data Model Update Task** | [`data-model-update-template.md`](./data-model-update-template.md) | Modeling data — internally / CTDC-driven | ✅ Drafted v7 (2026-06-02) — all internally-driven model changes, additive through breaking; 5 sections (milestone tracker, not a term inventory); records in the internal CTDC CDE Request Workbook (owned by the project, no individual owner); caDSR/ServiceNow and DC sign-off gates; Promotion Workflow follows the Data Model Contribution SOP (git PR flow, not a loading pipeline); per-environment progress tracked by native Jira status (no signoff table) | CTDC-2068 (four Program curation properties — canonical pilot) |

---

## 🔁 Workflow / SOP Inventory

Recurring per-cadence workflows (per-sprint, per-release, per-meeting, per-quarter) that produce stakeholder artifacts. These are different from ticket templates — they're instructions for *what to do*, not *how to format a ticket*.

| Workflow | File | Cadence | Status | Canonical Examples |
|---|---|---|---|---|
| **Post-Meeting Sprint Recap** | [`post-meeting-sprint-recap-workflow.md`](./post-meeting-sprint-recap-workflow.md) | Once per sprint, immediately after Review & Retro | ✅ Drafted v1 (2026-05-15) | Sprint 25 recap (2026-04-24), Sprint 26 recap (2026-05-15) |
| **CSP Quarterly Report** | [`csp-quarterly-report-workflow.md`](./csp-quarterly-report-workflow.md) | Once per quarter, aligned to BACS/FNL reporting period | ✅ Drafted v1 (2026-05-22) | Nov 2025 – Jan 2026 CSP (drafted 2026-03-02); Mar 2026 – May 2026 CSP (drafted 2026-05-22) |

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
   - Workflow SOPs: `<cadence>-<artifact-type>-workflow.md` (e.g., `post-meeting-sprint-recap-workflow.md`, `pre-release-tagging-hygiene-workflow.md`, `csp-quarterly-report-workflow.md`).
2. Inside the file, follow the standard structure: header with usage statement, "Why this template/workflow" rationale, section order or step order, standing emoji set, required content rules, writing-and-publishing or run-and-distribute workflow, when-to-expand-vs-trim guidance.
3. Add a row to the appropriate inventory table above (Template Inventory or Workflow / SOP Inventory). Note the lane (software development vs. data management) and the sub-function and work pattern (for data management templates) for ticket templates.
4. If migrating from SKILL.md, leave a small cross-reference in SKILL.md pointing here (don't delete the section header — replace its body with a pointer).
5. Update `SKILL.md` Section 9a (Epic Template Status Tracker) with the new entry if it's a ticket template.
6. Add a lessons-learned subsection in `SKILL.md` Section 9 if the component's drafting produced any reusable methodology insights. For workflow components, drop a dated lessons-learned file into `claude/templates/lessons-learned/YYYY-MM-DD-<short-name>.md`.

---

## 🧭 How Claude Should Use This Folder

When the user asks Claude to draft a ticket or run a recurring workflow:

1. **First, decide which lane the work is in** — software development or data management. If data management, decide which sub-function and work pattern. Use this decision tree:
   - *Is new data being loaded into the existing schema?* (new study, new files for an existing study) → **Data Loading Task (7e)**
   - *Are the files in this submission still pending IndexD GUID minting via the external CTDS/DCFS team?* (prerequisite to a planned Data Loading Task) → **IndexD Registration Task (7h)**
   - *Is the schema changing because an incoming **study submission** needs new properties / enums / permissible values, with that study's CDE Request Workbook as the spec?* → **Data Modeling for Study Submission (7g)** — study-driven; records in the study's workbook (owned by the study's Data Concierge)
   - *Is the schema changing because the **CTDC project itself** decided to change it — the application roadmap or the data team — anywhere from a single additive optional property to a breaking multi-repo refactor?* → **Data Model Update Task (7f)** — internally/CTDC-driven; records in the internal CTDC workbook (owned by the project, no individual owner)
   - *(Either way, the change always records in a CDE Request Workbook — the driver just decides which one. There is no "no workbook" path.)*
   - *None of the above?* → software development family
2. **Check if a component exists for that task** — look in this folder and in `SKILL.md` Section 7 (for ticket templates) or Section 12 (for meeting/sprint/quarterly workflows).
3. **If yes, follow the component exactly** — section order, emoji set, content rules, step order, all of it.
4. **If no component exists yet**, fall back to the closest adjacent component and note the gap to the user so it can be drafted.
5. **Always link the canonical component file** in the ticket's Notes section or in chat replies if relevant, so the next person picking up similar work knows where the convention is documented.

---

*This folder was created 2026-05-06 alongside the Design Task template. The Post-Meeting Sprint Recap workflow was added 2026-05-15 from the Sprint 26 Review & Retro session. The Data Loading Task template was added 2026-05-15 and iterated through v4 the same day to settle the scope: data management has two sub-functions (loading and modeling). The Data Model Update Task template was added 2026-05-15 as the first modeling template, designed around the heavyweight case (breaking changes, framework upgrades, multi-repo coordination). On 2026-05-20, the Data Modeling for Study Submission template was added as the common-case modeling template. Through three same-day iterations the template settled on its canonical v3 shape: six sections (Modeling Summary, CDE Request Workbook, Branch & Release Signoff, Verification Surfaces, Open Questions & Risks, Notes). v3 enforces the discipline that the workbook is the term-level source of truth and the ticket exists for milestone tracking only — Term Status Summary, Modeling & Promotion Workflow, Linked Work, and Collaboration sections that appeared in an intermediate v2 draft were removed because they duplicated content that already lives in the workbook, the SOP, Jira's native issue links, or the assignee field. CTDC-2051 (NCI-MATCH Arm Z1D, parent CTDC-1666) and CTDC-1799 (CIMAC-CIDC, parent CTDC-1804) are both in v3 shape and serve as the canonical pair. The CSP Quarterly Report workflow was added 2026-05-22 from the March 2026 – May 2026 CSP drafting session. The drafting session uncovered six lessons captured in `lessons-learned/2026-05-22-csp-quarterly-report.md`: software releases and data releases are strictly separate events; three dates per software release with the public one leading; the Bento Framework does not yet include a DMN (DMN contributions go to the CRDC Data Hub team's package); Monthly Summaries carry program-level context Jira can't see; excluding Test issue types from the Jira pull is essential; and the prior CSP's format is to be matched verbatim rather than reinvented. The IndexD Registration Task template was added 2026-05-26 to formalize the upstream artifact creation work pattern within the loading-data sub-function. The template treats IndexD registration as a coordination ticket for the external CTDS/DCFS handoff (manifest upload to DCF Google Drive → CRINTAKE intake ticket → DCFS confirmation → GUID spot-check), with mandatory Relates link to the parent submission user story, mandatory Blocks link to the downstream Data Loading Task, and mandatory remote link to the CRINTAKE ticket. CTDC-1907 is canonical for the workflow shape but predates the parent-user-story discipline; the template recommends a one-time retrofit to add the Relates link. On 2026-06-01, the Data Model Update Task template was reframed to v3: the 7f/7g split was redrawn on the **driver** of the change (internally/CTDC-driven vs. study-driven) rather than on weight (heavyweight vs. lightweight), and the CDE Request Workbook was made universal — every model change records in a workbook, the internal CTDC project workbook for 7f and the study's workbook for 7g, with no "no workbook" path. 7f grew a CDE Request Workbook section (internal workbook, owned by the project with no individual owner), caDSR/ServiceNow coordination, and a formal Data Commons sign-off gate, and now spans the full additive-to-breaking range; CTDC-2068 (four Program curation properties) is its canonical pilot. The README decision tree, the Modeling-data sub-function description, and the 7f/7g inventory rows were updated the same day to match. Later the same cycle, 7f was slimmed to v4 (7 sections) after CTDC-2068 was hand-tightened in use: the Affected Code & Loaders, Verification Surfaces, Per-Environment Verification, Collaboration & Handoffs, and Open Questions / Risks sections were removed; Model Change Details was trimmed to milestones (SemVer, current/target version, backward compatibility) plus the three-files schema-artifact checklist, with all term-level detail living in the workbook; per-environment verification was folded into the Promotion Workflow steps and signed off in Testing Signoff; and the workbook table dropped its Owner and caDSR-status rows (the Source-of-Truth / Out-of-Scope rows were kept). Minutes later, v5 removed the Linked Work section as well — related tickets are carried by native Jira issue links — leaving 6 sections. On 2026-06-02, 7f v6 corrected the Promotion Workflow: it had carried the data-*loading* promotion ladder (Jenkins lower/upper-tier pipelines, per-environment loads), but model contribution is a git feature-branch → PR into `develop` → PR `develop` → `prod` → tag and GitHub Release flow per `SOP_CTDC_Data_Model_Contribution.md`, with no loading pipeline; the same fix was applied to the canonical pilot CTDC-2068. 7g's Branch & Release Signoff was already the contribution flow and needed no change. Also on 2026-06-02, 7f v7 removed the Testing Signoff section — per-environment progress (Dev/QA/Stage/Prod) is tracked by the ticket's native Jira status, transitions, and assignee, so a signoff table in the description was redundant; the DC sign-off now lives only in the Section 2 caDSR table, and CTDC-2068 was updated to match (now 5 sections). A follow-on edit will register the universal-workbook routing rule in SKILL.md and apply the parallel SN-capture / DC-sign-off touches to 7g.*
