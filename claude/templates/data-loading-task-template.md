### DO-LOAD. 📦 Data Loading Task Template (v10)

> **Use this template for every CTDC data management task that loads a CRDC submission into CTDC (either a new study or a study update to an existing study) and promotes it through Dev → QA → Stage → Prod.** Canonical example: the AHEP0731 load (CTDC-2063). This template covers the **loading-data** sub-function of the team's data management work. It is **not** for changes to the data model itself; schema changes use the **Data Modeling for Study Submission** template (Section DO-MODEL) or the **Data Model Update Task** template (Section DO-INTMODEL). It is also **not** for the paired IndexD registration that registers the file GUIDs this load consumes; that uses the **IndexD Registration Task** template (Section DO-INDEX). Kept in lockstep with ICDC's Data Loading Task template (v2); the differences are the platform specifics in the anatomy below. See "When NOT to use this template" at the end.

**Why this template**

The CTDC team has two primary functions: **software development** (the React frontend, Java backend, microservices, and infrastructure; verified against application *behavior*) and **data management** (managing CRDC submissions, modeling the shape of CTDC's data, and loading data into CTDC's databases; verified against application *contents* and schema state).

Data management has two sub-functions:

- **Loading data**: taking a CRDC submission's *contents* (study metadata, files, IndexD entries) into CTDC's databases. Tracked with **this** template (Section DO-LOAD) and its paired sibling, the IndexD Registration Task template (Section DO-INDEX).
- **Modeling data**: changing the *shape* of what CTDC's databases can hold. Tracked with the Data Modeling for Study Submission template (Section DO-MODEL) or the Data Model Update Task template (Section DO-INTMODEL).

This template owns only the loading-data sub-function, tuned for loading into a stable schema with unchanged application code. If code is changing, it's the wrong template. If the schema is changing, that's a modeling Task that must land first.

**Tasks execute; the submission epic deliberates.** A Task carries only what the assignee needs to execute the load. Open questions, risks, ownership directories, and link inventories belong on the submission epic (Section DO-EPIC), not on the Task. Jira's native Links panel (Epic Link, Relates, Blocks, remote links) carries every relationship, including the paired IndexD Registration Task, so the description never restates them.

**Pipeline & store anatomy (read once before drafting)**

- **Source artifacts**: each CRDC submission produces its own **Release Package** (metadata loading TSVs plus the indexd.tsv manifest) in the CTDC metadata S3 bucket (`nci-cbiit-clinicaltrialdatacommons-metadata-nonprod`), in a per-release directory created when the study is released from the CRDC Submission Portal. One study load can span several submissions, so one load can consume several Release Packages.
- **Loading pipeline**: **Prefect**, run **once** per load; there is no Jenkins step for data loading.
- **Loader**: `CBIIT/crdc-ctdc-dataloader`, run by the Prefect pipeline.
- **Stores**: the pipeline loads the study through a **local Neo4j** into a **single Memgraph** (the canonical metadata store). Each tier's **OpenSearch** instance, which the frontend reads from, is then populated by **backup and restore**.
- **Contrast with ICDC**: ICDC writes to Neo4j and reindexes OpenSearch per environment; it has no Memgraph.

**Section order (4 sections, exactly this sequence)**

Each section header is a Jira wiki `h3.` heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections.

