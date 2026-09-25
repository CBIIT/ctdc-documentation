### DO-LOAD. 📦 Data Loading Task Template (v9)

> **Parent change (2026-09-25).** Each study submission is now tracked as a **submission epic** (Section DO-EPIC, `data-submission-epic-template.md`), which replaces the Data Submission user story (DO-STORY, retired) and the default CTDC Data Integration epic (CTDC-1664). Wherever this template says "parent submission user story" or "parent user story", read **submission epic**: this task is a child of that epic via the Epic Link (`customfield_12350`), not a `Relates` link, and the epic is the home for study identity, open questions, and risks. The title token is `<Program Short Name> <Study Short Name> <version>`, omitting parts that do not apply (for example `CMB v6`, `NCTN AHOD0831`).


> **Use this template for every CTDC data management task that loads a CRDC submission into CTDC (either a brand-new study or new data added to an existing study) and promotes it through Dev → QA → Stage → Prod.** Canonical example: the AHEP0731 load (CTDC-2063). This template covers the **loading-data** sub-function of the team's data management work. It is **not** for changes to the data model itself; schema changes use the **Data Modeling for Study Submission** template (Section DO-MODEL) or the **Data Model Update Task** template (Section DO-INTMODEL). It is also **not** for the paired IndexD registration that mints the file GUIDs this load consumes; that uses the **IndexD Registration Task** template (Section DO-INDEX). See "When NOT to use this template" at the end.

**Why this template**

The CTDC team has two primary functions: **software development** (the React frontend, Java backend, microservices, and infrastructure; verified against application *behavior*) and **data management** (managing CRDC submissions, modeling the shape of CTDC's data, and loading data into CTDC's databases; verified against application *contents* and schema state).

Data management has two sub-functions:

- **Loading data**: taking a CRDC submission's *contents* (study metadata, files, IndexD entries) into CTDC's databases. Tracked with **this** template (Section DO-LOAD) and its paired sibling, the IndexD Registration Task template (Section DO-INDEX).
- **Modeling data**: changing the *shape* of what CTDC's databases can hold. Tracked with the Data Modeling for Study Submission template (Section DO-MODEL) or the Data Model Update Task template (Section DO-INTMODEL).

This template owns only the loading-data sub-function, tuned for loading into a stable schema with unchanged application code. If code is changing, it's the wrong template. If the schema is changing, that's a modeling Task that must land first.

**Tasks execute; user stories deliberate.** A Task carries only what the assignee needs to execute the load. Open questions, risks, ownership directories, and link inventories belong on the parent submission user story, not on the Task. Jira's native Links panel (Epic Link, Relates, Blocks, remote links) carries every relationship, including the paired IndexD Registration Task, so the description never restates them.

**Pipeline & store anatomy (read once before drafting)**

- **Source artifacts**: the Release Package (metadata loading TSVs plus the indexd.tsv manifest) lives in the CTDC metadata S3 bucket (`nci-cbiit-clinicaltrialdatacommons-metadata`), in a per-release directory created when the study is released from the CRDC Submission Portal.
- **Loader**: `CBIIT/crdc-ctdc-dataloader` (branch `master`), run by Jenkins. Stable for a routine load.
- **Loading pipeline**: **Jenkins, with a dedicated data-loading job for each of the four tiers (Dev, QA, Stage, Prod).** There is no lower/upper-tier grouping for data loading: that two-tier split belongs to the `ctdc-model` contribution flow, not here. Each job parses the metadata loading TSVs, writes to Memgraph, and triggers the OpenSearch reindex.
- **Graph database**: **Memgraph** (the canonical metadata store; replaces the historical Neo4j).
- **Search index**: **OpenSearch** (the frontend reads from here; every load is followed by a reindex from Memgraph).

