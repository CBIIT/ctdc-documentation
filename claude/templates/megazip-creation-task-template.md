### DO-ZIP. 🗜️ Megazip Creation Task Template (v4)

> **Parent change (2026-09-25).** Each study submission is now tracked as a **submission epic** (Section DO-EPIC, `data-submission-epic-template.md`), which replaces the Data Submission user story (DO-STORY, retired) and the Data Integration epic (CTDC-1664) as the default parent. CTDC-1664 stays open as the standing epic for cross-study integration work that no single submission owns. Wherever this template says "parent submission user story" or "parent user story", read **submission epic**: this task is a child of that epic via the Epic Link (`customfield_12350`), not a `Relates` link, and the epic is the home for study identity, open questions, and risks. The title token is `<Program Short Name> <Study Short Name> <version>`, omitting parts that do not apply (for example `CMB v6`, `NCTN AHOD0831`).


> **Use this template for every CTDC data management task that bundles a released study's object files into one megazip per data file type, submits the megazip metadata to the CRDC Submission Portal as a metadata-only submission, and indexes the megazips, so a paired Data Loading task (DO-LOAD) can load them and each file type downloads as one file from the Study Details page.** The canonical example is **CTDC-2207** (CMB v6), paired with its load ticket **CTDC-2297**. This step always runs **after** the study's dbGaP Validation (DO-DBGAP) and IndexD Registration (DO-INDEX) tasks, so the release package is already known from those linked tickets. See "When NOT to use this template" at the end.

**Why this template**

A megazip is a single `.zip` bundling all of a study's object files **of one data file type**, created **in addition to** the individual files (which stay in the bucket). A study with more than one `data_file_type` in its release package gets one megazip per type, all on one ticket. The megazip exists so a user can download a whole file type for a study in one click from the Study Details page.

The work passes between two roles on one ticket: the **Data Concierge** prepares the file lists, **DevOps** generates the megazips with a Prefect job, and the **Data Concierge** submits the megazip metadata to the CRDC Submission Portal and indexes the megazips. **Loading is not on this ticket**: a megazip submission produces an ordinary release package, so it is loaded on a standard Data Loading task (DO-LOAD) titled `Data Loading: <token> Megazips`.

**Design principle (v4): one owner per section, one place for every value.** Each workflow section names its owner and ends with a handoff (mark the Handoff Log, reassign the ticket). Every value the work produces is recorded in this ticket's Artifacts table, never copied across tickets, so nothing is lost or mistyped in transit. The only values that leave this ticket are the Megazip Submission ID and Megazip Release Package, which go to the paired load ticket.

**Background (read once before drafting)**

- **Release package**: lives in the **metadata bucket** `nci-cbiit-clinicaltrialdatacommons-metadata-nonprod`, in a directory named `<timestamp>-<submission-id>/`. It holds `file.tsv` (metadata, including the `data_file_type` column) and `indexd.tsv` (the manifest whose `urls` column points at the real files). It is recorded on the study's DO-DBGAP ticket (and usually its DO-INDEX ticket); **copy it from there rather than asking**.
- **Object files**: live in the **data bucket** `nci-crdc-data-bucket-prod`, in a directory named by a UUID, read from the `urls` column of `indexd.tsv` (the segment after the bucket name). **This directory is shared**: the Submission Portal releases every study's object files into it, so it holds other studies' and other submissions' files. Never list the directory to decide what goes into a megazip; the file lists from `file.tsv` are the only scope.
- **File lists**: `file.tsv` from the release package, split into one file per distinct `data_file_type`, keeping the header and every column unchanged. Name each `<study>_<version>_<data_file_type>_megazip_file_list.tsv`.
- **Megazip filename**: `<study>_<data_file_type>_<version>.zip`, spaces replaced by underscores, **no program prefix** (for example `CMB_Variant_Call_Files_v6.zip`). The version keeps a new megazip from overwriting an earlier release's megazip in the shared directory: overwriting changes the bytes behind the old GUID while IndexD still holds the old md5 and size. Confirm no object with the name already exists before DevOps runs the job.
- **Submission**: the megazips are already in the data bucket when they are submitted, so the CRDC Submission Portal submission is **metadata-only**: submit the file-metadata loading file and no object files. The Portal releases it as its own release package with a `file.tsv` for loading and an `indexd.tsv` carrying a **Portal-assigned GUID** for each megazip. Do not self-mint GUIDs; that would create a second IndexD record for the same file. The Radiology Images megazip for CMB (Submission ID `194d4a7e-368e-4401-a1c7-786db4f2091a`) is a worked example of a metadata-only megazip loading file.
- **IndexD**: the Megazip Release Package's `indexd.tsv` is handed to CTDS through the DCF Google Drive folder (`https://drive.google.com/drive/u/2/folders/1ZVsv2vFEcTPBT2IYsaOb_XCjpWjjMGTb`) and a CRINTAKE ticket (`tracker.nci.nih.gov/projects/CRINTAKE/`), exactly like every other CTDC file. Spot-check at `https://nci-crdc.datacommons.io/index/<GUID>`.

