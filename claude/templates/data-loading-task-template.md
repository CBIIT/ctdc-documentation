### 7e. 📦 Data Loading Task Template (v6)

> **Use this template for every CTDC data management task that loads a CRDC submission into CTDC — either a brand-new study or new data added to an existing study — and promotes it through Dev → QA → Stage → Prod.** Canonical example: the CMB v5 megazip metadata load (see CTDC-1753 lineage). This template covers the **loading-data** sub-function of the team's data management work. **v6 (2026-06-01)** corrects three conventions: the Data Loading task is linked to its paired IndexD Registration (Index) task with **`Relates` and runs in parallel** — IndexD registration does **not** block the load (metadata can load before GUIDs are minted; file downloads resolve once the registration's GUID spot-check passes); the artifacts table row is **`Release Package`** (constant bucket plus a placeholder directory), not `Release Package Location`; and the Data Loading task carries **no** label, while its paired Index task carries `Data-Concierge`. It is **not** for changes to the data model itself — schema changes use the **Data Modeling for Study Submission** template (Section 7g) or the **Data Model Update Task** template (Section 7f). It is also **not** for the paired IndexD registration that mints the file GUIDs this load consumes — that uses the **IndexD Registration Task** template (Section 7h). See "When NOT to use this template" at the end.

**Why this template**

The CTDC team has two primary functions:

- **Software development** — designing, coding, testing, releasing the React frontend, the Java backend, the microservices, and the infrastructure. Verified against application *behavior*. Tracked with the User Story, Features Epic, Application Pages Epic, Microservices Epic, Design Task, and Bug templates.
- **Data management** — managing CRDC submissions, modeling the shape of CTDC's data, and loading data into CTDC's databases. Verified against application *contents* (row counts, page renders, downloadable artifacts) and against schema state (node types, properties, relationships, version numbers).

Data management itself has two sub-functions:

- **Loading data** — taking a CRDC submission's *contents* (study metadata, files, IndexD entries) and getting them into CTDC's databases. Tracked with **this** template (Section 7e) and its paired sibling, the IndexD Registration Task template (Section 7h).
- **Modeling data** — changing the *shape* of what CTDC's databases can hold. Tracked with the Data Modeling for Study Submission template (Section 7g) for the common study-driven case, or the Data Model Update Task template (Section 7f) for the rare infrastructure-level case.

Application pages get updated when new data is loaded — the Programs page, the Study List, the Study Details page, the Explore Dashboard. That is not a sign that the load involves software work or a model change. It's the application working as designed: rendering the new data with unchanged behavior and unchanged schema.

This template owns only the loading-data sub-function. The verification points, signoff cadence, and pipeline choices below are tuned for loading data into a stable schema with unchanged application code.

**Tasks execute; user stories deliberate.** The v5 of this template formalizes the same principle that the IndexD Registration Task template (Section 7h, v3) adopted: **Open questions, risks, ownership directories, and link inventories belong on the parent user story, not on the Task.** A Task is what the assignee executes — it carries only what's needed to do the work. v4 of this template had a Linked Work section, a Collaboration & Handoffs section, and an Open Questions / Risks section. v5 removes all three:

- **Linked Work** is replaced by Jira's native Links panel (Epic Link, Relates, Blocks, remote links — all visible in the right sidebar without duplication in the description).
- **Collaboration & Handoffs** is replaced by the assignee field + comment thread (ownership is implicit in the workflow steps where it matters; a standalone roster is epic-shaped, not task-shaped).
- **Open Questions / Risks** is replaced by the parent submission user story's Open Questions / Risks section. Program-level risks (model version compatibility, bucket policy refresh, sequencing across multiple studies) belong on the user story that owns the program, not on each child Task.

The result is a 7-section v5 template: shorter, sharper, and unambiguously executable.

The most common antipatterns this template prevents:

