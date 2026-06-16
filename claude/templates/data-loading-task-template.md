### 7e. 📦 Data Loading Task Template (v8)

> **Use this template for every CTDC data management task that loads a CRDC submission into CTDC (either a brand-new study or new data added to an existing study) and promotes it through Dev → QA → Stage → Prod.** Canonical examples: the CMB megazip metadata load (CTDC-1753 lineage) and the AHEP0731 load (CTDC-2063). This template covers the **loading-data** sub-function of the team's data management work. It is **not** for changes to the data model itself; schema changes use the **Data Modeling for Study Submission** template (Section 7g) or the **Data Model Update Task** template (Section 7f). It is also **not** for the paired IndexD registration that mints the file GUIDs this load consumes; that uses the **IndexD Registration Task** template (Section 7h). See "When NOT to use this template" at the end.

**Why this template**

The CTDC team has two primary functions: **software development** (the React frontend, Java backend, microservices, and infrastructure; verified against application *behavior*) and **data management** (managing CRDC submissions, modeling the shape of CTDC's data, and loading data into CTDC's databases; verified against application *contents* and schema state).

Data management has two sub-functions:

- **Loading data**: taking a CRDC submission's *contents* (study metadata, files, IndexD entries) into CTDC's databases. Tracked with **this** template (Section 7e) and its paired sibling, the IndexD Registration Task template (Section 7h).
- **Modeling data**: changing the *shape* of what CTDC's databases can hold. Tracked with the Data Modeling for Study Submission template (Section 7g) or the Data Model Update Task template (Section 7f).

This template owns only the loading-data sub-function, tuned for loading into a stable schema with unchanged application code. If code is changing, it's the wrong template. If the schema is changing, that's a modeling Task that must land first.

**Tasks execute; user stories deliberate.** A Task carries only what the assignee needs to execute the load. Open questions, risks, ownership directories, and link inventories belong on the parent submission user story, not on the Task. Jira's native Links panel (Epic Link, Relates, Blocks, remote links) carries every relationship, including the paired IndexD Registration Task, so the description never restates them.

**Pipeline & store anatomy (read once before drafting)**

- **Source artifacts**: the Release Package (metadata loading TSVs plus the indexd.tsv manifest) lives in the CTDC metadata S3 bucket (`nci-cbiit-clinicaltrialdatacommons-metadata`), in a per-release directory created when the study is released from the CRDC Submission Portal.
- **Loader**: `CBIIT/crdc-ctdc-dataloader` (branch `master`), run by Jenkins. Stable for a routine load.
- **Loading pipeline**: **Jenkins, with a dedicated data-loading job for each of the four tiers (Dev, QA, Stage, Prod).** There is no lower/upper-tier grouping for data loading: that two-tier split belongs to the `ctdc-model` contribution flow, not here. Each job parses the metadata loading TSVs, writes to Memgraph, and triggers the OpenSearch reindex.
- **Graph database**: **Memgraph** (the canonical metadata store; replaces the historical Neo4j).
- **Search index**: **OpenSearch** (the frontend reads from here; every load is followed by a reindex from Memgraph).

**Section order (4 sections, exactly this sequence)**

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

3. `### 🚦 **Loading Workflow**`: Numbered list of the end-to-end promotion. **Data loading runs a dedicated Jenkins job per tier**: one each for Dev, QA, Stage, and Prod. There is no lower/upper grouping (that two-tier split is the `ctdc-model` contribution flow, not data loading).

   **Dev**
   1. Run the dedicated Jenkins Dev data-loading job.
   2. Verify the load and OpenSearch reindex completed and the new data renders in the application. Record in Testing Signoff.

   **QA**
   3. Run the dedicated Jenkins QA data-loading job.
   4. Assign for QA testing. Tester verifies data renders on the expected pages, file downloads resolve via the IndexD signed-URL flow, and there are no regressions on existing studies. Tester initials Testing Signoff on completion.

   **Stage**
   5. Run the dedicated Jenkins Stage data-loading job.
   6. Assign for Stage testing and signoff: same checks as QA, plus production-parity sanity checks. Tester initials Testing Signoff on completion.

   **Prod**
   7. Run the dedicated Jenkins Prod data-loading job.
   8. Assign for Prod verification and signoff; verify in production with the live application URL. Tester initials Testing Signoff on completion. **This is the trigger to close the ticket.**

4. `### ✅ **Testing Signoff**`: The completion record. Tester fills in date and initials per environment as work progresses. **Prod signoff is the trigger to transition the ticket to Closed.**

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

**Standing emoji set (4 entries)**

| Section | Emoji |
|---|---|
| Load Summary | 🎯 *(shared with IndexD Registration Task)* |
| Submission & Artifacts | 📦 *(shared with IndexD Registration Task)* |
| Loading Workflow | 🚦 *(shared with IndexD Registration Task)* |
| Testing Signoff | ✅ *(unique to data loading task)* |

**Required content rules**

