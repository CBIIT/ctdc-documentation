### DO-ZIP. 🗜️ Megazip Creation Task Template (v3)

> **Parent change (2026-09-25).** Each study submission is now tracked as a **submission epic** (Section DO-EPIC, `data-submission-epic-template.md`), which replaces the Data Submission user story (DO-STORY, retired) and the Data Integration epic (CTDC-1664) as the default parent. CTDC-1664 stays open as the standing epic for cross-study integration work that no single submission owns. Wherever this template says "parent submission user story" or "parent user story", read **submission epic**: this task is a child of that epic via the Epic Link (`customfield_12350`), not a `Relates` link, and the epic is the home for study identity, open questions, and risks. The title token is `<Program Short Name> <Study Short Name> <version>`, omitting parts that do not apply (for example `CMB v6`, `NCTN AHOD0831`).


> **Use this template for every CTDC data management task that bundles a released study's object files into one megazip per data file type, self-mints a GUID for each, hands the manifest to CTDS for indexing, and loads the megazips through Dev → QA → Stage → Prod so each file type downloads as one file from the Study Details page.** Canonical examples are **CTDC-2220** (AHOD0831) and **CTDC-2221** (S0819), the first tickets on the v3 shape; **CTDC-2104** (AHEP0731) is the v2 ancestor. This step always runs **after** the study's dbGaP Validation (DO-DBGAP) and IndexD Registration (DO-INDEX) tasks, so the release package is already known from those linked tickets. See "When NOT to use this template" at the end.

**Why this template**

A megazip is a single `.zip` bundling all of a study's object files **of one data file type**, created **in addition to** the individual files (which stay in the bucket). A study with more than one `data_file_type` in its release package gets one megazip per type, all on one ticket. The megazip exists so a user can download a whole file type for a study in one click from the Study Details page.

