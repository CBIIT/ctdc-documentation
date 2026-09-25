# 📚 CTDC Component Library

This directory is the **component library** for CTDC ticket templates and recurring workflow SOPs. **Ticket templates** (how to format a Jira artifact) live in [`templates/`](./templates/); **process SOPs and runbooks** (what to do, per cadence) live in [`workflows/`](./workflows/); dated methodology notes captured while drafting them live in [`lessons-learned/`](./lessons-learned/). Each component is a discrete `.md` file that can be linked from individual tickets, evolved independently, and read directly without paging through the much larger SKILL.md.

The SKILL.md remains the operational knowledge base (SOPs, JQL recipes, deck standards, session recovery, etc.). Templates and workflows that have outgrown an inline section in SKILL.md, or that are large enough to deserve their own home, live here.

---

## 🧭 The Two Functions of the CTDC Team

Templates in this library map to one of the team's two primary functions. Knowing which lane a piece of work belongs in is the first step in picking the right template.

### Software development
Designing, coding, testing, and releasing the React frontend, the Java backend, the microservices, and the infrastructure. Verified against application *behavior*. Tracked with the User Story, Epic, Design Task, Release, and Bug templates.

### Data management
Managing CRDC submissions, modeling the shape of CTDC's data, and loading data into CTDC's databases. Verified against application *contents* (row counts, page renders, downloadable artifacts) and against schema state (node types, properties, relationships, version numbers).

**Every study submission is one Data Submission Epic** (DO-EPIC, since 2026-09-25). The epic carries study identity (Submission Details, lifecycle, risks, chronology) and closes when the study is verified in Production. It replaces the Data Submission user story (DO-STORY, retired) and the single standing CTDC Data Integration epic (CTDC-1664). The submission's tasks hang off it, and they fall into **two sub-functions**:

- **Loading data**: taking a CRDC submission's *contents* into CTDC's databases. Four work patterns, all children of the submission epic through the Epic Link and linked to each other with `Relates`, never `Blocks`:
  - **Consent-group gate**: running the `dbgap_validatation_prod` Prefect deployment against the released submission so its consent group / ACL values reconcile with dbGaP before anything is indexed or loaded. Use the **dbGaP Validation Task** (DO-DBGAP).
  - **IndexD registration**: minting GUIDs for the submission's files through the external CTDS/DCFS handoff. Runs in parallel with the load; file downloads resolve once the GUIDs are minted and spot-checked. Use the **IndexD Registration Task** (DO-INDEX).
  - **End-to-end load**: promoting metadata through Dev, QA, Stage, and Prod with a dedicated Jenkins job per tier, checking counts against the Expected Counts table at each tier. Use the **Data Loading Task** (DO-LOAD).
  - **Megazip**: bundling the study's object files into one `.zip` per `data_file_type`, self-minting a GUID for each, indexing, and loading them so each file type downloads in one click. Use the **Megazip Creation Task** (DO-ZIP).
- **Modeling data**: changing the *shape* of what CTDC's databases can hold. **Every model change records in a CDE Request Workbook; there is no "no workbook" path.** The two work patterns split on the *driver* of the change, and the driver selects which workbook holds the term-level truth:
  - **Study-driven model changes**: properties, enums, or permissible values requested by an incoming study submission, anchored on that **study's** CDE Request Workbook (owned by the study's Data Concierge). Use **Data Modeling for Study Submission** (DO-MODEL). The task is a child of the Submission Data Modeling epic and links to the study's submission epic with `Relates`.
  - **Internally / CTDC-driven model changes**: changes initiated by the CTDC project itself (the application roadmap or the data team), across the full range from a single additive optional property to a breaking multi-repo refactor, anchored on the single persistent **internal CTDC CDE Request Workbook** (owned by the CTDC project, no individual owner). Use the **Data Model Update Task** (DO-INTMODEL), a child of the Internal Data Modeling epic.

