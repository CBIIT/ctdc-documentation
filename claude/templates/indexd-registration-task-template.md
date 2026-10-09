### DO-INDEX. 🔖 IndexD Registration Task Template (v8)

> **Parent change (2026-09-25).** Each study submission is now tracked as a **submission epic** (Section DO-EPIC, `data-submission-epic-template.md`), which replaces the Data Submission user story (DO-STORY, retired) and the Data Integration epic (CTDC-1664) as the default parent. CTDC-1664 stays open as the standing epic for cross-study integration work that no single submission owns. Wherever this template says "parent submission user story" or "parent user story", read **submission epic**: this task is a child of that epic via the Epic Link (`customfield_12350`), not a `Relates` link, and the epic is the home for study identity, open questions, and risks. The title token is `<Program Short Name> <Study Short Name> <version>`, omitting parts that do not apply (for example `CMB v6`, `NCTN AHOD0831`).


> **Use this template for every CTDC data management task that registers a study's files in CRDC IndexD: minting GUIDs that the paired Data Loading Task will reference.** The canonical example is **CTDC-2060** (Index NCTN-NCORP TCIA Images-Only AHEP0731 Files), drafted 2026-05-26 as the first Sprint 28 ingestion ticket. CTDC-2060 was authored under this template's v1, iterated to v2 shape directly in Jira, then refined to v3 when the team formalized the principle that **Open Questions / Risks belong on the parent user story, not on the Task**. **v4 (2026-06-01)** corrects three conventions: the IndexD registration (Index) task and its Data Loading task are linked with **`Relates` and run in parallel**: IndexD registration does **not** block the load; the artifacts table row is **`Release Package`** (constant bucket plus a placeholder directory), not `Release Package Location`; and the registration (Index) task carries the **`Data-Concierge`** label. **v5 (2026-06-03)** restructures the Submission & Artifacts table to mirror the Data Loading Task's shape: the constant **AWS Account ID** and **AWS S3 Bucket** are split into their own rows, **Release Package** holds the directory name only, and the spot-check anchor is renamed **Sample GUID**; the **Consent group / ACL value** and **Object Files Location** rows are removed because both are captured in the indexd.tsv manifest itself (the `acl` and `url` columns), so restating them in the table was redundant. **v6 (2026-06-04)** slims the task to **four sections**: the Registration Summary is reduced to a single sentence (consent codes and on-hold status are visible in Jira statuses and on the parent user story, not restated here), the Workflow drops the *Confirmation and verification* phase (the spot-check lives wholly in the Verification section now), and the **Notes** section is removed entirely (terminology and historical context are not task-level content). **v7 (2026-06-11)** replaces em-dash separators throughout with colons for label lead-ins and semicolons/commas for clause joins; the rendering-safe bullet convention is now `* *Label*: content` (italic label, colon separator). This template covers the **upstream artifact creation** work pattern within the loading-data sub-function; it is the **parallel partner** of a Data Loading Task (Section DO-LOAD), not a substitute for one, and not a blocker of one. See "When NOT to use this template" at the end.

**Why this template**

The CTDC team has two primary functions (software development and data management), and data management has two sub-functions: loading data and modeling data. The Data Loading Task template (Section DO-LOAD) covers the *promotion of a CRDC submission's contents* into CTDC's databases through Jenkins. But before that load can run, every file in the submission needs a globally unique identifier (GUID) registered in **CRDC IndexD**, and that registration is performed by an **external team at the University of Chicago Center for Translational Data Science (CTDS)**, not by the CTDC engineering team.

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

Each section header is a Jira wiki `h3.` heading using the emoji + bold title format shown. Author the description in Jira wiki markup (see SKILL.md 7b-shared, "Authoring format"). Keep the sections in the order shown so a reader scanning multiple registration tickets sees the same visual flow. All examples below are shown in Jira wiki markup, which is what gets pushed.