The work is three operations the team already runs separately, on one ticket: **Create** (a Prefect job writes each megazip into the study's object-files directory), **Index** (the team self-mints a `dg.4DFC/` GUID per megazip, authors one `indexd.tsv`, and hands it to CTDS through the standard DCF Google Drive + CRINTAKE path), and **Load** (the per-tier Jenkins data-loading jobs promote the megazip metadata Dev → QA → Stage → Prod, recorded in a Testing Signoff table).

**Design principle (v3): the ticket carries only what the assignee needs to type.** Earlier versions repeated program-level context, constants nobody uses (the AWS account ID), and cross-references to sibling tickets. People stopped reading them. A v3 ticket is short enough to read top to bottom, and the one step people get wrong (finding the object-files directory, which lives in a different bucket from the release package) is spelled out with an example. Open questions and risks stay on the parent submission user story.

**The two buckets (read once before drafting)**

- **Release package**: lives in the **metadata bucket** `nci-cbiit-clinicaltrialdatacommons-metadata`, in a directory named `<timestamp>-<submission-id>/`. It holds `file.tsv` (metadata, including the `data_file_type` column) and `indexd.tsv` (the manifest whose `urls` column points at the real files). The directory is created when the study is released from the CRDC Submission Portal and is recorded on the study's DO-DBGAP ticket (and usually its DO-INDEX ticket); **copy it from there rather than asking**.
- **Object files**: live in the **data bucket** `nci-crdc-data-bucket-prod`, in a directory named by a UUID. The directory is **not** written anywhere except inside `indexd.tsv`: each `urls` value looks like `s3://nci-crdc-data-bucket-prod/663e6a44-f212-4673-a2ae-af2854557e3f/<file>.zip`, and the directory is the segment after the bucket name. The megazips are written into this same directory, next to the individual files.
- **Megazip filename**: `<study>_<data_file_type>.zip`, spaces replaced by underscores, **no program prefix** (`AHOD0831_Radiology_Images.zip`, not `NCTN_AHOD0831_...`). `<study>` is the study short name from the submission epic; `<data_file_type>` is the value from `file.tsv`.
- **IndexD**: GUIDs are self-minted (UUID + `dg.4DFC/`), one per megazip; the manifest is authored by the team and handed off through the DCF Google Drive folder (`https://drive.google.com/drive/u/2/folders/1ZVsv2vFEcTPBT2IYsaOb_XCjpWjjMGTb`) and a CRINTAKE ticket (`tracker.nci.nih.gov/projects/CRINTAKE/`), exactly like every other CTDC file. Spot-check at `https://nci-crdc.datacommons.io/index/<GUID>`.
- **Loading**: one file-metadata loading file covering every megazip, run through the dedicated Jenkins data-loading job per tier (Dev, QA, Stage, Prod).

**Section order (4 sections, exactly this sequence)**

Each header is an `h3` Markdown heading in the emoji + bold form shown.

1. `### 🎯 **Summary**`: One or two sentences. Example: *"Create one megazip per data file type for AHOD0831, index each, and load them Dev → QA → Stage → Prod so each file type downloads as one file from the Study Details page. The individual files stay in the bucket."*

2. `### 📦 **Artifacts**`: Two Jira-wiki tables. The first holds the three study-level values; the second has one row per `data_file_type` and is filled in as the work progresses.

   ||Field||Value||
   |CRDC Submission ID|`<submission-id>`|
   |Release Package (metadata bucket)|`s3://nci-cbiit-clinicaltrialdatacommons-metadata/<timestamp>-<submission-id>/`|
   |Object Files Directory (data bucket)|`s3://nci-crdc-data-bucket-prod/`PLACEHOLDER (Workflow step 1)|

   One row per distinct `data_file_type` in `file.tsv` (Workflow step 2):

   ||data_file_type||Megazip File||GUID||md5 / size||
   |PLACEHOLDER|`<study>_<data_file_type>.zip`|TBD| |

3. `### 🚦 **Workflow**`: Four phases, Markdown `1.` ordered lists under an italic phase label (numbering restarts per phase).

   *Read the release package*
   1. The release package lives in the metadata bucket (`nci-cbiit-clinicaltrialdatacommons-metadata`); the object files live in a different bucket (`nci-crdc-data-bucket-prod`). Open `indexd.tsv` in the release package and look at the `urls` column. Each row looks like `s3://nci-crdc-data-bucket-prod/663e6a44-f212-4673-a2ae-af2854557e3f/<file>.zip`; the directory is the segment after the bucket name (`663e6a44-f212-4673-a2ae-af2854557e3f` in that example). Record it in the Artifacts table.
   2. Open `file.tsv` in the same release package and list the distinct `data_file_type` values. There is one megazip per value, named `<study>_<data_file_type>.zip` with spaces replaced by underscores (for example `AHOD0831_Radiology_Images.zip`). Add one row per value to the megazip table. No program prefix in the name.

   *Create*
   1. For each data file type, run the Prefect megazip job against the Object Files Directory. It writes the megazip into that same directory; the individual files stay in place.
   2. Record each megazip's md5sum and size in the megazip table.
   3. Author one file-metadata loading file covering every megazip (CRDC Submission Portal CLI format).

   *Index*
   1. For each megazip, generate a UUID, prepend `dg.4DFC/`, and record the GUID in the megazip table.
   2. Author one `indexd.tsv` with a row per megazip (GUID, size, md5, url) and upload it to the [DCF Google Drive folder](https://drive.google.com/drive/u/2/folders/1ZVsv2vFEcTPBT2IYsaOb_XCjpWjjMGTb).
   3. File a [CRINTAKE](https://tracker.nci.nih.gov/projects/CRINTAKE/) ticket naming the manifest, and link it back to this ticket.
   4. When CTDS reports done, resolve each GUID at `https://nci-crdc.datacommons.io/index/<GUID>`. A pass returns a record whose `urls` points at that megazip and whose `size` and `hashes` are non-empty. If any fails, comment on the CRINTAKE ticket and do not load.

   *Load*
   1. Run the Jenkins *Dev* data-loading job with the megazip loading file. Confirm every megazip shows on the Study Details page.
   2. Run *QA*. Tester confirms each megazip downloads from the Study Details page.
   3. Run *Stage*. Tester confirms download.
   4. Run *Prod*. Tester confirms download. *Prod signoff closes the ticket.*

4. `### ✅ **Testing Signoff**`: The completion record. **Prod signoff is the trigger to transition the ticket to Closed.**

   ||Environment||Date||Initials||
   |Dev| | |
   |QA| | |
   |Stage| | |
   |Prod| | |

**Standing emoji set (4 entries)**

| Section | Emoji |
|---|---|
| Summary | 🎯 |
| Artifacts | 📦 |
| Workflow | 🚦 |
| Testing Signoff | ✅ |

The separate 🧪 Verification section from v1/v2 is gone; the GUID spot-check is now Index step 4.

**Required content rules**

- **Scope is megazip creation, self-minted indexing, and loading only.** A plain study load is DO-LOAD; registering Portal-assigned GUIDs is DO-INDEX; consent-group validation is DO-DBGAP; schema changes are DO-MODEL / DO-INTMODEL.
- **One ticket per study, one megazip per `data_file_type`.** The megazip table has one row per type; the loading file and `indexd.tsv` each carry one row per megazip; Jenkins runs once per tier for all of them.
- **Filename is `<study>_<data_file_type>.zip`**, underscores for spaces, no program prefix.
- **Release package is inferred, not requested.** It is already recorded on the study's DO-DBGAP ticket (and usually DO-INDEX); copy it into the Artifacts table at creation. If neither carries it, the study has not been released and this ticket is premature.
- **Object files directory is derived from `indexd.tsv`** (Workflow step 1), never guessed from the release package name; the two buckets are different.
- **Title:** `Create, Index, and Load Megazip files for <token>`, where `<token>` is the submission epic's `<Program Short Name> <Study Short Name> <version>` (plural "files"; no data type in the title, since the types are not known until step 2).
- **Issue type Task; Parent Epic via `customfield_12350`** (the study's submission epic, Section DO-EPIC); **no label**; **Unassigned** at creation; priority Major to match the family.
- **Sprint: always add the ticket to the standing `CTDC Data Related` sprint at creation** (board 641, sprint id 8612; a permanent future-state sprint that holds data-operations work). Never leave it in the backlog and never place it in a numbered development sprint.
- **Links: `Relates` to every other task for the study** (DO-DBGAP, DO-INDEX, DO-LOAD, and the DHDM tracker; the submission epic is the parent via the Epic Link), plus a remote link to the CRINTAKE ticket once filed. **Never `Blocks`. No link to any feature user story.**
- **No AWS Account ID row, no "To be created" rows, no Notes column.** If a value is not typed into a command, a file, or a form, it does not belong in the table.
- **Rendering-safe authoring**: `### **Title**` headers (round-trip to `h3.`); Jira-wiki `||header||` tables; `{{monospace}}` inside cells; Markdown `1.` ordered lists (a leading wiki `#` becomes an `h1.` heading); links as `[text|url]`. The converter rewrites `<placeholder>` as `[placeholder]`, which is fine. Two-step create (`jira_create_issue` then `jira_update_issue`) and confirm the render in the UI.

**Writing-and-publishing workflow**

1. Confirm the study's DO-DBGAP ticket is Closed and carries the release package directory; copy it.
2. Create via `jira_create_issue` (Task, placeholder description, `customfield_12350`, priority Major, Unassigned, no label).
3. Push the body via `jira_update_issue`.
4. Add the ticket to the `CTDC Data Related` sprint (id 8612) via `jira_add_issues_to_sprint`.
5. Add `Relates` links to every study task and the DHDM tracker.
6. Verify the render in the UI.
7. As work progresses, fill the Object Files Directory, the per-type megazip rows (name, GUID, md5/size), then the Testing Signoff rows. Prod signoff closes the ticket.

**When NOT to use this template**

- **Loading a study's own release package** → DO-LOAD.
- **Registering Portal-assigned GUIDs** → DO-INDEX.
- **dbGaP consent-group validation** → DO-DBGAP.
- **Data modeling** → DO-MODEL or DO-INTMODEL.
- **Software development** → the software development template family.
- **CRDC platform changes** → owned by CRDC platform teams; out of CTDC scope.

**Canonical examples**

**CTDC-2220** (*Create, Index, and Load Megazip files for NCTN-NCORP AHOD0831*) and **CTDC-2221** (*... S0819*), created 2026-09-17: 4 sections, the two-table Artifacts block, a four-phase workflow starting with "Read the release package," release package copied from the sibling DO-DBGAP / DO-INDEX tickets, `Relates` links to CTDC-1805, the study's Index, Load, and dbGaP tickets, and the DHDM tracker; both placed in the `CTDC Data Related` sprint. **CTDC-2104** (AHEP0731) is the v2 ancestor and still carries the older 5-section shape.

**Changelog**

> _Numbering note: template IDs in older entries predate the 2026-07-23 renumber from legacy 7x letters to DO- codes; see the crosswalk in SKILL.md._

- **2026-09-25 parent change** (no version bump unless noted): tasks from this template are children of the study's submission epic (DO-EPIC) via `customfield_12350`; the Data Submission user story (DO-STORY) is retired and CTDC-1664 is no longer the default parent; it stays open as the standing epic for cross-study integration work. `Relates` links to the parent user story are replaced by the Epic Link. Title token is `<Program Short Name> <Study Short Name> <version>`. Title: `Create, Index, and Load Megazip files for <token>` (for example CTDC-2207, CMB v6).
- **v3 (2026-09-17)**: Lean rewrite driven by the TPM after assignees reported the v2 ticket was too long to read. One megazip **per `data_file_type`** on one ticket (v2 assumed one per study); filename `<study>_<data_file_type>.zip` with **no program prefix**; the release package is **inferred from the linked DO-DBGAP / DO-INDEX tickets**; the object-files directory lookup (different bucket, read from `indexd.tsv` `urls`) is an explicit first step with an example; 4 sections instead of 5 (Verification folded into Index step 4); Artifacts split into a three-row study table and a per-type megazip table; dropped the AWS Account ID row, the Notes column, and the "To be created" rows; **no feature user story link**, `Relates` to every study task instead; every ticket goes into the standing `CTDC Data Related` sprint (id 8612) at creation. Canonical examples CTDC-2220 and CTDC-2221.
- **v2 (2026-07-22)**: Corrected the indexing model: megazips are indexed through the external CTDS team via DCF Google Drive + CRINTAKE like every CTDC file; the only megazip-specific difference is the self-minted GUID and team-authored `indexd.tsv`. Expanded the Index phase to four steps; added md5sum/size and spot-check-failure guidance; added the CRINTAKE remote-link requirement.
- **v1 (2026-06-11)**: First version, built from the Data Loading and IndexD Registration templates with CTDC-2104 as the canonical example.