The two modeling templates share one shape except for a single section: DO-MODEL (v11) carries a study-specific ⭐ Data Concierge section (6 sections), while DO-INTMODEL deliberately stays at 5. Otherwise they differ only by context: which workbook holds the term-level truth, and which surface is the close trigger.

Application pages updating when new data is loaded is the application working as designed: it does not mean software work is involved. The model is stable, the code is unchanged; only the contents shift. Schema changes are a different sub-function within data management, not software development, because the *concern* is data shape: the cascading loader code and frontend rendering updates are owned by the data management ticket as children of the model change.

---

## 🎫 Template Inventory

### Software development lane

| Template | File | Status | Canonical Examples |
|---|---|---|---|
| **Design Task** | [`design-task-template.md`](./templates/design-task-template.md) | ✅ Drafted v3 (2026-09-04): no Jira ticket keys in the body, colons instead of em dashes; v2 (2026-07-07) slimmed from 10 to 7 sections | CTDC-2044, CTDC-2045 |
| **Release Ticket Templates** | [`release-ticket-templates.md`](./templates/release-ticket-templates.md) | ✅ Drafted v1 (2026-07-08): the three tickets cloned every software release (Final Design QA epic, Stage deploy task, Prod deploy task); house-style bodies, deploy tasks parented to the standing epic CTDC-2007; companion to the Software Release Playbook | Templates CTDC-2135 (FDQA epic), CTDC-2136 (Stage), CTDC-2137 (Prod); live CTDC-2130 / CTDC-2128 / CTDC-2129 |

### Data management lane

> **Template IDs are stable Data Operations (DO) codes** (since 2026-07-23; SKILL.md Section 7, "Data Management Templates"). A code is assigned once and never reused; a retired template keeps its code, marked retired. Operational order is carried by the Step column. Crosswalk from the older letter IDs:
>
> | DO code | Legacy ID (2026-06-16 to 2026-07-23) | Legacy ID (before 2026-06-16) |
> |---|---|---|
> | DO-EPIC | none (added 2026-09-25) | none |
> | DO-STORY *(retired 2026-09-25)* | `7e` | `7j` |
> | DO-MODEL | `7f` | `7g` |
> | DO-INDEX | `7g` | `7h` |
> | DO-LOAD | `7h` | `7e` |
> | DO-ZIP | `7i` | `7i` |
> | DO-INTMODEL | `7j` | `7f` |
> | DO-DBGAP | `7k` | none |

**Study Submission Flow**