**Section order (4 sections, exactly this sequence)**

Each header is a Jira wiki `h3.` heading in the emoji + bold form shown.

1. `h3. 🎯 *Summary*`: One or two sentences, then the standing "How this ticket works" line. Example: *"Bundle each CMB v6 data file type into one megazip, submit its metadata through the CRDC Submission Portal, and index it, so the linked loading ticket can load it and each file type downloads as one file from the Study Details page. The individual files stay in the bucket."* Then, verbatim: *"How this ticket works: each workflow section has one owner. Read your inputs from the Artifacts table, record your outputs there (never on another ticket), mark your row in the Handoff Log, and reassign the ticket to the next owner. The assignee is always the person holding the work."*

2. `h3. 📦 *Artifacts*`: Two Jira-wiki tables. The first holds the study-level and submission values with a **Filled by** column; the second has one row per megazip and is filled in as the work progresses, followed by a one-line italic note naming who fills which columns.

   ||Field||Value||Filled by||
   |CRDC Submission ID|`<submission-id>`|Data Concierge|
   |Release Package|`<timestamp>-<submission-id>/` (linked to the S3 console)|Data Concierge|
   |Object Files Directory|`s3://nci-crdc-data-bucket-prod/<uuid>/`|Data Concierge|
   |Megazip Submission ID|PLACEHOLDER|Data Concierge|
   |Megazip Release Package|PLACEHOLDER|Data Concierge|

   One row per megazip:

   ||data_file_type||File List (attached)||Files||Megazip File||md5||size||GUID||
   |`<data_file_type>`|`<study>_<version>_<data_file_type>_megazip_file_list.tsv`|`<count>`|`<study>_<data_file_type>_<version>.zip`| | | |

   _File List, Files and Megazip File: Data Concierge (section 1). md5 and size: DevOps (section 2). GUID: Data Concierge (section 3)._

3. `h3. 🔁 *Handoff Log*`: One row per workflow section; the section owner fills Completed and Initials when handing off.

   ||Section||Owner||Completed||Initials||
   |1. Prepare file lists|Data Concierge| | |
   |2. Generate megazips|DevOps| | |
   |3. Submit to the Portal|Data Concierge| | |
   |4. Index|Data Concierge| | |

4. `h3. 🚦 *Workflow*`: Four numbered sections, each a bold label naming its owner, with `#` numbered steps (numbering restarts per section).

   *1. Prepare file lists (Data Concierge)*
   1. Open `file.tsv` in the Release Package and split it into one file list per distinct `data_file_type`. Each list is the exact scope of one megazip. Do not list the Object Files Directory to find files; the Submission Portal releases every study's object files into that shared directory.
   2. Name each megazip `<study>_<data_file_type>_<version>.zip`, confirm no object with that name already exists in the Object Files Directory, and fill in the File List, Files and Megazip File columns.
   3. Attach the file lists, mark the Handoff Log, and reassign to DevOps.

   *2. Generate megazips (DevOps)*
   _You need_: each row's File List and Megazip File name, and the Object Files Directory.
   1. For each row, run the Prefect megazip job against that row's file list, writing the megazip into the Object Files Directory. The individual files stay in place.
   2. Confirm each megazip contains exactly the number of files in its Files column and nothing else.
   3. Record each megazip's md5 and size, mark the Handoff Log, and reassign to the Data Concierge.

   *3. Submit to the Portal (Data Concierge)*
   1. Author one file-metadata loading file covering every megazip (CRDC Submission Portal CLI format), using the md5 and size from the table.
   2. Submit it through the CRDC Submission Portal as its own *metadata-only* submission. The megazips are already in the Object Files Directory, so do not upload any object files; submit only the loading file. Record the Megazip Submission ID.
   3. Once the submission validates and is released, record the Megazip Release Package directory and copy each megazip's GUID from its `indexd.tsv` into the table. Mark the Handoff Log.
   4. Record the Megazip Submission ID and Megazip Release Package on the linked megazip loading ticket and hand it to the data loading engineer. Loading can start while indexing runs.

   *4. Index (Data Concierge)*
   1. Upload the `indexd.tsv` from the Megazip Release Package to the DCF Google Drive folder; keep the Release Package filename.
   2. File a CRINTAKE ticket naming the manifest, and link it to this ticket.
   3. When CTDS reports done, resolve each GUID at `https://nci-crdc.datacommons.io/index/<GUID>`. A pass returns a record whose `urls` points at that megazip and whose `size` and `hashes` are non-empty. If any fails, comment on the CRINTAKE ticket and on the linked loading ticket.
   4. Mark the Handoff Log. Passing GUID spot-checks close this ticket.