1. `h3. 🎯 *Load Summary*`: One to two sentences: what's being loaded (the study or release) and whether it's a new study or a study update to an existing study. Do **not** add a study version (it's in the title), counts (they are in the Release Packages), enumerate the application surfaces or nodes the load lights up, or mention the data model. The paired IndexD relationship is conveyed by the Jira link, not restated here. Example: *"Load the Cancer Moonshot Biobank (CMB) release into CTDC across all four environments (Dev → QA → Stage → Prod). This is a study update to the existing CMB study."*

2. `h3. 📦 *Submission & Artifacts*`: Required. Two bare-value constant bullets, then a table with **one row per CRDC submission** the load consumes. No intro sentence, no explanatory text on the constants. Study identity lives on the submission epic via the native Links panel, not here.

   ```
   * *AWS Account ID*: {{265135454114}}
   * *Release Package bucket*: {{nci-cbiit-clinicaltrialdatacommons-metadata-nonprod}}

   ||Submission||CRDC Submission ID||Release Package||
   |<Portal submission name>|<submission-id>|[<timestamp>-<submission-id>/|<S3 console URL>]|
   |<next submission>|<submission-id>|[<timestamp>-<submission-id>/|<S3 console URL>]|
   ```

   **Columns:**
   - *Submission*: the submission's name in the CRDC Submission Portal. Use the same name as the paired IndexD Registration Task's table so the two tickets line up row for row.
   - *CRDC Submission ID*: issued by the CRDC Submission Portal. Plain text, no `{{ }}`.
   - *Release Package*: the directory name within the Release Package bucket, rendered as a clickable S3-console link. It holds that submission's metadata loading TSVs and indexd.tsv manifest. Until the study is released from the CRDC Submission Portal the directory does not exist, so use PLACEHOLDER.

   **Clickable Release Package links**: render each as a Jira wiki link `[<timestamp>-<submission-id>/|<url>]`. A wiki link inside a table cell renders correctly on this instance. URL format, with the `prefix=` value being the same directory URL-encoded (colons as `%3A`):

   ```
   https://us-east-1.console.aws.amazon.com/s3/buckets/nci-cbiit-clinicaltrialdatacommons-metadata-nonprod?region=us-east-1&prefix=<URL-ENCODED-DIR>/&showversions=false
   ```

   **Every submission gets a row**, including metadata-only submissions with no object files: the load still consumes their Release Package. A submission that arrives after the ticket is written is added as a new row, never as narrative elsewhere in the body.

   **Why one row per submission** (the v10 change): one load can span several CRDC submissions, each with its own Release Package. The v9 four-row field/value table allowed several Submission IDs in one cell and relied on matching positions against the Release Package cell. On ICDC, which used the same table, that drifted: a late fourth submission never made it into the lists (ICDC-4195). A row per submission keeps each Submission ID beside its own Release Package and makes a late arrival a single new row. This mirrors the IndexD Registration Task table (DO-INDEX v8), so the paired tickets read the same way.

3. `h3. 🚦 *Loading Workflow*`: Numbered list of the end-to-end promotion, grouped with a bold label. The Prefect data loading pipeline runs **once**; each tier is then populated by an OpenSearch backup and restore. Steps state the outcome to reach, not how the pipeline gets there.

   **Load**
   1. Run the Prefect data loading pipeline (once, for all tiers).

   **Dev**
   2. Restore the OpenSearch backup to Dev.
   3. Verify the new data renders in the application and the counts match the Release Packages. Record in Testing Signoff.

   **QA**
   4. Restore the OpenSearch backup to QA.
   5. QA verification: data renders on the expected pages, counts match the Release Packages, file downloads resolve via the IndexD signed-URL flow, and there are no regressions on existing studies. Tester initials Testing Signoff on completion.

   **Stage**
   6. Restore the OpenSearch backup to Stage.
   7. Stage verification: same checks as QA, plus production-parity sanity checks. Tester initials Testing Signoff on completion.

   **Prod**
   8. Restore the OpenSearch backup to Prod.
   9. Prod verification with the live application URL, same checks as QA. Tester initials Testing Signoff on completion. **This is the trigger to close the ticket.**

4. `h3. ✅ *Testing Signoff*`: The completion record. Tester fills in date and initials per environment as work progresses. **Prod signoff is the trigger to transition the ticket to Closed.**

   | Environment | Testing Completion Date | Tester Initials |
   |---|---|---|
   | Dev | | |
   | QA | | |
   | Stage | | |
   | Prod | | |

**Not in the description**: pipeline run records (Prefect flow-run links, run parameters) and counts. Run records go in a plain-text ticket comment; counts are in the Release Packages.

**Sections removed**

- ❌ **📊 Expected Counts** (added in v9, removed in v10): the counts are in the Release Packages, so a copy on the ticket adds nothing and can go stale. Not used in ICDC either.
- ❌ **🧪 Verification Surfaces**, **📊 Per-Environment Verification**, **📝 Notes** (removed in v7): redundant with the per-tier workflow steps and Testing Signoff; terminology is not task-level.
- ❌ **Pre-load workflow phase** (removed in v7): its checks are upstream work tracked on other tickets and inferable from the artifacts table.
- ❌ **🔗 Linked Work**, **🤝 Collaboration & Handoffs**, **🔍 Open Questions / Risks** (removed in v5): the native Links panel and the submission epic carry these.

**Standing emoji set (4 entries)**

| Section | Emoji |
|---|---|
| Load Summary | 🎯 *(shared with IndexD Registration Task)* |
| Submission & Artifacts | 📦 *(shared with IndexD Registration Task)* |
| Loading Workflow | 🚦 *(shared with IndexD Registration Task)* |
| Testing Signoff | ✅ *(unique to data loading task)* |

**Required content rules**

- **Scope is loading-data work only.** Schema/model changes use a modeling template (DO-INTMODEL/DO-MODEL). IndexD registration uses the IndexD Registration Task template (DO-INDEX). Software development uses the software development family. See "When NOT to use this template."
- **No Acceptance Criteria, Open Questions / Risks, Verification Surfaces, Per-Environment Verification, Expected Counts, or Notes sections.** Data loading is operational SOP work; the completion bar is the Testing Signoff table. Risks belong on the submission epic.
- **One Task per end-to-end load**: Dev through Prod, not one ticket per environment. The Testing Signoff table is the single source of truth for where the load is in the pipeline.
- **The Prefect data loading pipeline runs once; each tier is populated by OpenSearch backup and restore.** No Jenkins step.
- **No handoff wording in the workflow** ("assign for QA testing", "assign for Prod verification"): the handoff is the ticket's status change in Jira.
- **Issue type is Task.** Do not use Story or Subtask.
- **Title convention:** `Data Loading: <Program Short Name> <Study Short Name> <version>`: reuse the exact token from the submission epic title (Section DO-EPIC) verbatim, omitting parts that do not apply, for example `Data Loading: CMB v6`.
- **Parent Epic field set via `customfield_12350`** to the study's submission epic (Section DO-EPIC). CTDC-1664 stays open only for cross-study integration work no single submission owns.
- **Developer field is required.** Set `customfield_23650` to the engineer who runs the load.
- **Leave the ticket Unassigned at creation** per standing convention.
- **The Data Loading task carries no label**: the load is performed by engineering; the `Data-Concierge` label belongs to the paired IndexD Registration (Index) task.
- **Sprint: always add the ticket to the standing `CTDC Data Related` sprint at creation** (board 641, sprint id 8612; a permanent future-state sprint that holds all data-operations work). Never leave it in the backlog, and do not put it in a numbered development sprint at creation; a TPM pulls it into a sprint when the work is scheduled; pass the new key to `jira_add_issues_to_sprint` right after creation.
- **`Relates` link to each submission's Data Hub tracking ticket (DHDM-XXX).** DHDM tracks per submission, so a load spanning several submissions links several DHDM tickets.
- **`Relates` link to the paired IndexD Registration Task** when one exists: the two run in parallel; do **not** use `Blocks`. The non-blocking relationship is conveyed by the link, not restated in the body.
- **No other ticket keys in the body.** The epic, the DHDM trackers, the paired IndexD task, and sibling tasks are carried by the Epic Link and the native Links panel.
- **Submission & Artifacts table is mandatory and complete.** One row per submission the load consumes, metadata-only submissions included, every column populated. Use PLACEHOLDER explicitly when a value is pending upstream; never leave a cell blank or fold several submissions into one cell.
- **Run records go in a comment, not the description.** Post Prefect flow-run links as a plain-text comment grouped by environment, one line per submission.
- **Jira wiki authoring** (connector Markdown translation is off, so what is sent is stored exactly; see SKILL.md 7b-shared, "Authoring format"): `h3. {emoji} *Title*` headers; `* *Label*: content` labeled lines; `*` bullets and `#` numbered lists at column 0; `*bold*`; `{{monospace}}`; tables `||header||` / `|cell|`; links `[text|url]`; curly braces escaped as `\{...\}`. Blank line after every heading. After pushing, read the description back and confirm it starts with `h3.`, not `###`.

**Writing-and-publishing workflow**

1. Confirm each Release Package exists in `nci-cbiit-clinicaltrialdatacommons-metadata-nonprod`. The paired IndexD registration runs in parallel and need not be complete. Surface any open questions on the submission epic, not the Task.
2. Confirm this is a data load, not a model update or software development. If the schema is changing, a modeling Task (DO-INTMODEL/DO-MODEL) lands first.
3. Identify the submission epic (set as the Epic Link at creation) and the paired IndexD Registration Task (add via a native `Relates` link after creation, never `Blocks`).
4. Create via `jira_create_issue` with `issue_type = "Task"`, the title from the convention above, a placeholder description, the parent epic via `customfield_12350`, and the Developer via `customfield_23650`. Add no label. Leave Unassigned.
5. Push the full body via `jira_update_issue`, authored in Jira wiki markup (stored exactly as sent; no server-side conversion).
6. Add the ticket to the `CTDC Data Related` sprint (id 8612) via `jira_add_issues_to_sprint`.
7. Add the `Relates` links (each submission's DHDM tracker, paired IndexD task, and the other tasks for the study).
8. Verify the rendered description with a UI screenshot.
9. As each environment completes, the tester adds date and initials to Testing Signoff; post the Prefect run links as a comment.
10. **Prod signoff is the close trigger**: once the Prod row is filled in, transition to Closed.

**When NOT to use this template**

- **IndexD registration (registering GUIDs)** → IndexD Registration Task template (DO-INDEX), the parallel sibling.
- **Data modeling** → Data Modeling for Study Submission (DO-MODEL) for study-driven changes, or Data Model Update Task (DO-INTMODEL) for internally-driven changes.
- **Software development** → software development template family.
- **CRDC platform changes** (Fence, IndexD, Submission Portal upgrades) → owned by CRDC platform teams; out of CTDC scope.
- **Pure file-creation tickets** (making a megazip, generating a metadata loading file) → upstream artifact-creation tasks that feed this load; tracked separately. Megazips are created, submitted, and indexed on a Megazip Creation Task (DO-ZIP), and their release package is then loaded with **this** template on a paired ticket titled `Data Loading: <token> Megazips`, whose tier checks confirm every megazip downloads from the Study Details page and any existing megazip still downloads.

If a submission needs schema changes before it can load, that's a modeling Task (DO-INTMODEL/DO-MODEL) the load is blocked on; if it needs IndexD registration, that's the paired IndexD Registration Task (DO-INDEX) running in parallel. Both are separate tickets carried by native Jira links.

**Changelog**

- **v10 (2026-10-22)**: Realigned with ICDC Data Loading v2 (same section layout and rules; platform specifics differ). Loading moved to **Prefect**: one pipeline run (local Neo4j into a single Memgraph), then an OpenSearch backup and restore into Dev, QA, Stage, and Prod; removed the per-tier Jenkins jobs. Submission & Artifacts is **one row per submission** (Submission, CRDC Submission ID, Release Package) with the two constants as bare-value bullets above, matching DO-INDEX v8, so a load spanning several Release Packages lists each beside its Submission ID. **Removed the 📊 Expected Counts section** added in v9 (back to 4 sections): the counts are in the Release Packages. Removed handoff wording from the QA, Stage, and Prod steps. The DHDM `Relates` rule now covers each submission's DHDM tracker, not one per study. **Developer field is now required** (was "not required"). Run records (Prefect flow-run links) go in a comment. No ticket keys in the body. The 2026-09-25 parent-change note is folded into the rules (parent is the submission epic).
- **v9 (2026-09-25)**: Added the 📊 Expected Counts section (5 sections), holding the aggregate counts that moved off the submission epic. Parent changed to the submission epic (DO-EPIC).
- **2026-09-17 sprint rule** (no version bump): every ticket from this template goes into the standing `CTDC Data Related` sprint (board 641, id 8612) at creation. Applies to the whole DO-* family.
- **v8 (2026-06-11)**: Replaced em-dash separators throughout with colons for label lead-ins and semicolons/commas for clause joins; the rendering-safe bullet convention is now `* *Label*: content`. No structural or content changes.
- **v7 (2026-06-03)**: Slimmed to four sections (Load Summary · Submission & Artifacts · Loading Workflow · Testing Signoff). Removed Verification Surfaces, Per-Environment Verification, and Notes; dropped the Pre-load workflow phase. Corrected the two-tier-pipeline error: data loading ran a dedicated Jenkins job per tier. Trimmed Submission & Artifacts to four rows. Load Summary no longer carries a study version, surface enumeration, or model mention.
- **v6 (2026-06-01)**: Paired IndexD task linked with `Relates` and run in parallel (not blocking); artifacts row `Release Package` (was `Release Package Location`); Data Loading task carries no label while the paired Index task carries `Data-Concierge`.
- **v5 (2026-06-01)**: Removed Linked Work, Collaboration & Handoffs, and Open Questions / Risks; adopted "tasks execute, user stories deliberate."
- **v4 and earlier**: One ticket per end-to-end load; explicit payload and per-environment signoff. Superseded.