**Section order (5 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections.

1. `### 🎯 **Load Summary**`: One to two sentences: what's being loaded (the study or release) and whether it's a brand-new study or an addition to an existing study. Do **not** add a study version (it's inferable from the study metadata), enumerate the application surfaces or nodes the load lights up (that's dictated by the Release Package), or mention the data model (model work is tracked on a modeling Task). The paired IndexD relationship is conveyed by the Jira link, not restated here. Example: *"Load the Cancer Moonshot Biobank (CMB) release into CTDC across all four environments (Dev → QA → Stage → Prod). This adds new data to the existing CMB study."*

2. `### 📦 **Submission & Artifacts**`: Required. A four-row table. The AWS Account ID and S3 Bucket are constant for CTDC; only the CRDC Submission ID and the Release Package directory vary per load. Study identity lives on the parent submission user story via the native Links panel, not in this table.

   | Field | Value | Notes |
   |---|---|---|
   | CRDC Submission ID | *(Submission Portal ID)* | Issued by the CRDC Submission Portal; one per submission. Multiple allowed if a load consolidates more than one. |
   | AWS Account ID | `101183076466` | Constant for CTDC: the CTDC data commons AWS account. |
   | AWS S3 Bucket | `nci-cbiit-clinicaltrialdatacommons-metadata` | Constant for CTDC: the metadata bucket that holds every release package. |
   | Release Package | *(directory name, e.g., `2026-05-26T19:37:39-59eb273a-a098-463c-af44-b955131d2098`)* | Directory name only, within the AWS S3 Bucket above. Created when the study is released from the CRDC Submission Portal, a placeholder until release. Contains the metadata loading TSVs and the indexd.tsv manifest. |

   **Naming discipline**: Release Package holds only the directory name, the one variable part of the address; the constant account and bucket are their own rows above. The directory doesn't exist until the study is released, so it stays a placeholder until then.

3. `### 📊 **Expected Counts**`: Added in v9. One lead line, then a one-column table filled **once** from the Release Package when the submission is released. Testers check the Explore dashboard and Studies page against it at every tier. This is where the aggregate counts that used to sit on the Data Submission user story live now.

   | Metric | Expected |
   |---|---|
   | Participants | |
   | Diagnoses | |
   | Therapies | |
   | Biospecimens | |
   | Participant Files | |
   | Study-Level Files | |
   | Data Volume | |

   Lead line: *"Filled from the Release Package when the submission is released. Testers check these against the Explore dashboard and Studies page at every tier."* This is not the per-environment count table removed in v7: it is written once, and the per-tier check is a phrase in the existing workflow steps, recorded through the existing Testing Signoff initials.

4. `### 🚦 **Loading Workflow**`: Numbered list of the end-to-end promotion. **Data loading runs a dedicated Jenkins job per tier**: one each for Dev, QA, Stage, and Prod. There is no lower/upper grouping (that two-tier split is the `ctdc-model` contribution flow, not data loading).

   **Dev**
   1. Run the dedicated Jenkins Dev data-loading job.
   2. Verify the load and OpenSearch reindex completed, the new data renders in the application, and the counts match Expected Counts. Record in Testing Signoff.

   **QA**
   3. Run the dedicated Jenkins QA data-loading job.
   4. Assign for QA testing. Tester verifies data renders on the expected pages, counts match Expected Counts, file downloads resolve via the IndexD signed-URL flow, and there are no regressions on existing studies. Tester initials Testing Signoff on completion.

   **Stage**
   5. Run the dedicated Jenkins Stage data-loading job.
   6. Assign for Stage testing and signoff: same checks as QA, plus production-parity sanity checks. Tester initials Testing Signoff on completion.

   **Prod**
   7. Run the dedicated Jenkins Prod data-loading job.
   8. Assign for Prod verification and signoff; verify in production with the live application URL, including counts against Expected Counts. Tester initials Testing Signoff on completion. **This is the trigger to close the ticket.**

5. `### ✅ **Testing Signoff**`: The completion record. Tester fills in date and initials per environment as work progresses. **Prod signoff is the trigger to transition the ticket to Closed.**

   | Environment | Testing Completion Date | Tester Initials |
   |---|---|---|
   | Dev | | |
   | QA | | |
   | Stage | | |
   | Prod | | |

**Sections removed in v7**

- ❌ **🧪 Verification Surfaces**: the per-surface checklist was mostly redundant with the workflow's per-tier verification instruction.
- ❌ **📊 Per-Environment Verification**: the count table was overkill; Testing Signoff is the straightforward per-environment record.
- ❌ **📝 Notes**: terminology and reminders are not task-level; keep tasks slim.
- ❌ **Pre-load workflow phase**: its checks (Release Package present, model deployed, object files present) are upstream work tracked on other tickets and inferable from the artifacts table.
- (Carried over from v5) **🔗 Linked Work**, **🤝 Collaboration & Handoffs**, and **🔍 Open Questions / Risks** remain out: the native Links panel and the parent user story carry these.

**Standing emoji set (5 entries)**

| Section | Emoji |
|---|---|
| Load Summary | 🎯 *(shared with IndexD Registration Task)* |
| Submission & Artifacts | 📦 *(shared with IndexD Registration Task)* |
| Loading Workflow | 🚦 *(shared with IndexD Registration Task)* |
| Expected Counts | 📊 *(unique to data loading task)* |
| Testing Signoff | ✅ *(unique to data loading task)* |

**Required content rules**

- **Scope is loading-data work only.** Schema/model changes use a modeling template (DO-INTMODEL/DO-MODEL). IndexD registration uses the IndexD Registration Task template (DO-INDEX). Software development uses the software development family. See "When NOT to use this template."
- **No Acceptance Criteria, Open Questions / Risks, Verification Surfaces, Per-Environment Verification, or Notes sections.** Data loading is operational SOP work; the completion bar is the Testing Signoff table. AC and risks belong on the parent submission user story.
- **One Task per end-to-end load**: Dev through Prod, not one ticket per environment. The Testing Signoff table is the single source of truth for where the load is in the pipeline.
- **A dedicated Jenkins data-loading job per tier.** Run the correct per-tier job (Dev, QA, Stage, Prod). The lower/upper-tiers split is a `ctdc-model` contribution concept and does **not** apply to data loading.
- **Issue type is Task.** Do not use Story or Subtask.
- **Title convention:** `Data Loading: <Program Short Name> <Study Short Name> <version>`: reuse the exact token from the submission epic title (Section DO-EPIC) verbatim, for example `Data Loading: CMB v6`.
- **Parent Epic field set via `customfield_12350`** to the study's submission epic (Section DO-EPIC). CTDC-1664 is no longer the default.
- **Expected Counts is filled once from the Release Package** (one column); testers check it at every tier and record the result through Testing Signoff. Do not add a per-environment count table.
- **`Relates` link to the study-specific Data Hub tracking ticket (DHDM-XXX) when one exists.**
- **`Relates` link to the paired IndexD Registration Task** when one exists: the two run in parallel; do **not** use `Blocks`. The non-blocking relationship is conveyed by the link, not restated in the body.
- **The Data Loading task carries no label**: the load is performed by engineering; the `Data-Concierge` label belongs to the paired IndexD Registration (Index) task.
- **Leave the ticket Unassigned at creation** per standing convention. Data loading tasks do not require the Developer field.
- **Sprint: always add the ticket to the standing `CTDC Data Related` sprint at creation** (board 641, sprint id 8612; a permanent future-state sprint that holds all data-operations work). Never leave it in the backlog and never place it in a numbered development sprint; pass the new key to `jira_add_issues_to_sprint` right after creation.
- **Submission & Artifacts table is mandatory and complete**: all four rows present. The two constant rows (AWS Account ID, AWS S3 Bucket) are hardcoded; use PLACEHOLDER for the Submission ID or the Release Package directory when pending upstream.
- **Rendering-safe authoring**: `### **Title**` headers (round-trip to `h3.`); italic-label bullets as `* *Label*: content`, not `- **Label:**`; Jira-wiki `||header||` tables. Push Markdown via `jira_update_issue` (two-step create-then-update) and confirm the render with a UI screenshot; wiki source is not a reliable preview.

**Writing-and-publishing workflow**

1. Confirm the Release Package exists in `nci-cbiit-clinicaltrialdatacommons-metadata`. The paired IndexD registration runs in parallel and need not be complete. Surface any open questions on the parent submission user story, not the Task.
2. Confirm this is a data load, not a model update or software development. If the schema is changing, a modeling Task (DO-INTMODEL/DO-MODEL) lands first.
3. Identify the submission epic (set as the Epic Link at creation) and the paired IndexD Registration Task (add via a native `Relates` link after creation, never `Blocks`).
4. Create via `jira_create_issue` with `issue_type = "Task"`, a placeholder description, and the parent epic via `customfield_12350`. Add no label. Leave Unassigned.
5. Push the full body via `jira_update_issue` (Markdown in; converts server-side).
6. Add the ticket to the `CTDC Data Related` sprint (id 8612) via `jira_add_issues_to_sprint`.
7. Add the `Relates` links (DHDM tracker, paired IndexD task, and the other tasks for the study).
8. Verify the rendered description with a UI screenshot.
9. As each environment completes, the tester adds date + initials to Testing Signoff.
10. **Prod signoff is the close trigger**: once the Prod row is filled in, transition to Closed.

**When NOT to use this template**

- **IndexD registration (minting GUIDs)** → IndexD Registration Task template (DO-INDEX), the parallel sibling.
- **Data modeling** → Data Modeling for Study Submission (DO-MODEL) for study-driven changes, or Data Model Update Task (DO-INTMODEL) for internally-driven changes.
- **Software development** → software development template family.
- **CRDC platform changes** (Fence, IndexD, Submission Portal upgrades) → owned by CRDC platform teams; out of CTDC scope.
- **Pure file-creation tickets** (making a megazip, generating a metadata loading file) → upstream artifact-creation tasks that feed this load; tracked separately.

If a submission needs schema changes before it can load, that's a modeling Task (DO-INTMODEL/DO-MODEL) the load is blocked on; if it needs IndexD registration, that's the paired IndexD Registration Task (DO-INDEX) running in parallel. Both are separate tickets carried by native Jira links.

**Changelog**

- **v9 (2026-09-25)**: Added the 📊 **Expected Counts** section (5 sections), holding the aggregate counts that moved off the submission epic; the Dev, QA, and Prod verification steps check counts against it. Parent changed to the submission epic (DO-EPIC).
- **2026-09-17 sprint rule** (no version bump): every ticket from this template goes into the standing `CTDC Data Related` sprint (board 641, id 8612) at creation, per the TPM on 2026-09-17. Applies to the whole DO-* family.
- **v8 (2026-06-11)**: Replaced em-dash separators throughout with colons for label lead-ins and semicolons/commas for clause joins; the rendering-safe bullet convention is now `* *Label*: content` (italic label, colon separator). No structural or content changes.
- **v7 (2026-06-03)**: Slimmed to four sections (Load Summary · Submission & Artifacts · Loading Workflow · Testing Signoff). Removed Verification Surfaces, Per-Environment Verification, and Notes; dropped the Pre-load workflow phase. **Corrected the long-standing two-tier-pipeline error:** data loading runs a dedicated Jenkins job per tier (Dev/QA/Stage/Prod); the lower/upper grouping is the `ctdc-model` contribution flow, not data loading. Trimmed Submission & Artifacts to four rows: split the constant AWS Account ID and S3 Bucket into their own rows, reduced Release Package to the directory name only, and removed Object Files Location (handled by GUID minting), Study (acronym + version) (in the title), Metadata loading file (many, inside the Release Package), and Target model version (model work is a modeling Task). Load Summary no longer carries a study version, surface enumeration, or model mention.
- **v6 (2026-06-01)**: Paired IndexD task linked with `Relates` and run in parallel (not blocking); artifacts row `Release Package` (was `Release Package Location`); Data Loading task carries no label while the paired Index task carries `Data-Concierge`.
- **v5 (2026-06-01)**: Removed Linked Work, Collaboration & Handoffs, and Open Questions / Risks; adopted "tasks execute, user stories deliberate."
- **v4 and earlier**: One ticket per end-to-end load; explicit payload and per-environment signoff. Superseded.