- **Scope is loading-data work only.** Schema/model changes use a modeling template (7f/7g). IndexD registration uses the IndexD Registration Task template (7h). Software development uses the software development family. See "When NOT to use this template."
- **No Acceptance Criteria, Open Questions / Risks, Verification Surfaces, Per-Environment Verification, or Notes sections.** Data loading is operational SOP work; the completion bar is the Testing Signoff table. AC and risks belong on the parent submission user story.
- **One Task per end-to-end load**: Dev through Prod, not one ticket per environment. The Testing Signoff table is the single source of truth for where the load is in the pipeline.
- **A dedicated Jenkins data-loading job per tier.** Run the correct per-tier job (Dev, QA, Stage, Prod). The lower/upper-tiers split is a `ctdc-model` contribution concept and does **not** apply to data loading.
- **Issue type is Task.** Do not use Story or Subtask.
- **Title convention:** `Data Loading: <Study Name vN>` — reuse the exact `<Study Name vN>` token from the parent Data Submission user story title (Section 7i) verbatim.
- **Parent Epic field set via `customfield_12350`.** Default parent CTDC-1664 (CTDC Data Integration) unless a release-specific epic exists.
- **`Relates` link to the parent submission user story is mandatory** (set via `jira_create_issue_link` after creation). The user story carries study identity and is the home for open questions / risks.
- **`Relates` link to the study-specific Data Hub tracking ticket (DHDM-XXX) when one exists.**
- **`Relates` link to the paired IndexD Registration Task** when one exists: the two run in parallel; do **not** use `Blocks`. The non-blocking relationship is conveyed by the link, not restated in the body.
- **The Data Loading task carries no label**: the load is performed by engineering; the `Data-Concierge` label belongs to the paired IndexD Registration (Index) task.
- **Leave the ticket Unassigned at creation** per standing convention. Data loading tasks do not require the Developer field.
- **Submission & Artifacts table is mandatory and complete**: all four rows present. The two constant rows (AWS Account ID, AWS S3 Bucket) are hardcoded; use PLACEHOLDER for the Submission ID or the Release Package directory when pending upstream.
- **Rendering-safe authoring**: `### **Title**` headers (round-trip to `h3.`); italic-label bullets as `* *Label*: content`, not `- **Label:**`; Jira-wiki `||header||` tables. Push Markdown via `jira_update_issue` (two-step create-then-update) and confirm the render with a UI screenshot; wiki source is not a reliable preview.

**Writing-and-publishing workflow**

1. Confirm the Release Package exists in `nci-cbiit-clinicaltrialdatacommons-metadata`. The paired IndexD registration runs in parallel and need not be complete. Surface any open questions on the parent submission user story, not the Task.
2. Confirm this is a data load, not a model update or software development. If the schema is changing, a modeling Task (7f/7g) lands first.
3. Identify the parent submission user story and the paired IndexD Registration Task; add both via native links after creation (`Relates`, never `Blocks`).
4. Create via `jira_create_issue` with `issue_type = "Task"`, a placeholder description, and the parent epic via `customfield_12350`. Add no label. Leave Unassigned.
5. Push the full body via `jira_update_issue` (Markdown in; converts server-side).
6. Add the `Relates` links (parent user story, DHDM tracker, paired IndexD task).
7. Verify the rendered description with a UI screenshot.
8. As each environment completes, the tester adds date + initials to Testing Signoff.
9. **Prod signoff is the close trigger**: once the Prod row is filled in, transition to Closed.

**When NOT to use this template**

- **IndexD registration (minting GUIDs)** → IndexD Registration Task template (7h), the parallel sibling.
- **Data modeling** → Data Modeling for Study Submission (7g) for study-driven changes, or Data Model Update Task (7f) for internally-driven changes.
- **Software development** → software development template family.
- **CRDC platform changes** (Fence, IndexD, Submission Portal upgrades) → owned by CRDC platform teams; out of CTDC scope.
- **Pure file-creation tickets** (making a megazip, generating a metadata loading file) → upstream artifact-creation tasks that feed this load; tracked separately.

If a submission needs schema changes before it can load, that's a modeling Task (7f/7g) the load is blocked on; if it needs IndexD registration, that's the paired IndexD Registration Task (7h) running in parallel. Both are separate tickets carried by native Jira links.

**Changelog**

- **v8 (2026-06-11)**: Replaced em-dash separators throughout with colons for label lead-ins and semicolons/commas for clause joins; the rendering-safe bullet convention is now `* *Label*: content` (italic label, colon separator). No structural or content changes.
- **v7 (2026-06-03)**: Slimmed to four sections (Load Summary · Submission & Artifacts · Loading Workflow · Testing Signoff). Removed Verification Surfaces, Per-Environment Verification, and Notes; dropped the Pre-load workflow phase. **Corrected the long-standing two-tier-pipeline error:** data loading runs a dedicated Jenkins job per tier (Dev/QA/Stage/Prod); the lower/upper grouping is the `ctdc-model` contribution flow, not data loading. Trimmed Submission & Artifacts to four rows: split the constant AWS Account ID and S3 Bucket into their own rows, reduced Release Package to the directory name only, and removed Object Files Location (handled by GUID minting), Study (acronym + version) (in the title), Metadata loading file (many, inside the Release Package), and Target model version (model work is a modeling Task). Load Summary no longer carries a study version, surface enumeration, or model mention.
- **v6 (2026-06-01)**: Paired IndexD task linked with `Relates` and run in parallel (not blocking); artifacts row `Release Package` (was `Release Package Location`); Data Loading task carries no label while the paired Index task carries `Data-Concierge`.
- **v5 (2026-06-01)**: Removed Linked Work, Collaboration & Handoffs, and Open Questions / Risks; adopted "tasks execute, user stories deliberate."
- **v4 and earlier**: One ticket per end-to-end load; explicit payload and per-environment signoff. Superseded.
