### 7h. 🔖 IndexD Registration Task Template (v7)

> **Use this template for every CTDC data management task that registers a study's files in CRDC IndexD: minting GUIDs that the paired Data Loading Task will reference.** The canonical example is **CTDC-2060** (Index NCTN-NCORP TCIA Images-Only AHEP0731 Files), drafted 2026-05-26 as the first Sprint 28 ingestion ticket. CTDC-2060 was authored under this template's v1, iterated to v2 shape directly in Jira, then refined to v3 when the team formalized the principle that **Open Questions / Risks belong on the parent user story, not on the Task**. **v4 (2026-06-01)** corrects three conventions: the IndexD registration (Index) task and its Data Loading task are linked with **`Relates` and run in parallel**: IndexD registration does **not** block the load; the artifacts table row is **`Release Package`** (constant bucket plus a placeholder directory), not `Release Package Location`; and the registration (Index) task carries the **`Data-Concierge`** label. **v5 (2026-06-03)** restructures the Submission & Artifacts table to mirror the Data Loading Task's shape: the constant **AWS Account ID** and **AWS S3 Bucket** are split into their own rows, **Release Package** holds the directory name only, and the spot-check anchor is renamed **Sample GUID**; the **Consent group / ACL value** and **Object Files Location** rows are removed because both are captured in the indexd.tsv manifest itself (the `acl` and `url` columns), so restating them in the table was redundant. **v6 (2026-06-04)** slims the task to **four sections**: the Registration Summary is reduced to a single sentence (consent codes and on-hold status are visible in Jira statuses and on the parent user story, not restated here), the Workflow drops the *Confirmation and verification* phase (the spot-check lives wholly in the Verification section now), and the **Notes** section is removed entirely (terminology and historical context are not task-level content). **v7 (2026-06-11)** replaces em-dash separators throughout with colons for label lead-ins and semicolons/commas for clause joins; the rendering-safe bullet convention is now `* *Label*: content` (italic label, colon separator). This template covers the **upstream artifact creation** work pattern within the loading-data sub-function; it is the **parallel partner** of a Data Loading Task (Section 7e), not a substitute for one, and not a blocker of one. See "When NOT to use this template" at the end.

**Why this template**

The CTDC team has two primary functions (software development and data management), and data management has two sub-functions: loading data and modeling data. The Data Loading Task template (Section 7e) covers the *promotion of a CRDC submission's contents* into CTDC's databases through Jenkins. But before that load can run, every file in the submission needs a globally unique identifier (GUID) registered in **CRDC IndexD**, and that registration is performed by an **external team at the University of Chicago Center for Translational Data Science (CTDS)**, not by the CTDC engineering team.

CTDC's role in IndexD registration is **coordination**, not engineering: validate the indexd.tsv manifest that ships inside the CRDC Submission Portal's Release Package, hand it off to the external CTDS/DCFS team via the agreed channels, then verify the minted GUIDs resolve correctly. The registration runs **in parallel** with the paired Data Loading Task; metadata can load before GUIDs are minted, and file downloads for the study resolve once this registration's GUID spot-check passes.

**Tasks execute; user stories deliberate.** This is the core principle the template enforces. Tasks are operational work units the assignee executes; they should carry only what's needed to do the work. **Open questions, risks, and unresolved decisions belong on the parent user story**, where the team negotiates scope and tracks risk at the program level. By the time work is decomposed into Tasks, those questions should be resolved enough that the Task can be executed. If a Task accumulates open questions, that's a signal the parent user story isn't fully baked, and the questions should be raised there, not buried in a Task description where they don't influence sequencing decisions and are harder to find.

The template is **task-shaped: four sections totaling well under 700 words, with ownership, external-coordination, and deliberative content removed.** The assignee can read this template-shaped ticket and know exactly what to do without paging through narrative.

The five most common antipatterns this template prevents:

1. **Treating IndexD registration as internal pipeline work.** It is not. There is no Jenkins job, no Memgraph write, no environment promotion. IndexD is a single centralized service operated by CTDS/DCFS that mints GUIDs for the entire CRDC platform. The bottleneck is an external team's queue, not our pipeline capacity.
2. **Treating the indexd.tsv as a manifest CTDC authors.** It is not. The indexd.tsv ships *inside* the validated Release Package the CRDC Submission Portal produces. CTDC's role is extraction and validation, not authoring. Earlier versions of this template (and the CTDC-1907 lineage) suggested otherwise.
3. **Duplicating Jira's native Links panel inside the description body.** A standalone "Linked Work" section in a Task description duplicates what the right-sidebar Links panel already shows: Epic Link, Relates links, Blocks links, remote links. The template omits this section entirely; set links via the Jira native mechanisms and trust the sidebar.
4. **Accumulating open questions on a Task that should be on the parent user story.** This is the v3 lesson. If the Release Package GUID isn't generated yet, if the bucket policy hasn't been refreshed, if model version backward compatibility is unconfirmed, those are *program-level* risks that the parent user story (e.g., CTDC-1805) carries on behalf of every Task it spawns. Restating them on each child Task creates noise and dilutes the user story's role as the deliberative anchor.
5. **No verification step recorded on the ticket.** When CTDS/DCFS reports "registration complete," CTDC needs to verify by resolving a sample of GUIDs against the IndexD resolution endpoint. The Verification section codifies the spot-check method and acceptance criteria so the close trigger is reproducible.

**Service & handoff anatomy (read once before drafting)**

- **CRDC IndexD**: The actual service. Open source, maintained by UChicago CTDS (`github.com/uc-cdis/indexd`). Mints 128-bit GUIDs (`dg.4DFC/00006197-6407-5014-8175-c82efdf6cf0f`) that resolve to physical S3 locations and carry access-control metadata (`acl` / `authz` fields). One centralized instance serves the entire CRDC platform; there is no Dev/QA/Stage/Prod separation for registration the way there is for the CTDC application.
- **Source artifacts**: Release Package + Object Files live in CRDC-owned AWS S3 buckets. The Release Package bucket is universal: `nci-cbiit-clinicaltrialdatacommons-metadata`. The Object Files bucket is typically `nci-crdc-data-bucket-prod`. The **indexd.tsv manifest is part of the Release Package**: do not regenerate it.
- **DCF Google Drive**: The drop-off point for the indexd.tsv copy extracted from the Release Package. CTDS/DCFS monitors this folder for new manifests. Folder: `https://drive.google.com/drive/u/2/folders/1ZVsv2vFEcTPBT2IYsaOb_XCjpWjjMGTb`.
- **CRINTAKE Jira board**: `tracker.nci.nih.gov/projects/CRINTAKE/`. The external team's intake queue. Filing a ticket here tells CTDS the manifest is ready and gives them a place to coordinate the work and report completion.
- **Resolution endpoint**: `https://nci-crdc.datacommons.io/index/<guid>`. Public endpoint for resolving a GUID to its IndexD record. Used for verification spot-checks.
- **Paired Data Loading Task**: The CTDC ticket that loads this study, linked via `Relates` and run **in parallel** with this registration. The two are not sequential: metadata can load before GUIDs are minted. File downloads for the loaded study resolve once this registration's GUID spot-check passes; the load ticket references the GUIDs in its metadata loading file's `data_file_uuid` column.