1. **One ticket per environment.** Splits the audit trail across four tickets and forces a manual reconstruction of "where is this release in the pipeline?" at every standup. A data load is a single end-to-end promotion, not four independent jobs.
2. **No per-environment signoff record.** When QA, Stage, and Prod testers initial their work in Slack threads or comments, there's no consolidated record for compliance review or for the post-load retrospective if something goes wrong in Prod.
3. **Mixed metaphor with software development tickets or model update tickets.** Writing the description as if it's a code deployment ("merge to main, tag the release") or a frontend change ("update the component to render the new field") obscures that this is a *data* promotion — a different operational pathway with different verification points (Memgraph contents, OpenSearch reindex status, Study Details page rendering, megazip download links). If code is changing, this is the wrong template. If the schema is changing, use a modeling template.
4. **Ambiguous data payload.** "Load the new metadata" without naming the Submission ID, the SharePoint folder, the AWS bucket, the metadata loading file location, and the IndexD manifest location leaves the data engineer guessing — and a guess on which `file.tsv` to load is exactly the kind of mistake that goes undetected until Prod verification. The Submission & Artifacts table in Section 2 exists specifically to eliminate this ambiguity.

The template below resolves all four with three commitments:

1. **One Task per end-to-end load.** Single source of truth from Dev kickoff through Prod signoff. The Testing Signoff section is the artifact that justifies the one-ticket approach — it consolidates four environment-level handoffs into one auditable table.
2. **Two-tier pipeline grouping.** Dev and QA run on the **Jenkins lower-tiers pipeline**; Stage and Prod run on the **Jenkins upper-tiers pipeline**. That's a real architectural boundary in our deployment infrastructure, not a cosmetic grouping.
3. **Explicit payload, explicit verification.** The Submission & Artifacts table names every artifact going into the load. The Verification Surfaces section names every application page to check after each environment load.

**Pipeline & store anatomy (read once before drafting)**

A CTDC data load touches multiple systems in sequence:

- **Source artifacts** — Release Package (metadata TSVs + indexd.tsv manifest) lives in the **AWS S3 metadata bucket** (`nci-cbiit-clinicaltrialdatacommons-metadata`). Object files (BAMs, VCFs, DICOMs, megazips) live in the **AWS S3 object files bucket** (typically `nci-crdc-data-bucket-prod`). The metadata loading file (`file.tsv` or similar) the Jenkins pipeline consumes lives in the Release Package.
- **Loader repo** — **`CBIIT/crdc-ctdc-dataloader`** (branch `master`). The Python loader code that the Jenkins pipeline runs. For a routine data load, no changes are needed in this repo — the loader code is stable.
- **Loading pipeline** — **Jenkins**. Two pipelines exist: *lower tiers* (targets Dev and QA) and *upper tiers* (targets Stage and Prod). The pipeline parses the metadata loading file, applies it to the graph, and triggers the reindex.
- **Graph database** — **Memgraph**. This is the canonical metadata store. The loading pipeline writes nodes and relationships here. **The model is assumed stable for a data load — if the model is changing, that's a modeling Task and uses a modeling template.**
- **Search index** — **OpenSearch**. The frontend's Explore Dashboard, autocomplete, and filters read from OpenSearch, not from Memgraph directly. After every metadata load, OpenSearch must be reindexed from Memgraph before the frontend reflects the change. The Jenkins pipeline handles this — but verification needs to confirm both the Memgraph write **and** the OpenSearch reindex landed.
- **Application surfaces** — Study Details page, Explore Dashboard, Cart, Participant Details, etc. These are the pages a tester loads in their browser to verify the data actually shows up correctly.

The template walks all of this in order: payload → pipeline run per environment → verification per environment → signoff per environment.