**Standing emoji set (4 entries)**

| Section | Emoji |
|---|---|
| Summary | 🎯 |
| Artifacts | 📦 |
| Handoff Log | 🔁 |
| Workflow | 🚦 |

The ✅ Testing Signoff section from v3 moved to the paired DO-LOAD ticket with the Load phase.

**The paired load ticket (DO-LOAD)**

Create it at the same time as this ticket, from the Data Loading Task template (DO-LOAD), titled `Data Loading: <token> Megazips`. Its submission table row is `<token> Megazips` with the Megazip Submission ID and Megazip Release Package as PLACEHOLDER until section 3 of this ticket fills them. Its tier checks are: every megazip shows on the Study Details page and downloads through the IndexD signed-URL flow, and any megazip already on the study (for example a Radiology Images megazip) still downloads. Link the two tickets `Relates`.

**Required content rules**

- **Scope is file lists, megazip generation, the metadata-only Portal submission, and indexing.** Loading the megazips is the paired DO-LOAD ticket; a plain study load is DO-LOAD; registering a study's own files is DO-INDEX; consent-group validation is DO-DBGAP; schema changes are DO-MODEL / DO-INTMODEL.
- **One ticket per study, one megazip per `data_file_type`.** The megazip table has one row per type; the loading file and the Megazip Release Package each carry one row per megazip.
- **File lists come from `file.tsv`, never from listing the Object Files Directory**, which is shared across studies.
- **Filename is `<study>_<data_file_type>_<version>.zip`**, underscores for spaces, no program prefix, and must not match any existing object in the directory.
- **GUIDs are Portal-assigned** by the metadata-only submission; never self-minted.
- **Every value lives in this ticket's Artifacts table.** Section owners write their outputs there; only the Megazip Submission ID and Megazip Release Package are copied, once, to the paired load ticket.
- **Each section ends with a handoff**: mark the Handoff Log and reassign the ticket to the next owner.
- **Release package is inferred, not requested.** It is already recorded on the study's DO-DBGAP ticket (and usually DO-INDEX); copy it into the Artifacts table at creation. If neither carries it, the study has not been released and this ticket is premature.
- **Title:** `Create, Submit, and Index Megazip files for <token>`, where `<token>` is the submission epic's `<Program Short Name> <Study Short Name> <version>` (plural "files"; no data type in the title).
- **Issue type Task; Parent Epic via `customfield_12350`** (the study's submission epic, Section DO-EPIC); **no label; no component**; priority Major to match the family. Assign to the Data Concierge at creation, since section 1 is theirs.
- **Sprint: always add the ticket to the standing `CTDC Data Related` sprint at creation** (board 641, sprint id 8612; a permanent future-state sprint that holds data-operations work). Never leave it in the backlog and never place it in a numbered development sprint.
- **Links: `Relates` to every other task for the study** (DO-DBGAP, DO-INDEX, DO-LOAD, the paired megazip DO-LOAD, and the DHDM tracker; the submission epic is the parent via the Epic Link), plus a native "Related To" issue link to the CRINTAKE ticket once filed (`jira_create_issue_link`; CRINTAKE is on the same Jira instance, so not a remote link). **Never `Blocks` or `Supports`. No link to any feature user story.**
- **No AWS Account ID row, no "To be created" rows, no Notes column.** If a value is not typed into a command, a file, or a form, it does not belong in the table.
- **Jira wiki authoring** (connector Markdown translation is off, so what is sent is stored exactly; see SKILL.md 7b-shared, "Authoring format"): `h3. {emoji} *Title*` headers; `* *Label*: content` labeled lines; `*` bullets and `#` numbered lists at column 0; `*bold*`; `{{monospace}}`; tables `||header||` / `|cell|`; links `[text|url]`; curly braces escaped as `\{...\}`. Blank line after every heading. After pushing, read the description back and confirm it starts with `h3.`, not `###`. Edit descriptions through the connector or the Text tab: Jira's visual editor rewrites URLs inside `{{ }}` into broken links.

**Writing-and-publishing workflow**

1. Confirm the study's DO-DBGAP ticket is Closed and carries the release package directory; copy it. Read the Object Files Directory from the `urls` column of the release package's `indexd.tsv`.
2. Create this ticket via `jira_create_issue` (Task, placeholder description, `customfield_12350`, priority Major, assigned to the Data Concierge, no label, no component).
3. Push the body via `jira_update_issue`.
4. Create the paired load ticket from DO-LOAD (`Data Loading: <token> Megazips`) with the same epic link, sprint, and no component.
5. Add both tickets to the `CTDC Data Related` sprint (id 8612) via `jira_add_issues_to_sprint`.
6. Add `Relates` links: this ticket to the paired load ticket and to every study task and the DHDM tracker.
7. Verify the render in the UI.

**When NOT to use this template**

- **Loading the megazips** → the paired DO-LOAD ticket.
- **Loading a study's own release package** → DO-LOAD.
- **Registering a study's own files** → DO-INDEX.
- **dbGaP consent-group validation** → DO-DBGAP.
- **Data modeling** → DO-MODEL or DO-INTMODEL.
- **Software development** → the software development template family.
- **CRDC platform changes** → owned by CRDC platform teams; out of CTDC scope.

**Canonical examples**

**CTDC-2207** (*Create, Submit, and Index Megazip files for CMB v6*), restructured to v4 on 2026-10-27: two megazips (Variant Call Files, Variant Reports), file lists split from `file.tsv` and attached, the Filled-by Artifacts table, the Handoff Log, four owner-labeled sections, and the metadata-only submission. Its paired load ticket is **CTDC-2297** (*Data Loading: CMB v6 Megazips*). CTDC-2220 and CTDC-2221 are the v3 examples and still carry Create, Index, and Load on one ticket.

**Changelog**

> _Numbering note: template IDs in older entries predate the 2026-07-23 renumber from legacy 7x letters to DO- codes; see the crosswalk in SKILL.md._

- **v4 (2026-10-27)**: Restructured for clear ownership after the team found that the Submission Portal releases every study's object files into one shared directory. **File lists**: the Data Concierge splits the release package's `file.tsv` into one list per `data_file_type`; the megazip job runs against a list, never the directory. **Megazip filename** gains a version (`<study>_<data_file_type>_<version>.zip`) plus a no-collision check, so a new megazip cannot overwrite an earlier one behind its GUID. **Submission**: the megazip metadata goes through the CRDC Submission Portal as a **metadata-only** submission, so GUIDs are **Portal-assigned** (self-minting removed). **Load moved out** to a paired DO-LOAD ticket (`Data Loading: <token> Megazips`), taking the Testing Signoff table with it. **Ownership**: each section names its owner (Data Concierge, DevOps), a Handoff Log records each handoff, the Artifacts table gains a Filled-by column, and the ticket is reassigned at every handoff. Title `Create, Submit, and Index Megazip files for <token>`. No component. Canonical example CTDC-2207 with CTDC-2297.
- **2026-09-25 parent change** (no version bump unless noted): tasks from this template are children of the study's submission epic (DO-EPIC) via `customfield_12350`; the Data Submission user story (DO-STORY) is retired and CTDC-1664 is no longer the default parent; it stays open as the standing epic for cross-study integration work. `Relates` links to the parent user story are replaced by the Epic Link. Title token is `<Program Short Name> <Study Short Name> <version>`.
- **v3 (2026-09-17)**: Lean rewrite driven by the TPM after assignees reported the v2 ticket was too long to read. One megazip **per `data_file_type`** on one ticket (v2 assumed one per study); filename `<study>_<data_file_type>.zip` with **no program prefix**; the release package is **inferred from the linked DO-DBGAP / DO-INDEX tickets**; the object-files directory lookup (different bucket, read from `indexd.tsv` `urls`) is an explicit first step with an example; 4 sections instead of 5 (Verification folded into Index step 4); Artifacts split into a three-row study table and a per-type megazip table; dropped the AWS Account ID row, the Notes column, and the "To be created" rows; **no feature user story link**, `Relates` to every study task instead; every ticket goes into the standing `CTDC Data Related` sprint (id 8612) at creation. Canonical examples CTDC-2220 and CTDC-2221.
- **v2 (2026-07-22)**: Corrected the indexing model: megazips are indexed through the external CTDS team via DCF Google Drive + CRINTAKE like every CTDC file; the only megazip-specific difference is the self-minted GUID and team-authored `indexd.tsv`. Expanded the Index phase to four steps; added md5sum/size and spot-check-failure guidance; added the CRINTAKE remote-link requirement.
- **v1 (2026-06-11)**: First version, built from the Data Loading and IndexD Registration templates with CTDC-2104 as the canonical example.