**Section order (4 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. If a section has no real content for a given registration, omit the header entirely rather than stub it with "None at this time." Keep the remaining sections in the order shown so a reader scanning multiple registration tickets sees the same visual flow.

1. `### 🎯 **Registration Summary**`: **One sentence**: what's being indexed and the paired Data Loading Task this registration runs in parallel with. **Do not restate study identity, consent codes, dbGaP IDs, submission chronology, or on-hold status**: all of that lives on the parent submission user story (linked via the native Links panel) and is visible in Jira statuses. Example: *"Register the NCTN-NCORP TCIA Images-Only AHEP0731 study files in CRDC IndexD, minting GUIDs for the object files that the paired Data Loading Task will reference."*

2. `### 📦 **Submission & Artifacts**`: Required field. A five-row table holding the artifacts the external CTDS team needs to do the work. Study identity (program, study name, dbGaP, submitter, chronology) lives on the parent submission user story linked via the native Links panel, not in this table. The five rows:

   | Field | Value | Notes |
   |---|---|---|
   | CRDC Submission ID | *(Submission Portal ID, one per submission)* | Issued by the CRDC Submission Portal; one per submission. |
   | AWS Account ID | `101183076466` | Constant for CTDC: the CTDC data commons AWS account. |
   | AWS S3 Bucket | `nci-cbiit-clinicaltrialdatacommons-metadata` | Constant for CTDC: the metadata bucket that holds every release package. |
   | Release Package | *(directory name)* | Directory name only, within the AWS S3 Bucket above. The directory is created when the study is released from the CRDC Submission Portal, so it stays a placeholder until release. Contains the indexd.tsv manifest the CTDS team registers. |
   | Sample GUID | *(the first minted GUID, used as the spot-check anchor)* | The spot-check anchor used in the Verification section. Fill in once the Release Package is generated; the CRDC Submission Portal pipeline assigns the GUIDs ahead of registration. |

   **Naming discipline**: the constant bucket address now lives in its own **AWS S3 Bucket** row, and **Release Package** holds only the directory name within it, so there is no longer a combined "address" row that would need a "Location" suffix. This mirrors the Data Loading Task's table exactly.

   **Rows omitted**: "GUID prefix" (always `dg.4DFC/` for CRDC, implicit; mention only if the study uses a non-standard prefix); "indexd.tsv manifest path" (it's part of the Release Package; saying so in the Release Package row's Notes is sufficient); "CTDC Data Model version" (belongs on the Data Loading Task, not the IndexD ticket; IndexD registration doesn't care about model versions). As of **v5**, also omitted: **"Object Files Location"** (the manifest's `url` column already points to the object files) and **"Consent group / ACL value"** (the manifest's `acl` column carries it on every row); both are captured in the indexd.tsv manifest itself, so restating them in the table was redundant.

3. `### 🚦 **Registration Workflow**`: Numbered list grouped into two phases. Standard CTDC sequence:

   **Pre-registration**
   1. Extract the indexd.tsv manifest from the Release Package in the `nci-cbiit-clinicaltrialdatacommons-metadata` bucket. Validate that every row carries a non-empty `acl` value and that the `acl` is uniform across all rows, the row count matches the file count for this study, every row's `url` resolves to a real object-file location, and the GUID placeholder format is consistent with the `dg.4DFC/` prefix.

   **External handoff**
   2. Upload the extracted indexd.tsv to the [DCF Google Drive folder](https://drive.google.com/drive/u/2/folders/1ZVsv2vFEcTPBT2IYsaOb_XCjpWjjMGTb) for indexing. Filename convention is preserved from the Release Package; do not rename.
   3. File a CRINTAKE intake ticket on the [CRDC CRs_INTAKE Jira board](https://tracker.nci.nih.gov/projects/CRINTAKE/) describing the study, the name of the indexd.tsv uploaded to DCF Google Drive in step 2, and any requested due date if the registration is time-bound for a planned Data Loading Task.
   4. Link the CRINTAKE ticket back to this CTDC ticket as a Jira-to-Jira remote link.
   5. If a due date is communicated, notify both the NCI CRDC (Leidos) PM and the NCI DCFS PM as early as possible.

   **Step count: 5 (1 pre-registration, 4 external handoff).**

4. `### 🧪 **Verification**`: How CTDC confirms the registration worked, and the close trigger. Once CTDS/DCFS reports indexing complete (via CRINTAKE or direct notification), spot-check the minted GUIDs; a successful spot-check is the trigger to close the ticket. Bullet list (italic-labelled, colon separators, the rendering-safe pattern):

   - *How to spot-check*: Resolve a sample of GUIDs by hitting the IndexD resolution endpoint at `nci-crdc.datacommons.io/index/` with the GUID appended. A pass returns a non-error response with the full IndexD record (`did`, `urls`, `hashes`, `size`, `acl`, `authz`, `rev`, `baseid`): `urls` points to the expected S3 object-file location, `acl` is non-empty, and `size`/`hashes` are non-empty.
   - *How many GUIDs to spot-check*: At minimum, the first, middle, and last GUIDs in the manifest. If the study has more than 1,000 files, spot-check at least 5, including any GUIDs flagged by CTDS as edge cases.
   - *If a spot-check fails*: Do not close this ticket. Reopen the CRINTAKE ticket with the specific GUIDs and resolution-endpoint responses, coordinate the fix with CTDS, and surface the issue on the parent submission user story so it's tracked at the program level.

**Sections omitted compared to v1**

- ❌ **🔗 Linked Work**: Removed in v2. Jira's native Links panel (right sidebar) already shows Epic Link, Relates links, Blocks links, and remote links. Duplicating this content in the description body is noise.
- ❌ **🌐 External Handoff Coordination**: Removed in v2. The CTDS, DCFS, DCF Google Drive folder, CRINTAKE board, and PM contacts are folded into the workflow steps where they're used. A standalone directory section was duplicative.
- ❌ **🤝 Collaboration & Handoffs**: Removed in v2. Ownership stays implicit via the Jira assignee field + comment audit trail. Standalone ownership directory was epic-shaped.
- ❌ **🔍 Open Questions / Risks**: Removed in v3. The principle is *tasks execute, user stories deliberate*. Program-level open questions and risks belong on the parent submission user story (CTDC-1805 for the NCTN-NCORP TCIA Images-Only submission, for example). The user story is where the team negotiates scope and tracks risk; Tasks should carry only what's needed to do the work. If a Task accumulates open questions, that's a signal the parent user story isn't fully baked.

**Standing emoji set (4 entries)**

| Section | Emoji |
|---|---|
| Registration Summary | 🎯 *(shared with Data Loading Task)* |
| Submission & Artifacts | 📦 *(shared with Data Loading Task)* |
| Registration Workflow | 🚦 *(shared with Data Loading Task)* |
| Verification | 🧪 *(shared with Data Loading Task; scoped to GUID resolution spot-check)* |

**Required content rules**

- **Scope is IndexD registration only.** Minting GUIDs for files via the external CTDS/DCFS handoff. **Data loading** uses the Data Loading Task template (Section 7e). **Schema or model changes** use the Data Modeling for Study Submission template (Section 7g) or the Data Model Update Task template (Section 7f). See "When NOT to use this template" below.
- **No Acceptance Criteria section.** IndexD registration is operational SOP work; the completion bar is the GUID spot-check passing (Section 4). AC belongs on user stories, not on Tasks.
- **No Open Questions / Risks section.** Open questions and risks live on the parent submission user story (e.g., CTDC-1805), not on this Task. If a question or risk surfaces during the registration work, raise it as a bullet under the parent user story's Open Questions / Risks section so it's tracked at the program level. The Task description carries only what the assignee needs to execute.
- **One Task per registration handoff**: one CRINTAKE intake ticket, one IndexD registration ticket on the CTDC side. If a single Data Loading Task depends on two separate registrations (e.g., one for the Release Package, one for the Object Files), file two registration tickets and have the load ticket linked from both via `Relates`.
- **Issue type is Task** on this tracker, matching the convention used on CTDC-2060. Do not use Story or Subtask.
- **Parent Epic field set via `customfield_12350`.** Default parent is CTDC-1664 (CTDC Data Integration) unless a release-specific epic exists.
- **`Data-Concierge` label is mandatory on this registration (Index) task.** Indexing is performed by the Data Concierge, so the IndexD Registration task carries the `Data-Concierge` label, set at creation via the `labels` field. The paired Data Loading task carries **no** label; the load is performed by engineering.
- **Leave the ticket Unassigned at creation.** Per standing team convention, newly created tickets are left Unassigned unless an assignee is explicitly directed. Data management tasks (IndexD Registration, Data Loading) also do not require the Developer field.
- **`Relates` link to the parent submission user story is mandatory.** Set via `jira_create_issue_link` after ticket creation. The parent user story is the canonical record of study identity AND the home for open questions / risks. The Task description does not duplicate that content.
- **`Relates` link to the study-specific Data Hub tracking ticket (DHDM-XXX) when one exists.** Set the same way as the parent submission user story link. This gives the ticket two anchors: the program-level user story (CTDC-XXXX) and the study-specific Data Hub tracker (DHDM-XXX). Verified on CTDC-2060 (linked to CTDC-1805 + DHDM-143).
- **`Relates` link to the paired Data Loading Task is mandatory** when that load ticket exists. The registration and the load run **in parallel**: IndexD registration does **not** block the load (metadata can load before GUIDs are minted; file downloads resolve once this registration's GUID spot-check passes). Do **not** use a `Blocks` link between them. Pass the registration ticket as the inward issue, the load ticket as the outward issue. If the load ticket has not been filed yet at registration ticket creation time, add the `Relates` link as soon as the load ticket exists.
- **Remote link to the CRINTAKE ticket is mandatory** once workflow step 4 is complete. Use `jira_create_remote_issue_link` with the CRINTAKE ticket URL. A free-text reference to the CRINTAKE ticket key is **not** sufficient; the remote link makes the cross-project dependency visible from both sides.
- **Submission & Artifacts table is mandatory and complete at ticket creation.** All five rows present. Use PLACEHOLDER explicitly when a value is pending upstream, never silently omit a row.
- **Spot-check method and acceptance criteria explicit in the Verification section.** Naming "spot-check the GUIDs" without a method or success criteria is a gap; the spot-check is the verified close trigger and needs to be reproducible by anyone reading the ticket.
- **Rendering-safe authoring patterns**: section headers use `### **Title**` Markdown form (round-trips cleanly to `h3.` Jira-wiki); bullet lists with italic labels use `* *Label*: content` (italic label, colon separator), NOT `- **Label:** content` (bold-and-colon, which the converter collapses to broken `**...:*` mismatched-asterisk damage); tables use Jira-wiki `||header||` syntax, NOT GitHub-flavored Markdown `|h|h|`. Verified on CTDC-2060 second push.
- **All four sections are required.** There are no optional sections; every registration ticket carries Registration Summary, Submission & Artifacts, Registration Workflow, and Verification, in that order.

**Writing-and-publishing workflow**

1. Confirm the upstream artifacts exist before drafting the registration ticket. If the Release Package isn't generated yet in `nci-cbiit-clinicaltrialdatacommons-metadata`, or the Object Files aren't in `nci-crdc-data-bucket-prod`, or the consent code isn't confirmed, the registration ticket is premature; those upstream items should land first. **Surface any open questions on the parent submission user story, not on the Task.**
2. Confirm this work is IndexD registration, not data loading or modeling. If the work is promoting an existing-and-registered submission through Jenkins, use the Data Loading Task template (Section 7e). If the schema is changing, use a modeling template (Section 7f or 7g).
3. **Identify the parent submission user story and the study-specific Data Hub tracker.** Every CTDC data submission has a program-level user story (e.g., CTDC-1805) and per-study Data Hub tracking tickets (e.g., DHDM-143 for AHEP0731). Both go on the ticket via `Relates` links after creation.
4. Confirm the paired Data Loading Task exists (or will exist). The two tickets are paired by design and run **in parallel**; a registration without a paired load is unusual and should be questioned.
5. Create the IndexD registration task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, the parent epic linked via `customfield_12350` in `additional_fields` (default: CTDC-1664), and the `Data-Concierge` label via the `labels` field. **Leave the ticket Unassigned** per standing convention; the TPM still owns the external CRINTAKE coordination, but that ownership is tracked through comments and the CRINTAKE remote link, not the assignee field.
6. Push the full description in a second call via `jira_update_issue` with the full Markdown body. Same two-step pattern as the Data Loading Task and Features templates.
7. Add `Relates` links from the registration ticket to the parent submission user story AND the study-specific DHDM tracker using `jira_create_issue_link`. Order: registration ticket as inward issue.
8. Add a `Relates` link from the registration ticket to the paired Data Loading Task using `jira_create_issue_link` (registration as inward issue, load as outward issue). Do **not** use `Blocks`; the two run in parallel.
9. Verify the rendered description with a UI screenshot.
10. As the workflow progresses, add the CRINTAKE remote link via `jira_create_remote_issue_link` once step 3 of the workflow is complete.
11. After GUID spot-check passes (Verification section), transition the ticket to Closed with resolution `Fixed`. **GUID spot-check success is the close trigger.**

**When to expand vs trim**

- **Standard single-study registration** → use the template as written; expect 4 sections present.
- **Multi-manifest registration** (one study, multiple manifest files) → expand the Submission & Artifacts table with manifest-specific rows, or add a row per manifest; expand the workflow step 2 to enumerate each manifest by filename. Keep one ticket; the CRINTAKE intake is one unit of work.
- **Re-registration after a file correction** → use the template as written; explain the reason for re-registration in a Jira comment and reference IndexD's `baseid` / `rev` versioning model.

**When NOT to use this template**

The CTDC team has two primary functions: software development and data management. Data management has two sub-functions: loading data and modeling data. Within loading data, there are two work patterns: *promoting a CRDC submission's contents into CTDC's databases* (Data Loading Task, Section 7e) and *creating upstream artifacts that the load consumes* (this template). This template covers the IndexD registration work pattern only.

**Data loading**: does NOT use this template. Use the **Data Loading Task** template (Section 7e) instead. That sibling template covers the actual end-to-end promotion of metadata through Dev → QA → Stage → Prod after IndexD has minted the GUIDs.

**Data modeling**: does NOT use this template. Use the **Data Modeling for Study Submission** template (Section 7g) for study-driven model additions, or the **Data Model Update Task** template (Section 7f) for infrastructure-level model changes.

**Software development work**: does NOT use this template. Use the appropriate template from the software development family.

**Other upstream artifact creation work**: partially overlaps, and some now has its own template. Megazip creation (bundling all of a study's object files into one downloadable archive, then indexing and loading it) uses the **Megazip Creation Task** template (Section 7i), built from this template and the Data Loading Task (Section 7e) as its closest siblings. Standalone metadata loading file creation that is not already part of a megazip or load task still has no dedicated template; file it as a standalone Task under CTDC-1664 (CTDC Data Integration) with a free-form description and link it from the Data Loading Task via the native Jira mechanism. If a recurring pattern emerges, draft a template using the closest sibling.

**CRDC platform changes**: Fence, IndexD, Submission Portal upgrades owned by CRDC platform teams. Out of CTDC scope entirely; CTDC files dependency tickets if affected, but does not own the work.

**Canonical example**

**CTDC-2060**: *Index NCTN-NCORP TCIA Images-Only AHEP0731 Files* (drafted 2026-05-26; **aligned to v5 on 2026-06-04**, along with the 11 paired NCTN-NCORP Index tickets CTDC-2072–2092, even). The ticket carries:

- 4 sections in the standard order (Registration Summary, Submission & Artifacts, Registration Workflow, Verification)
- 5-row Submission & Artifacts table (CRDC Submission ID, AWS Account ID, AWS S3 Bucket, Release Package, Sample GUID), carrying the study's real release-package directory and minted sample GUID
- 5-step workflow grouped Pre-registration / External handoff
- `Relates` links to CTDC-1805 (program-level user story) and DHDM-143 (study-specific Data Hub tracker), set via Jira native Links panel, not duplicated in description
- Parent Epic CTDC-1664 set via `customfield_12350`
- Open questions and risks for the broader submission live on **CTDC-1805's Open Questions / Risks section**, not on CTDC-2060

The retrofit recommendation that existed in v1 (against CTDC-1907) has been removed; CTDC-1907 is a different TCIA submission lineage (CMB, not Images-Only) and was never going to be the right canonical anchor for this template. It is retained in this template's antipattern notes for historical context only.