| Step | Template | File | Work pattern | Status | Canonical Examples |
|---|---|---|---|---|---|
| 1 | **DO-EPIC · Data Submission Epic** | [`data-submission-epic-template.md`](./templates/data-submission-epic-template.md) | Submission: one epic per study submission | ✅ Drafted v1 (2026-09-25): card line plus 9 sections (🎯 Epic Summary · 🧬 Context & Background · 🏁 Goal / Objectives · 🗂️ Submission Details · 🚦 Submission Lifecycle · ✅ Acceptance Criteria · ⚠️ Risks & Mitigations · 📅 Submission Chronology · 📝 Notes); Process Documentation link first in Submission Details; chronology holds milestones and decisions only, never data model versions; title `CTDC Data Submission: <Program Short Name> <Study Short Name> <version>`, reused as the token in every child title; labels `Task-1.2.1` and `Data-Concierge` | CTDC-2110 (CMB v6, pilot) |
| 2 | **DO-MODEL · Data Modeling for Study Submission** | [`data-modeling-study-submission-template.md`](./templates/data-modeling-study-submission-template.md) | Modeling data: study-driven | ✅ Drafted v11 (2026-07-22): 6 sections (🎯 Modeling Summary · 📚 CDE Request Workbook · ⭐ Data Concierge · 🔍 DM Federal Lead & SME Review · 🪜 Steps to Completion · 🧪 Verification Surfaces); milestone tracker, no specifics, no counts; child of the Submission Data Modeling epic, `Relates` to the submission epic; title `Submission Data Modeling: <token>` | CTDC-2051 (NCI-MATCH Arm Z1D); CTDC-2111 (CMB v6, current link pattern) |
| 3 | **DO-DBGAP · dbGaP Validation Task** | [`dbgap-validation-task-template.md`](./templates/dbgap-validation-task-template.md) | Loading data: consent-group gate | ✅ Drafted v2 (2026-09-16): 4 sections (🎯 Validation Summary · 📦 Submission & Artifacts · 🚦 Validation Workflow · 🧪 Verification); runs `dbgap_validatation_prod` with `check_consent_group` on; the gate is a process rule, so links are `Relates`; `Data-Concierge` label; title `PREFECT dbGaP Validation: <token>` | CTDC-2141 (first of the pattern); CTDC-2233 (CMB v6, first under a submission epic) |
| 4 | **DO-INDEX · IndexD Registration Task** | [`indexd-registration-task-template.md`](./templates/indexd-registration-task-template.md) | Loading data: IndexD registration | ✅ Drafted v7 (2026-06-11): external CTDS/DCFS handoff for GUID minting; 4 sections (🎯 Registration Summary · 📦 Submission & Artifacts · 🚦 Registration Workflow · 🧪 Verification); runs in parallel with the load; `Data-Concierge` label; title `Data Indexing: <token>` | CTDC-2060 (AHEP0731 Images-Only); CTDC-2206 (CMB v6) |
| 5 | **DO-LOAD · Data Loading Task** | [`data-loading-task-template.md`](./templates/data-loading-task-template.md) | Loading data: end-to-end load | ✅ Drafted v9 (2026-09-25): 5 sections (🎯 Load Summary · 📦 Submission & Artifacts · 📊 Expected Counts · 🚦 Loading Workflow · ✅ Testing Signoff); Expected Counts is one column filled once from the Release Package and checked at every tier; a dedicated Jenkins job per tier; no label; title `Data Loading: <token>` | AHEP0731 load (CTDC-2063); CTDC-2205 (CMB v6, first v9) |
| 6 | **DO-ZIP · Megazip Creation Task** | [`megazip-creation-task-template.md`](./templates/megazip-creation-task-template.md) | Loading data: megazip (CTDC-created artifact) | ✅ Drafted v3 (2026-09-17): one ticket per study, one megazip per `data_file_type` (`<study>_<data_file_type>.zip`, no program prefix); 4 sections (🎯 Summary · 📦 Artifacts · 🚦 Workflow · ✅ Testing Signoff); GUIDs self-minted (`dg.4DFC/`) and handed to CTDS through the standard DCF Google Drive and CRINTAKE path; title `Create, Index, and Load Megazip files for <token>` | CTDC-2220 (AHOD0831), CTDC-2221 (S0819); CTDC-2207 (CMB v6) |
| retired | ~~**DO-STORY · Data Submission User Story**~~ | [`data-submission-user-story-template.md`](./templates/data-submission-user-story-template.md) | Submission: former parent user story | ⛔ Retired 2026-09-25, replaced by DO-EPIC. Kept for history; existing stories convert in the Jira UI with Move to Epic | CTDC-1666 (historical) |

**Internal Modeling**

| Template | File | Work pattern | Status | Canonical Examples |
|---|---|---|---|---|
| **DO-INTMODEL · Data Model Update Task** | [`data-model-update-template.md`](./templates/data-model-update-template.md) | Modeling data: internally / CTDC-driven | ✅ Drafted v14 (2026-07-20): any SemVer level (additive through breaking); 5 sections (🎯 Modeling Summary · 📚 CDE Request Workbook · 🔍 DM Federal Lead & SME Review · 🪜 Steps to Completion · 🧪 Verification Surfaces); milestone tracker, no specifics, no counts; records in the internal CTDC CDE Request Workbook; the DM Federal Lead & SME Review (caDSR II Help Desk Request Form plus DHDM Jira Issue) gates `prod` | CTDC-2068 (Program curation properties) |

**Sprint rule.** Every ticket created from a DO task template goes into the standing `CTDC Data Related` sprint (board 641, sprint id 8612) at creation. Epics are not placed in sprints.