1. `h3. 🎯 *Registration Summary*`: **Two sentences.** First, what's being indexed and the paired Data Loading Task this registration runs in parallel with. Second, a **bold new-vs-existing study marker**: state whether this is a new study's first registration or an existing study's data update, with the version. **Do not restate anything else** (study identity, consent codes, dbGaP IDs, submission chronology, submission count, on-hold status): all of that lives on the submission epic and is visible in Jira statuses. New-study example: *"Register the NCTN-NCORP TCIA Images-Only AHEP0731 study files in CRDC IndexD, minting GUIDs for the object files that the paired Data Loading Task will reference. *AHEP0731 is a new CTDC study; this is its first registration.*"* Existing-study example: *"Register the CMB v6 object files in CRDC IndexD, minting the GUIDs that the paired Data Loading Task will reference. *CMB is an existing CTDC study; this is a data update (v6), not a new study registration.*"*

2. `h3. 📦 *Submission & Artifacts*`: Required. Two bare-value constant bullets, then a table with **one row per CRDC submission** in the data update. No intro sentence, no explanatory text on the constants. Study identity (program, study name, dbGaP, submitter, chronology) lives on the submission epic, not here.

   ```
   * *AWS Account ID*: {{101183076466}}
   * *Release Package bucket*: {{nci-cbiit-clinicaltrialdatacommons-metadata}}

   ||Submission||CRDC Submission ID||Release Package||Index?||Sample GUID||Intake batch||
   |CMB v6 Clinical|<submission-id>|[<timestamp>-<submission-id>/|<S3 console URL>]|Yes|dg.4DFC/<guid>|Batch 1|
   |CMB v6 Imaging|<submission-id>|[<timestamp>-<submission-id>/|<S3 console URL>]|Yes|dg.4DFC/<guid>|Batch 2|
   |Study metadata update|<submission-id>|[<timestamp>-<submission-id>/|<S3 console URL>]|No, no indexing required|N/A|N/A|
   ```

   **Columns:**
   * *Submission*: the submission's name in the CRDC Submission Portal. This is the human handle everyone uses; it is the first column on purpose. If the Portal name is unknown, use a descriptive label and confirm it with the Data Concierge.
   * *CRDC Submission ID*: issued by the CRDC Submission Portal. Plain text, no `{{ }}`.
   * *Release Package*: the directory name within the Release Package bucket, rendered as a clickable S3-console link (see below). The directory contains that submission's indexd.tsv manifest. Until the study is released from the CRDC Submission Portal the directory does not exist, so use PLACEHOLDER.
   * *Index?*: `Yes` when the submission carries object files to register; `No, no indexing required` for metadata-only submissions. Metadata-only submissions still get a row so the table accounts for every submission in the update; the paired Data Loading Task still loads them.
   * *Sample GUID*: the spot-check anchor for that submission's manifest, used in Verification. Fill in once minted (the CRDC Submission Portal pipeline assigns GUIDs ahead of registration); PLACEHOLDER until then; `N/A` for Index = No.
   * *Intake batch*: which CRINTAKE handoff covered this submission (`Batch 1`, `Batch 2`, ...). Use batch numbers, not CRINTAKE keys; the CRINTAKE tickets are carried by the native Links panel. `N/A` for Index = No.

   **Clickable Release Package links**: once the directories exist, render each as a Jira wiki link `[<timestamp>-<submission-id>/|<url>]`. A wiki link inside a table cell renders correctly on this instance. URL format:

   ```
   https://us-east-1.console.aws.amazon.com/s3/buckets/nci-cbiit-clinicaltrialdatacommons-metadata?region=us-east-1&prefix=<URL-ENCODED-DIR>/&showversions=false
   ```

   The `prefix=` value is the same directory URL-encoded (colons as `%3A`).

   **Why one row per submission** (the v8 change, adopted from ICDC's v4): a field/value table puts every submission's values in one cell and relies on matching positions across rows. ICDC hit that on ICDC-4193 when a late-arriving submission had nowhere to go and hardcoded counts went stale. A row per submission makes each submission self-contained and makes a late arrival a single new row.

   **Not in the table**: "GUID prefix" (always `dg.4DFC/` for CRDC, implicit; mention only if the study uses a non-standard prefix); "indexd.tsv manifest path" (it's part of the Release Package); "CTDC Data Model version" (belongs on the Data Loading Task); "Object Files Location" (the manifest's `url` column already points to the object files); "Consent group / ACL value" (the manifest's `acl` column carries it on every row, and the dbGaP Validation Task (DO-DBGAP) has already reconciled it).

3. `h3. 🚦 *Registration Workflow*`: Numbered `#` list grouped into three phases, each introduced by a bold label (`*Pre-registration*`). Put a blank line after the section heading and after each bold phase label so the numbered lists render cleanly. Never hardcode a submission or manifest count; refer to "each submission marked Index = Yes" so the table stays the single source of truth. Standard CTDC sequence:

   **Pre-registration**
   1. For each submission marked Index = Yes, extract the indexd.tsv manifest from its Release Package in `nci-cbiit-clinicaltrialdatacommons-metadata`. Validate that every row carries a non-empty `acl` value and that the `acl` is uniform across the manifest, the row count matches the file count, every row's `url` is well-formed, and the GUID placeholder format uses the `dg.4DFC/` prefix.

   **External handoff**
   2. Upload each manifest to the DCF Google Drive folder for indexing; preserve the Release Package filenames, do not rename.
   3. File a CRINTAKE intake ticket on the CRDC CRs_INTAKE board describing the data release, naming every manifest in the batch, and any requested due date. A submission that arrives after an intake ticket is filed goes on a new intake ticket; record the batch in the Intake batch column.
   4. Link each CRINTAKE ticket back to this ticket as a Jira link.
   5. If a due date is communicated, notify both the NCI CRDC (Leidos) PM and the NCI DCFS PM as early as possible.

   **Confirmation & verification**
   6. Monitor each CRINTAKE intake ticket to track progress; its status is how we know when CTDS/DCFS has completed indexing that batch.
   7. Once every batch is complete, run the GUID spot-checks (see Verification). Passing spot-checks are the trigger to close this ticket.

   **Step count: 7 (1 pre-registration, 4 external handoff, 2 confirmation & verification).** This is the only place the close trigger is stated.

4. `h3. 🧪 *Verification*`: How CTDC confirms the registration worked. Bullet list of labeled lines (`* *Label*: content`):

   * *Spot-check method*: for every row marked Index = Yes, resolve its Sample GUID at `https://nci-crdc.datacommons.io/index/<guid>` and paste the returned IndexD record into a comment on this ticket, labeled with the submission name. A pass returns the record with `urls` pointing to the expected object-files location, a non-empty `acl`, and non-empty `size` and `hashes`.
   * *Why one per submission*: each submission has its own manifest, so a pass on one manifest says nothing about the others.
   * *If a check fails*: do not close. Reopen the relevant CRINTAKE ticket with the GUID and the resolution-endpoint response, coordinate the fix with CTDS/DCFS, and surface the issue on the submission epic.

   **Timing note (for the assignee, not the ticket body):** a GUID spot-check resolves only after the submission is marked Complete in the CRDC Submission Portal, which is when the object files move to the production bucket. Registration can be handed off as soon as the Release Package exists, but run the spot-checks after Complete.

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
| Verification | 🧪 *(shared with Data Loading Task; scoped to GUID resolution spot-checks)* |

**Required content rules**

- **Scope is IndexD registration only.** Minting GUIDs for files via the external CTDS/DCFS handoff. **Data loading** uses the Data Loading Task template (Section DO-LOAD). **Schema or model changes** use the Data Modeling for Study Submission template (Section DO-MODEL) or the Data Model Update Task template (Section DO-INTMODEL). See "When NOT to use this template" below.
- **No Acceptance Criteria section.** IndexD registration is operational SOP work; the completion bar is the GUID spot-check passing (Section 4). AC belongs on user stories, not on Tasks.
- **No Open Questions / Risks section.** Open questions and risks live on the parent submission user story (e.g., CTDC-1805), not on this Task. If a question or risk surfaces during the registration work, raise it as a bullet under the parent user story's Open Questions / Risks section so it's tracked at the program level. The Task description carries only what the assignee needs to execute.
- **Registration Summary carries the new-vs-existing study marker.** Second sentence, bold, stating new study (first registration) versus existing study (data update, with version).
- **Non-vital scope detail goes in a ticket comment, not the description.** File counts, file-type breakdowns, and other study-content context are useful background but are not indexing steps; post them as a plain-text comment. If a submission is added later, update that comment's counts too.
- **One Task per study data update.** All submissions for one study version go on one registration ticket, one table row each. CRINTAKE intake tickets are one per handoff batch; a single registration ticket can carry several. If a single Data Loading Task depends on registrations for two different studies, file two registration tickets and link the load ticket from both via `Relates`.
- **Issue type is Task** on this tracker, matching the convention used on CTDC-2060. Do not use Story or Subtask.
- **Title convention:** `Data Indexing: <Program Short Name> <Study Short Name> <version>`: reuse the exact token from the submission epic title (Section DO-EPIC) verbatim, for example `Data Indexing: CMB v6`. The board title reads "Data Indexing"; the underlying activity is IndexD registration, called out in the task body.
- **Parent Epic field set via `customfield_12350`** to the study's submission epic (Section DO-EPIC). CTDC-1664 is no longer the default.
- **`Data-Concierge` label is mandatory on this registration (Index) task.** Indexing is performed by the Data Concierge, so the IndexD Registration task carries the `Data-Concierge` label, set at creation via the `labels` field. The paired Data Loading task carries **no** label; the load is performed by engineering.
- **Leave the ticket Unassigned at creation.** Per standing team convention, newly created tickets are left Unassigned unless an assignee is explicitly directed. Data management tasks (IndexD Registration, Data Loading) also do not require the Developer field.
- **Sprint: always add the ticket to the standing `CTDC Data Related` sprint at creation** (board 641, sprint id 8612; a permanent future-state sprint that holds all data-operations work). Never leave it in the backlog and never place it in a numbered development sprint; pass the new key to `jira_add_issues_to_sprint` right after creation.
- **The submission epic is the parent.** It is the canonical record of study identity and the home for open questions and risks; the Task description does not duplicate that content. No `Relates` link to a user story.
- **`Relates` link to the study-specific Data Hub tracking ticket (DHDM-XXX) when one exists.** Set via `jira_create_issue_link` after creation.
- **`Relates` link to the paired Data Loading Task is mandatory** when that load ticket exists. The registration and the load run **in parallel**: IndexD registration does **not** block the load (metadata can load before GUIDs are minted; file downloads resolve once this registration's GUID spot-check passes). Do **not** use a `Blocks` link between them. Pass the registration ticket as the inward issue, the load ticket as the outward issue. If the load ticket has not been filed yet at registration ticket creation time, add the `Relates` link as soon as the load ticket exists.
- **Remote link to the CRINTAKE ticket is mandatory** once workflow step 4 is complete. Use `jira_create_remote_issue_link` with the CRINTAKE ticket URL. A free-text reference to the CRINTAKE ticket key is **not** sufficient; the remote link makes the cross-project dependency visible from both sides.
- **Submission & Artifacts table is mandatory and complete at ticket creation.** One row per submission in the update, every column populated. Use PLACEHOLDER explicitly when a value is pending upstream, never leave a cell blank.
- **Spot-check method and acceptance criteria explicit in the Verification section.** The spot-checks are the verified close trigger and need to be reproducible by anyone reading the ticket.
- **Description format is Jira wiki markup, authored directly** (connector translation is off; see SKILL.md 7b-shared). Patterns: headings `h3. 🎯 *Title*`; bold `*text*`; labeled lines `* *Label*: content`; inline code `{{value}}`; numbered lists `#`; bullets `*`; links `[text|url]`; tables `||header||` and `|cell|`. Put a blank line after every heading, between a bold phase label and its numbered list, and between sections. Never use an in-cell line break inside a table. After pushing, read the description back and confirm it starts with `h3.`, not `###`.
- **All four sections are required.** There are no optional sections; every registration ticket carries Registration Summary, Submission & Artifacts, Registration Workflow, and Verification, in that order.

**Writing-and-publishing workflow**

1. Confirm the upstream artifacts exist before drafting the registration ticket. If the Release Package isn't generated yet in `nci-cbiit-clinicaltrialdatacommons-metadata`, or the Object Files aren't in `nci-crdc-data-bucket-prod`, or the consent code isn't confirmed, the registration ticket is premature; those upstream items should land first. **Surface any open questions on the parent submission user story, not on the Task.**
2. Confirm this work is IndexD registration, not data loading or modeling. If the work is promoting an existing-and-registered submission through Jenkins, use the Data Loading Task template (Section DO-LOAD). If the schema is changing, use a modeling template (Section DO-INTMODEL or DO-MODEL).
3. **Identify the submission epic and the study-specific Data Hub tracker.** The epic is set as the Epic Link at creation; the DHDM tracker (e.g., DHDM-143 for AHEP0731) goes on via a `Relates` link after creation.
4. Confirm the paired Data Loading Task exists (or will exist). The two tickets are paired by design and run **in parallel**; a registration without a paired load is unusual and should be questioned.
5. Create the IndexD registration task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, the parent epic linked via `customfield_12350` in `additional_fields` (the submission epic), and the `Data-Concierge` label via the `labels` field. **Leave the ticket Unassigned** per standing convention; the TPM still owns the external CRINTAKE coordination, but that ownership is tracked through comments and the CRINTAKE remote link, not the assignee field.
6. Push the full description in a second call via `jira_update_issue`, authored in Jira wiki markup and passed as `{"description": "..."}` in `additional_fields`. Read it back to confirm it stored as wiki markup.
7. Add the ticket to the `CTDC Data Related` sprint (id 8612) via `jira_add_issues_to_sprint`.
8. Add a `Relates` link from the registration ticket to the study-specific DHDM tracker using `jira_create_issue_link`. Order: registration ticket as inward issue.
9. Add a `Relates` link from the registration ticket to the paired Data Loading Task using `jira_create_issue_link` (registration as inward issue, load as outward issue). Do **not** use `Blocks`; the two run in parallel.
10. Post the data-update context comment (file counts, file-type breakdown) as plain text via `jira_add_comment`, then verify the rendered description with a UI screenshot.
11. As the workflow progresses, add the CRINTAKE remote link via `jira_create_remote_issue_link` once each intake ticket is filed, and add a table row (with the next batch number) for any late-arriving submission.
12. After every indexed submission's spot-check passes, transition the ticket to Closed with resolution `Fixed`.

**When to expand vs trim**

- **Standard single-submission registration** → the table has one row; everything else as written.
- **Multi-submission registration** (one study version, several submissions / manifests) → one row per submission, including metadata-only submissions marked Index = No. Keep one ticket.
- **Late-arriving submission** (after an intake ticket is filed) → add a row with the next batch number, file a new CRINTAKE ticket for it, link it, and update the context comment's counts. Do not open a second registration ticket.
- **Re-registration after a file correction** → use the template as written; explain the reason for re-registration in a Jira comment and reference IndexD's `baseid` / `rev` versioning model.

**When NOT to use this template**

The CTDC team has two primary functions: software development and data management. Data management has two sub-functions: loading data and modeling data. Within loading data, there are two work patterns: *promoting a CRDC submission's contents into CTDC's databases* (Data Loading Task, Section DO-LOAD) and *creating upstream artifacts that the load consumes* (this template). This template covers the IndexD registration work pattern only.

**Data loading**: does NOT use this template. Use the **Data Loading Task** template (Section DO-LOAD) instead. That sibling template covers the actual end-to-end promotion of metadata through Dev → QA → Stage → Prod after IndexD has minted the GUIDs.

**Data modeling**: does NOT use this template. Use the **Data Modeling for Study Submission** template (Section DO-MODEL) for study-driven model additions, or the **Data Model Update Task** template (Section DO-INTMODEL) for infrastructure-level model changes.

**Software development work**: does NOT use this template. Use the appropriate template from the software development family.

**Other upstream artifact creation work**: partially overlaps, and some now has its own template. Megazip creation (bundling all of a study's object files into one downloadable archive, then indexing and loading it) uses the **Megazip Creation Task** template (Section DO-ZIP), built from this template and the Data Loading Task (Section DO-LOAD) as its closest siblings. Standalone metadata loading file creation that is not already part of a megazip or load task still has no dedicated template; file it as a standalone Task under the study's submission epic (Section DO-EPIC) with a free-form description and link it from the Data Loading Task via the native Jira mechanism. If a recurring pattern emerges, draft a template using the closest sibling.

**CRDC platform changes**: Fence, IndexD, Submission Portal upgrades owned by CRDC platform teams. Out of CTDC scope entirely; CTDC files dependency tickets if affected, but does not own the work.

**Canonical example**

> **v8 note:** CTDC-2060 predates v8 and still carries the five-row table and 5-step workflow. The first CTDC registration drafted under v8 becomes the canonical example; until then, ICDC-4194 (ICDC IndexD v4, same shape) is the closest reference for the per-submission table.

**CTDC-2060**: *Index NCTN-NCORP TCIA Images-Only AHEP0731 Files* (drafted 2026-05-26; **aligned to v5 on 2026-06-04**, along with the 11 paired NCTN-NCORP Index tickets CTDC-2072–2092, even). The ticket carries:

- 4 sections in the standard order (Registration Summary, Submission & Artifacts, Registration Workflow, Verification)
- 5-row Submission & Artifacts table (CRDC Submission ID, AWS Account ID, AWS S3 Bucket, Release Package, Sample GUID), carrying the study's real release-package directory and minted sample GUID
- 5-step workflow grouped Pre-registration / External handoff
- `Relates` links to CTDC-1805 (program-level user story) and DHDM-143 (study-specific Data Hub tracker), set via Jira native Links panel, not duplicated in description
- Parent Epic CTDC-1664 set via `customfield_12350` at drafting (before 2026-09-25; new tasks take the study's submission epic)
- Open questions and risks for the broader submission live on **CTDC-1805's Open Questions / Risks section**, not on CTDC-2060

The retrofit recommendation that existed in v1 (against CTDC-1907) has been removed; CTDC-1907 is a different TCIA submission lineage (CMB, not Images-Only) and was never going to be the right canonical anchor for this template. It is retained in this template's antipattern notes for historical context only.


**Changelog**

- **2026-10-09 v8** (aligned with ICDC IndexD v4): Submission & Artifacts is now two constant bullets plus **one row per submission** (Submission, CRDC Submission ID, Release Package, Index?, Sample GUID, Intake batch), replacing the five-row field/value table; the Registration Summary gains a bold new-vs-existing study marker; the workflow gains a Confirmation & verification phase (7 steps) that holds the close trigger; Verification spot-checks one Sample GUID per indexed submission; file counts move to a plain-text comment; headers and examples are authored in Jira wiki markup.
- **2026-09-25 parent change** (no version bump unless noted): tasks from this template are children of the study's submission epic (DO-EPIC) via `customfield_12350`; the Data Submission user story (DO-STORY) is retired and CTDC-1664 is no longer the default parent; it stays open as the standing epic for cross-study integration work. `Relates` links to the parent user story are replaced by the Epic Link. Title token is `<Program Short Name> <Study Short Name> <version>`.
- **2026-09-17 sprint rule** (no version bump): every ticket from this template goes into the standing `CTDC Data Related` sprint (board 641, id 8612) at creation, per the TPM on 2026-09-17. Applies to the whole DO-* family.