**Section order (7 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header.

1. `### 🎯 **Load Summary**` — Two to three sentences. What's being loaded, which study or release it belongs to, whether this is a new study or an addition to an existing study, and what application surfaces it lights up. Example: *"Load the Cancer Moonshot Biobank (CMB) v5 release — new data added to the existing CMB study, including the megazipped VCF file — through all four CTDC environments. After load and reindex, the CMB Study Details page should expose the VCF megazip download link and the Explore Dashboard should reflect the updated participant and file counts."*

2. `### 📦 **Submission & Artifacts**` — Required field. A six-row table holding the artifacts the load consumes. Study identity (program, study name, dbGaP, submitter, chronology) lives on the parent submission user story linked via the native Links panel — not in this table. The six rows:

   | Field | Value | Notes |
   |---|---|---|
   | CRDC Submission ID | *(Submission Portal ID — one per submission)* | Use the ID issued by the CRDC Submission Portal. Multiple IDs allowed if this load consolidates more than one submission. |
   | Release Package | AWS Bucket: `nci-cbiit-clinicaltrialdatacommons-metadata/<directory-placeholder>` | The bucket is always the same; the directory is created when the study is released from the CRDC Submission Portal, so the directory name stays a placeholder until release. The Release Package contains the metadata TSVs (including the loading file) AND the indexd.tsv manifest. |
   | Object Files Location | AWS Bucket: `nci-crdc-data-bucket-prod` *(confirm before assuming)* | Where the object files (BAMs, VCFs, DICOMs, megazips) live. Include the bucket path prefix if files are scoped to a sub-folder. |
   | Metadata loading file | *(filename, e.g., `file.tsv`)* | The TSV the Jenkins pipeline consumes from the Release Package. This is the artifact that drives the load. |
   | Study (acronym + version) | *(e.g., CMB v5; or AHEP0731 v1 for a per-study load)* | Drives the Study Details page verification in Section 4. |
   | Target model version | *(e.g., `1.22.2`)* | The CTDC model version that this load runs against. Should match the `Version:` field in `model-desc/ctdc_model_file.yaml` on the `prod` branch of `CBIIT/ctdc-model` at the time of load. If the load requires a *newer* model version, that's a modeling Task (Section 7f or 7g) that must land first. |

   **Naming discipline**: the **Release Package** row is named *without* a "Location" suffix because its value is a constant bucket plus a *placeholder directory* that does not exist until the study is released — a partially-known address, not a fixed one. The **Object Files Location** row keeps the "Location" suffix because it holds a fixed bucket address. The general rule still holds — a row that holds a stable address ends in "Location" — and the Release Package is the documented exception while its directory remains a placeholder.

   **Rows omitted compared to v4**: "SharePoint folder" (the Release Package lives in AWS, not SharePoint — v4 was outdated on this); "IndexD manifest location" (it's part of the Release Package, named in workflow step 1 by reference); "New study or addition?" (determined by whether the study already exists in Memgraph; can be noted inline in the Load Summary instead of as a separate row).

3. `### 🚦 **Loading Workflow**` — Numbered list of the end-to-end promotion steps. **Two-tier pipeline grouping** is enforced here: Dev + QA on lower tiers, Stage + Prod on upper tiers.

   **Pre-load**
   1. Confirm the Release Package exists in `nci-cbiit-clinicaltrialdatacommons-metadata` and the metadata loading file inside it is the expected one. The paired IndexD Registration Task runs **in parallel** and does **not** block this load — metadata can load before GUIDs are minted. File downloads for the loaded study resolve once the registration's GUID spot-check passes, so coordinate timing with that task rather than waiting on it.
   2. If the load includes new object files or a megazip, confirm those files exist in the Object Files bucket and that each indexd.tsv entry resolves to a real bucket location.
   3. Confirm the target model version named in Section 2 is the version currently deployed in each environment. If a model update is required first, that work belongs in a modeling Task (Section 7f or 7g) which must land before this load proceeds.

   **Lower tiers — Dev**
   4. Run the Jenkins **lower-tiers** data loading pipeline targeting Dev with the confirmed metadata loading file.
   5. After pipeline completion, verify in Dev: Memgraph node counts updated, OpenSearch reindex completed, Study Details page renders the new data, no errors in application metrics or page loads. Record results in Section 5 (Per-Environment Verification).

   **Lower tiers — QA**
   6. Run the Jenkins **lower-tiers** data loading pipeline targeting QA with the same metadata loading file.
   7. Assign for QA testing. Tester verifies: data renders on expected pages, megazip download link (if applicable) initiates a successful download of the correct file, no regressions on existing studies. Tester initials Section 6 (Testing Signoff) on completion.

   **Upper tiers — Stage**
   8. Run the Jenkins **upper-tiers** data loading pipeline targeting Stage with the same metadata loading file.
   9. Assign for Stage testing and signoff. Same verification points as QA, plus production-parity sanity checks. Tester initials Section 6 on completion.

   **Upper tiers — Prod**
   10. Run the Jenkins **upper-tiers** data loading pipeline targeting Prod with the same metadata loading file.
   11. Assign for Prod verification and signoff. Verify in production with the live application URL. Tester initials Section 6 on completion. **This is the trigger to close the ticket.**

4. `### 🧪 **Verification Surfaces**` — Bullet list naming every application surface that must be checked after each environment load. Bullet list (italic-labelled, em-dash separators — the rendering-safe pattern):

   - *Memgraph node counts* — confirm expected node and relationship counts post-load (drives the integrity check)
   - *OpenSearch reindex* — confirm reindex completed without error and counts match Memgraph
   - *Study Details page* — for the affected study, confirm new data renders correctly and any megazip download link initiates the right file
   - *Explore Dashboard* — confirm participant, study, and file counts reflect the load
   - *Programs page / Study List* — only for new study loads; confirm the new study appears in the global study list and Programs page
   - *Participant Details page* — only when participant-level metadata is part of the load
   - *Cart and File Download* — only when new files are part of the load; confirm cart selection and IndexD signed-URL flow still works
   - *Application error metrics* — confirm no spike in 4xx / 5xx after load
   - *Logs (CloudWatch or equivalent)* — confirm no exceptions during reindex

5. `### 📊 **Per-Environment Verification**` — Table to record observed Memgraph counts, OpenSearch counts, and any anomalies, per environment.

   | Environment | Memgraph node count (post-load) | OpenSearch doc count (post-reindex) | Application surfaces verified | Anomalies / notes |
   |---|---|---|---|---|
   | Dev | | | | |
   | QA | | | | |
   | Stage | | | | |
   | Prod | | | | |

6. `### ✅ **Testing Signoff**` — The completion record. Tester fills in date and initials per environment as work progresses. **Prod signoff is the trigger to transition the ticket to Closed.**

   | Environment | Testing Completion Date | Tester Initials |
   |---|---|---|
   | Dev | | |
   | QA | | |
   | Stage | | |
   | Prod | | |

7. `### 📝 **Notes**` — Bullet list. Optional content: prior load lessons learned, links to retrospective notes if a prior load surfaced an issue worth remembering, terminology translations (Bento "Case" → CTDC "Participant"), known constraints. If there's no meaningful note, write "None at this time."

**Sections omitted compared to v4**

- ❌ **🔗 Linked Work** — Removed in v5. Jira's native Links panel (right sidebar) already shows Epic Link, Relates links, Blocks links, and remote links. Duplicating this content in the description body is noise.
- ❌ **🤝 Collaboration & Handoffs** — Removed in v5. Ownership stays implicit via the Jira assignee field + comment audit trail. Standalone ownership directory was epic-shaped.
- ❌ **🔍 Open Questions / Risks** — Removed in v5. Program-level open questions and risks belong on the parent submission user story (e.g., CTDC-1805 for the NCTN-NCORP TCIA Images-Only submission), where the team negotiates scope and tracks risk. Tasks should carry only what's needed to do the work. If a Task accumulates open questions, that's a signal the parent user story isn't fully baked.

**Standing emoji set (7 entries)**

| Section | Emoji |
|---|---|
| Load Summary | 🎯 *(shared with IndexD Registration Task)* |
| Submission & Artifacts | 📦 *(shared with IndexD Registration Task)* |
| Loading Workflow | 🚦 *(shared with IndexD Registration Task)* |
| Verification Surfaces | 🧪 *(shared with IndexD Registration Task)* |
| Per-Environment Verification | 📊 *(unique to data loading task — environment-level table)* |
| Testing Signoff | ✅ *(unique to data loading task — the per-environment audit record)* |
| Notes | 📝 *(shared with IndexD Registration Task)* |

**Required content rules**

- **Scope is loading-data work only.** Loading a CRDC submission into CTDC — either a new study or new data added to an existing study. **Schema or model changes** do not use this template — those use a modeling template (Section 7f or 7g). **IndexD registration (minting GUIDs)** does not use this template — that uses the IndexD Registration Task template (Section 7h). **Software development work** does not use this template. See "When NOT to use this template" at the end.
- **No Acceptance Criteria section.** Data loading is operational SOP work; the completion bar is the Testing Signoff table plus the Verification Surfaces checklist. AC belongs on user stories in the software development lane, not on the load itself.
- **No Open Questions / Risks section.** Open questions and risks live on the parent submission user story (e.g., CTDC-1805), not on this Task. If a question or risk surfaces during the load, raise it as a bullet under the parent user story's Open Questions / Risks section so it's tracked at the program level. The Task description carries only what the assignee needs to execute.
- **One Task per end-to-end load** — Dev through Prod, not one ticket per environment. The Testing Signoff table is the single source of truth for where the load is in the pipeline.
- **Issue type is Task** on this tracker, matching the convention used on the megazip family (CTDC-1983 etc.) and on CTDC-2060. Do not use Story or Subtask.
- **Parent Epic field set via `customfield_12350`.** Default parent is CTDC-1664 (CTDC Data Integration) unless a release-specific epic exists.
- **`Relates` link to the parent submission user story is mandatory.** Set via `jira_create_issue_link` after ticket creation. The parent user story is the canonical record of study identity AND the home for open questions / risks. The Task description does not duplicate that content.
- **`Relates` link to the study-specific Data Hub tracking ticket (DHDM-XXX) when one exists.** Set the same way as the parent submission user story link.
- **`Relates` link to the paired IndexD Registration Task** when one exists. The IndexD Registration Task (Section 7h) runs **in parallel** with the Data Loading Task — it does **not** block the load (metadata can load before GUIDs are minted; file downloads resolve once the registration's GUID spot-check passes). Do **not** use a `Blocks` link between them. Pass the IndexD ticket as the inward issue, the load ticket as the outward issue.
- **The Data Loading task carries no label.** The load is performed by engineering, so this task takes **no** `Data-Concierge` label — that label belongs to the paired IndexD Registration (Index) task, where indexing is handled by the Data Concierge.
- **Leave the ticket Unassigned at creation.** Per standing team convention, newly created tickets are left Unassigned unless an assignee is explicitly directed. Data management tasks (IndexD Registration, Data Loading) also do not require the Developer field.
- **Two-tier Jenkins pipeline distinction must be explicit** in the Loading Workflow section. Dev + QA → lower tiers; Stage + Prod → upper tiers. This is not stylistic — running the wrong pipeline at the wrong tier is a real failure mode.
- **Memgraph and OpenSearch both named in Verification Surfaces.** A successful Memgraph write without an OpenSearch reindex means the frontend won't show the new data; a successful reindex against an incomplete Memgraph write means stale or partial results. Both surfaces are required checks.
- **Submission & Artifacts table is mandatory and complete.** All six rows present. Use PLACEHOLDER explicitly when a value is pending upstream — never silently omit a row.
- **Rendering-safe authoring patterns** — section headers use `### **Title**` Markdown form (round-trips cleanly to `h3.` Jira-wiki); bullet lists with italic labels use `* *Label* — content` (italic-and-em-dash), NOT `- **Label:** content` (bold-and-colon, which the converter collapses to broken `**...:*` mismatched-asterisk damage); tables use Jira-wiki `||header||` syntax, NOT GitHub-flavored Markdown `|h|h|`. Verified on CTDC-2060 second push.

**Writing-and-publishing workflow**

1. Confirm the Release Package artifacts exist before drafting the load ticket. If the Release Package isn't generated yet, the load ticket is premature. IndexD registration runs **in parallel** and need not be complete — the load and the registration proceed concurrently, and file downloads resolve once the registration's GUID spot-check passes. **Surface any open questions on the parent submission user story, not on the Task.**
2. Confirm this work is a data load, not a model update or software development. If the schema is changing, use a modeling template (Section 7f or 7g). If code is changing in the frontend, backend, or microservices without a schema change, use the software development ticket family.
3. Confirm the target model version (Section 2) matches what's deployed in each environment. If the load needs a newer model version, file a modeling Task first.
4. **Identify the parent submission user story and the paired IndexD Registration Task.** Every CTDC data load has a program-level user story (e.g., CTDC-1805) and a paired IndexD Registration Task (e.g., CTDC-2060 for AHEP0731). Both go on the ticket via Jira native links after creation: `Relates` for the user story, and `Relates` to the paired IndexD ticket (the two run in parallel — do **not** use `Blocks`).
5. Create the data loading task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields`. Add **no** label (the `Data-Concierge` label belongs to the paired IndexD task, not this one). **Leave the ticket Unassigned** per standing convention; the TPM coordinates the load and an assignee is set per-environment only when work is actively picked up.
6. Push the description in two steps: create with the placeholder, then `jira_update_issue` with the full Markdown body.
7. Add `Relates` links from the load ticket to the parent submission user story AND (when applicable) the study-specific DHDM tracker.
8. Verify the rendered description with a UI screenshot — wiki source is unreliable as a render preview.
9. As each environment completes, the assigned tester adds their initials and date to Section 6 (Testing Signoff) and notes counts in Section 5 (Per-Environment Verification) directly in the description, or via a comment that the TPM rolls up into the description at end-of-environment.
10. **Prod signoff is the close trigger.** Once Section 6's Prod row is filled in, transition the ticket to Closed.

**When to expand vs trim**

- **New study load** → use the template as written. Section 2 captures every artifact; Section 4 includes the Programs / Study List page check.
- **Addition to existing study** (e.g., CMB v5 megazip pattern) → use the template as written. This is the canonical case it was designed for. Skip the Programs / Study List bullet in Section 4 unless the study itself wasn't previously visible there.
- **Multi-study omnibus load** (multiple submissions consolidated into one Jenkins run) → expand Section 2 (Submission & Artifacts) to a table per submission, or add submission-ID rows; expand Section 5 (Per-Environment Verification) to a row per environment-per-study. Keep one ticket — the Jenkins run is one unit of work.
- **Re-load after data correction** (same submission, corrected file) → use the template as written, and use Section 7 (Notes) to explain *why* the re-load is needed.
- **Single-file hot-patch** (one corrected file, no metadata change) → this template is overkill. A free-form Task with a one-line description and a single-row signoff table is enough. But if the patch crosses environments, still use the four-row Testing Signoff table to keep the audit trail consistent.

**When NOT to use this template**

The CTDC team has two primary functions: software development and data management. Data management has two sub-functions: loading data (this template + the IndexD Registration Task template) and modeling data. This template covers the **end-to-end data load** work pattern within the loading-data sub-function.

**IndexD registration (minting GUIDs)** — does NOT use this template. Use the **IndexD Registration Task** template (Section 7h) instead. That sibling template covers the upstream external CTDS/DCFS handoff that produces the registered GUIDs this load consumes.

**Data modeling** — does NOT use this template. Use the **Data Modeling for Study Submission** template (Section 7g) for study-driven model additions, or the **Data Model Update Task** template (Section 7f) for infrastructure-level model changes.

**Software development work** — does NOT use this template. Use the appropriate template from the software development family.

**Other work that is none of the above:**

- **CRDC platform changes** — Fence, IndexD, Submission Portal upgrades owned by CRDC platform teams. Out of CTDC scope entirely; CTDC files dependency tickets if affected, but does not own the work.
- **Pure file-creation tickets** — making a megazip, generating a metadata loading file. These are *upstream artifact creation* tickets that feed *this* template, not the load itself. They're data-adjacent operational tasks tracked separately.

If a CRDC submission requires schema changes before it can be loaded, that's two pieces of data management work in two sub-functions: the schema change is a modeling Task, and the load is a Data Loading Task that uses this template. They depend on each other but are tracked separately, with the load blocked until the model update has landed and stabilized in each environment. If a CRDC submission also requires IndexD registration, that's the paired IndexD Registration Task (Section 7h), which runs **in parallel** with this load — it does **not** block the load; metadata can load before GUIDs are minted, and file downloads for the study resolve once the registration's GUID spot-check passes.