---

## 🔁 Workflow / SOP Inventory

Recurring per-cadence workflows (per-sprint, per-release, per-meeting, per-quarter) that produce stakeholder artifacts. These are different from ticket templates: they're instructions for *what to do*, not *how to format a ticket*.

| Workflow | File | Cadence | Status | Canonical Examples |
|---|---|---|---|---|
| **Post-Meeting Sprint Recap** | [`post-meeting-sprint-recap-workflow.md`](./workflows/post-meeting-sprint-recap-workflow.md) | Once per sprint, immediately after Review & Retro | ✅ Drafted v1 (2026-05-15) | Sprint 25 recap (2026-04-24), Sprint 26 recap (2026-05-15) |
| **CSP Quarterly Report** | [`csp-quarterly-report-workflow.md`](./workflows/csp-quarterly-report-workflow.md) | Once per quarter, aligned to BACS/FNL reporting period | ✅ Drafted v1 (2026-05-22) | Nov 2025 to Jan 2026 CSP (drafted 2026-03-02); Mar 2026 to May 2026 CSP (drafted 2026-05-22) |
| **Data Submission Process (Internal)** | [`data-submission-workflow.md`](./workflows/data-submission-workflow.md) | Reference: per active data submission | ✅ Revised 2026-09-25 for DO-EPIC (v1 drafted 2026-06-17): internal/technical process order and the per-step Jira artifact and template mapping, built around one submission epic per study submission; follows the Portal states (Dev load, Complete, megazip, QA/Stage/Prod, verify and close); companion to the submitter-facing Active Data Submission SOP (not cross-linked) | CTDC-2110 (CMB v6) |

---

## 📖 Templates Still Inline in SKILL.md (Not Yet Migrated)

These templates still live as sections inside `claude/SKILL.md`. They will be migrated here when they next get materially expanded, or in a focused migration pass.

| Template | SKILL.md Section | Status | Canonical Examples |
|---|---|---|---|
| **User Story** | Section 7a | ✅ Drafted v2 (2026-09-04): 5 sections (Story Summary · User Story · Scope · Acceptance Criteria · Testing Requirements), no ticket keys in the body | CTDC-1691 |
| **Epic (lean v2)** | Section 7b | ✅ Drafted v2 (2026-09-14): card line plus 5 sections (Epic Statement · Description · Scope · Acceptance Criteria · Notes), under 600 words, closes when delivered. Replaced the v1 per-grouping epic templates (7b-1 to 7b-7) | CTDC-1802 |
| **Data Epic Variant** | Section 7b-D | 🚧 Defined 2026-09-25 for the two standing modeling epics only (Submission Data Modeling, Internal Data Modeling): card line plus 7 sections with Upstream & Downstream and a required Risks table, under 800 words; first drafts in progress | n/a yet |
| **Bug Format** | Section 7c | ✅ Lightweight format (always was small) | n/a |

---

## 🧱 Why a Component Library?

Three reasons we're moving toward this pattern:

1. **Components are linkable from tickets and chats.** A Jira ticket can link directly to `claude/templates/design-task-template.md` so any reader (Claude, an engineer, the designer, a reviewer) lands on the canonical template without paging through the 130k-character SKILL.md. A workflow SOP can be linked from a session start so Claude reads only the relevant procedure.

2. **Components evolve independently.** When the Design Task template gets a new section or a new lessons-learned note, the change touches a focused file, not a sprawling monolith. PRs are easier to review.

3. **The SKILL.md stays leaner.** SKILL.md is still the right home for SOPs and reference data: the operational knowledge that's used as a working reference. Discrete components (ticket templates, recurring workflows) belong in this catalog.

---

## ➕ Adding a New Component Here

When migrating an inline SKILL.md section to this folder, or drafting a new component from scratch:

1. Create the file in this folder. Naming conventions:
   - Ticket templates: `<artifact-type>-template.md` (e.g., `bug-template.md`, `data-submission-epic-template.md`).
   - Workflow SOPs: `<cadence>-<artifact-type>-workflow.md` (e.g., `post-meeting-sprint-recap-workflow.md`, `pre-release-tagging-hygiene-workflow.md`, `csp-quarterly-report-workflow.md`).
2. Inside the file, follow the standard structure: header with usage statement, "Why this template/workflow" rationale, section order or step order, standing emoji set, required content rules, writing-and-publishing or run-and-distribute workflow, when-to-expand-vs-trim guidance.
3. Add a row to the appropriate inventory table above (Template Inventory or Workflow / SOP Inventory). Note the lane (software development vs. data management) and, for data management templates, the sub-function and work pattern.
4. A new data operations template gets a new permanent DO code (never a retired one) and a row in SKILL.md's DO tables, crosswalk, and Section 9a. Retiring a template keeps its code and marks it retired.
5. If migrating from SKILL.md, leave a small cross-reference in SKILL.md pointing here (don't delete the section header; replace its body with a pointer).
6. Update `SKILL.md` Section 9a (Template Status Tracker) with the new entry if it's a ticket template.
7. Add a lessons-learned subsection in `SKILL.md` Section 9 if the component's drafting produced any reusable methodology insights. For workflow components, drop a dated lessons-learned file into `claude/lessons-learned/YYYY-MM-DD-<short-name>.md`.

---

## 🧭 How Claude Should Use This Library

When the user asks Claude to draft a ticket or run a recurring workflow:

1. **First, decide which lane the work is in**: software development or data management. If data management, decide which sub-function and work pattern. Use this decision tree:
   - *Is a new study submission (or a new version of a study) starting?* → **Data Submission Epic (DO-EPIC)**; every task below for that submission hangs off it
   - *Has the submission been released and does it need its consent groups reconciled against dbGaP before indexing or loading?* → **dbGaP Validation Task (DO-DBGAP)**
   - *Are the files in this submission still pending IndexD GUID minting via the external CTDS/DCFS team?* → **IndexD Registration Task (DO-INDEX)**
   - *Is new data being loaded into the existing schema?* (new study, new files for an existing study) → **Data Loading Task (DO-LOAD)**
   - *Does the study need one downloadable zip per data file type?* → **Megazip Creation Task (DO-ZIP)**
   - *Is the schema changing because an incoming **study submission** needs new properties / enums / permissible values, with that study's CDE Request Workbook as the spec?* → **Data Modeling for Study Submission (DO-MODEL)**: study-driven; records in the study's workbook (owned by the study's Data Concierge)
   - *Is the schema changing because the **CTDC project itself** decided to change it (the application roadmap or the data team), anywhere from a single additive optional property to a breaking multi-repo refactor?* → **Data Model Update Task (DO-INTMODEL)**: internally/CTDC-driven; records in the internal CTDC workbook (owned by the project, no individual owner)
   - *(Either way, a model change always records in a CDE Request Workbook; the driver just decides which one. There is no "no workbook" path.)*
   - *None of the above?* → software development family
2. **Check if a component exists for that task**: look in this folder and in `SKILL.md` Section 7 (for ticket templates) or Section 12 (for meeting/sprint/quarterly workflows).
3. **If yes, follow the component exactly**: section order, emoji set, content rules, step order, all of it.
4. **If no component exists yet**, fall back to the closest adjacent component and note the gap to the user so it can be drafted.
5. **Point to the canonical component file** in chat replies or Jira comments when relevant, so the next person picking up similar work knows where the convention is documented. Ticket bodies stay lean and carry no such pointers.

---

> *Numbering note: the data-management templates were renumbered to process order on 2026-06-16 and to stable DO codes on 2026-07-23 (crosswalk in the Data management lane inventory above). The dated history below uses the template IDs in effect at the time and is left unchanged as a historical record. On 2026-09-25 the Data Submission Epic template (DO-EPIC) was added and the Data Submission user story (DO-STORY) retired: each study submission is now its own epic, piloted on CTDC-2110 (CMB v6), and the dbGaP validation, IndexD, loading, and megazip tasks became its Epic Link children. The Data Loading Task went to v9 the same day with an Expected Counts table that replaced the user story's Data Details section.*

*This folder was created 2026-05-06 alongside the Design Task template. The Post-Meeting Sprint Recap workflow was added 2026-05-15 from the Sprint 26 Review & Retro session. The Data Loading Task template was added 2026-05-15 and iterated through v4 the same day to settle the scope: data management has two sub-functions (loading and modeling). The Data Model Update Task template was added 2026-05-15 as the first modeling template, designed around the heavyweight case (breaking changes, framework upgrades, multi-repo coordination). On 2026-05-20, the Data Modeling for Study Submission template was added as the common-case modeling template. Through three same-day iterations the template settled on its canonical v3 shape: six sections (Modeling Summary, CDE Request Workbook, Branch & Release Signoff, Verification Surfaces, Open Questions & Risks, Notes). v3 enforces the discipline that the workbook is the term-level source of truth and the ticket exists for milestone tracking only — Term Status Summary, Modeling & Promotion Workflow, Linked Work, and Collaboration sections that appeared in an intermediate v2 draft were removed because they duplicated content that already lives in the workbook, the SOP, Jira's native issue links, or the assignee field. CTDC-2051 (NCI-MATCH Arm Z1D, parent CTDC-1666) and CTDC-1799 (CIMAC-CIDC, parent CTDC-1804) are both in v3 shape and serve as the canonical pair. The CSP Quarterly Report workflow was added 2026-05-22 from the March 2026 – May 2026 CSP drafting session. The drafting session uncovered six lessons captured in `lessons-learned/2026-05-22-csp-quarterly-report.md`: software releases and data releases are strictly separate events; three dates per software release with the public one leading; the Bento Framework does not yet include a DMN (DMN contributions go to the CRDC Data Hub team's package); Monthly Summaries carry program-level context Jira can't see; excluding Test issue types from the Jira pull is essential; and the prior CSP's format is to be matched verbatim rather than reinvented. The IndexD Registration Task template was added 2026-05-26 to formalize the upstream artifact creation work pattern within the loading-data sub-function. The template treats IndexD registration as a coordination ticket for the external CTDS/DCFS handoff (manifest upload to DCF Google Drive → CRINTAKE intake ticket → DCFS confirmation → GUID spot-check), with a mandatory `Relates` link to the parent submission user story, a `Relates` link to the **paired** Data Loading Task (the two run in **parallel** — IndexD does **not** block the load), and a remote link to the CRINTAKE ticket. On 2026-06-01 (v4) the registration/load link was corrected from Blocks to `Relates` and the `Data-Concierge` label convention was set. On 2026-06-04 (v5) the Submission & Artifacts table was restructured to mirror the Data Loading Task — the constant AWS Account ID and AWS S3 Bucket were split into their own rows, Release Package was reduced to the directory name, and Example GUID was renamed Sample GUID — and the Object Files Location and Consent group / ACL rows were dropped as captured in the indexd.tsv manifest itself; CTDC-2060 became the canonical example and was aligned to v5 along with the 11 paired NCTN-NCORP Index tickets (CTDC-2072–2092, even). Later on 2026-06-04 (v6) the task was slimmed to four sections: the Registration Summary was reduced to one sentence (consent codes and on-hold status are visible in Jira statuses and on the parent user story, not restated on the task), the Workflow dropped the Confirmation-and-verification phase (the spot-check now lives wholly in the Verification section), and the Notes section was removed as not task-level; CTDC-2060 and the 11 paired Index tickets were updated to the v6 shape. CTDC-1907 is retained for historical workflow context only. On 2026-06-01, the Data Model Update Task template was reframed to v3: the 7f/7g split was redrawn on the **driver** of the change (internally/CTDC-driven vs. study-driven) rather than on weight (heavyweight vs. lightweight), and the CDE Request Workbook was made universal — every model change records in a workbook, the internal CTDC project workbook for 7f and the study's workbook for 7g, with no "no workbook" path. 7f grew a CDE Request Workbook section (internal workbook, owned by the project with no individual owner), caDSR/ServiceNow coordination, and a formal Data Commons sign-off gate, and now spans the full additive-to-breaking range; CTDC-2068 (Program curation properties) is its canonical pilot. The README decision tree, the Modeling-data sub-function description, and the 7f/7g inventory rows were updated the same day to match. Later the same cycle, 7f was slimmed to v4 (7 sections) after CTDC-2068 was hand-tightened in use: the Affected Code & Loaders, Verification Surfaces, Per-Environment Verification, Collaboration & Handoffs, and Open Questions / Risks sections were removed; Model Change Details was trimmed to milestones (SemVer, current/target version, backward compatibility) plus the three-files schema-artifact checklist, with all term-level detail living in the workbook; per-environment verification was folded into the Promotion Workflow steps and signed off in Testing Signoff; and the workbook table dropped its Owner and caDSR-status rows (the Source-of-Truth / Out-of-Scope rows were kept). Minutes later, v5 removed the Linked Work section as well — related tickets are carried by native Jira issue links — leaving 6 sections. On 2026-06-02, 7f v6 corrected the Promotion Workflow: it had carried the data-*loading* promotion ladder (Jenkins lower/upper-tier pipelines, per-environment loads), but model contribution is a git feature-branch → PR into `develop` → PR `develop` → `prod` → tag and GitHub Release flow per `SOP_CTDC_Data_Model_Contribution.md`, with no loading pipeline; the same fix was applied to the canonical pilot CTDC-2068. 7g's Branch & Release Signoff was already the contribution flow and needed no change. 7f v7 removed the Testing Signoff section — per-environment progress (Dev/QA/Stage/Prod) is tracked by the ticket's native Jira status, transitions, and assignee, so a signoff table in the description was redundant; the close trigger moved into the Promotion Workflow, and CTDC-2068 was updated to match. 7f v8 (2026-06-02) brought the template fully into lockstep with CTDC-2068: counts were removed entirely (the number of properties / enums / PVs is term-level detail for the CDE Workbook, enforced by a new No-counts rule and an extended antipattern 3), and the caDSR/ServiceNow coordination was promoted out of the CDE Request Workbook section into its own **🔍 DM Federal Lead & Subject Matter Expertise Review** section (Section 3) — the DC formal sign-off concept was replaced by the **DHDM Jira Issue** created by the DH Coordinator and linked to the Submission Request ticket (owners: DM Fed Lead, DH Lead), and the workbook table's Out-of-Scope row was dropped, leaving 6 sections. Finally, on 2026-06-02, 7f (v9) and 7g (v5) were converged onto one identical seven-section shape — 🎯 Modeling Summary · 📚 CDE Request Workbook · 🔍 DM Federal Lead & SME Review · 🧬 Model Change Details · ✅ Branch & Release Signoff · 🧪 Verification Surfaces · 📝 Notes — differing only by context (study vs. internal workbook, Submission Portal vs. Prod + Data Hub sync close trigger). The Promotion Workflow section was dropped from 7f as a duplication of the Data Model Contribution SOP; the ✅ Branch & Release Signoff milestone audit trail (a dated record of when each git/release milestone landed, which the single Jira status field can't carry) was adopted for both, reversing 7f v7's no-signoff-table stance; 7g gained the 🧬 Model Change Details section so study-driven changes capture their assessed SemVer impact; and Open Questions & Risks was dropped from both as not task-level (open items live in Jira comments). Two governing principles were sharpened across both: no specifics of the modeling change belong in the ticket (no per-property / PV / enum / relationship / CDE-code listing and no counts — all of that lives in the CDE Request Workbook), and a modeling change may be any SemVer level including breaking, with any downstream re-load of existing data tracked as a separate Data Loading Task (7e). The canonical pilots were brought to the converged shape (CTDC-2068 and CTDC-2051); CTDC-1799 is pending the same retrofit.*
