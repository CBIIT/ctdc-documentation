### DO-EPIC. 📥 Data Submission Epic Template (v1)

> **Use this template for every CTDC study data submission.** One epic per study submission (and per version, when a study submits again). It replaces the Data Submission user story (Section DO-STORY, retired 2026-09-25) and the single standing CTDC Data Integration epic (CTDC-1664), which grew to over 100 children across every program. The canonical example is **CTDC-2110** (*CTDC Data Submission: CMB v6*), converted from a user story on 2026-09-25 as the pilot.

**Why this template**

A study submission has a real start (the Submission Request Form) and a real finish (the study verified in Production with its files downloadable). That makes it an epic: it holds the dbGaP validation, IndexD registration, data loading, and megazip tasks as native children, and it closes when the study is live. On this Jira instance a user story cannot hold tasks as children, which is why the old Data Submission user story needed a web of `Supports` and `Relates` links; the epic replaces that web with the Epic Link. Each epic is also one card on the Federal Leadership epic Kanban board, which is how leadership already talks about the work ("where is CMB v6?").

The structure is CTDC-1664's long-form data epic, scoped to one submission, merged with every field the Data Submission user stories carried. Sections from CTDC-1664 that do not help a single submission (Scope, Stakeholders, Key Definitions, Performance & Quality, Dependencies, Assumptions, Constraints, Documentation & Compliance) are not carried: the submission's **Process Documentation** in SharePoint is the source of truth for process and compliance records, and the epic links to it.

**Title and Epic Name**

`CTDC Data Submission: <Program Short Name> <Study Short Name> <version>`, omitting any part that does not apply. Examples: `CTDC Data Submission: CMB v6` (CMB has no program); `CTDC Data Submission: NCTN AHOD0831`. Set the Epic Name (`customfield_12351`) to the same string. Every child task reuses the same `<Program Short Name> <Study Short Name> <version>` token verbatim, so one `summary ~ "<token>"` search finds the whole family:

| Child | Title |
|---|---|
| dbGaP validation (DO-DBGAP) | `PREFECT dbGaP Validation: <token>` |
| IndexD registration (DO-INDEX) | `Data Indexing: <token>` |
| Data loading (DO-LOAD) | `Data Loading: <token>` |
| Megazip (DO-ZIP) | `Create, Index, and Load Megazip files for <token>` |
| Submission data modeling (DO-MODEL) | `Submission Data Modeling: <token>` |

**Card line + section order (card line, then 9 sections, exactly this sequence)**

Each section header is an `h3.` Jira wiki heading using the emoji + bold title format shown. Author in Jira wiki markup (see SKILL.md 7b-shared, "Authoring format").

0. **Card line** (no header): one plain sentence, 140 characters or fewer, for the Federal Leadership epic Kanban card. Same rule as every CTDC epic (SKILL.md 7b item 0).
1. `h3. 🎯 *Epic Summary*`: One short paragraph. What this submission brings into CTDC and why, written as a goal (not as a user story). Example (CTDC-2110): *"Integrate the Cancer Moonshot Biobank (CMB) v6 submission into CTDC: guide the submission through the CRDC Submission Portal, obtain the Release Package, and see the data validated, indexed, loaded, and verified so the study is correctly integrated and its files are downloadable in Production."*
2. `h3. 🧬 *Context & Background*`: The study, its program, and its history in CTDC (new study or existing study, prior versions, why this submission now), ending with who benefits. FAIR stated where it falls naturally. No protocol text copied from the submission.
3. `h3. 🏁 *Goal / Objectives*`: Three to five bullets for this submission (for example: released and completed in the Portal; consent groups validated against dbGaP; every file registered in IndexD with one megazip per data file type; loaded through Dev, QA, Stage, and Prod with signoff).
4. `h3. 🗂️ *Submission Details*`: Two-column `||Field||Value||` table, in this row order. Rows a submission does not have are left out, not filled with placeholders.

   | Row | Notes |
   |---|---|
   | Process Documentation | Link to the submission's Process Documentation in SharePoint; always first; provided by the TPM |
   | Submission Request Form (SRF) | Link; SRF approval triggers Concierge assignment and the SharePoint folder |
   | CRDC Data Submission Request Date | |
   | Submission ID | Monospace; assigned by the Portal when the submission is created |
   | Program | `NA` when the study has no program |
   | Program Short Name | `NA` when the study has no program |
   | Study Name | |
   | Study Short Name | The source of the title token |
   | dbGaP ID | |
   | dbGaP Link | |
   | Associated Publications | When known |
   | Study Status in CTDC | New study, or existing study with a new vN submission |
   | Submitter / Submission Team | |
   | CTDC Data Concierge | |
   | CDE Request Workbook | Link |
   | Data Hub Data Modeling Ticket (DHDM) | Link; the one Jira key allowed in the body, because it is a cross-project tracker |
   | SharePoint Folder | Link |
   | SharePoint Folder for Metadata / Documentation / Presentations | Legacy rows from older submissions; keep only when they exist |
   | AWS Bucket Location for Object Data | Legacy row from older submissions; keep only when it exists |

5. `h3. 🚦 *Submission Lifecycle*`: One lead line linking the CTDC Active Data Submission SOP, then the outline. Each step with its own child task is executed and tracked there.
   1. The submitter files the Data Submission Request through the CRDC Submission Portal; the Submission Review Committee approves it.
   2. The Data Concierge supports the submitter throughout.
   3. Any required data model and CDE changes run as a separate submission data modeling task, with the SI team for CDEs; backend queries and frontend features are reviewed for any model change.
   4. Validation completes in the CRDC Submission Portal; the submission is submitted and released, and the Release Package goes to engineering.
   5. Consent groups are validated against dbGaP.
   6. Files are registered in IndexD through DCF.
   7. Metadata loads to Dev for initial testing; the submission is then marked Complete, which moves the object files into the bucket.
   8. A megazip is built for each data file type.
   9. Data loads to QA, Stage, and Prod, with testing at each tier.
   10. Production Verification Testing (PVT) or Post-Deployment Testing (PDT) confirms the study and its file downloads.
6. `h3. ✅ *Acceptance Criteria*`: Five to eight functional outcomes that close the epic. The epic itself is not tested; verification happens on the child tasks. Standard set: released and marked Complete with the Release Package with engineering; consent groups pass dbGaP validation before indexing and loading; every file registered in IndexD and its GUID resolves; loaded Dev, QA, Stage, Prod with signoff and nothing loaded directly to Prod; one megazip per data file type downloads; the study renders correctly in Production and its files download for authorized users; the CDE Request Workbook records the released model version. Add submission-specific outcomes (for example new model fields rendering) as needed.
7. `h3. ⚠️ *Risks & Mitigations*`: `||Risk||Impact||Mitigation||` table, three to seven rows, specific to this submission. Offer these standing risks where they apply: the data model lags the submission; submitter package quality or validation errors; PHI handling on patient-level data (BlindID for NCI-MATCH); newly indexed fields break frontend rendering; a software release gated on this data release.
8. `h3. 📅 *Submission Chronology*`: `_Current as of <date>_` then a dated bullet log of **submission milestones and decisions only**: request filed, approvals, released, marked Complete, loaded to each tier, verified in Prod, and decisions that change the plan. **Never record data model version releases**; they change too often and the modeling task and model repo track them. Meeting tables from older submissions may stay here.
9. `h3. 📝 *Notes*`: Two bullets at most: what closes the epic (the study verified in Production and every child task Closed), and prior-version lineage. The next version of the study gets its own epic.

**Standing emoji set (9 entries)**

| Section | Emoji |
|---|---|
| Epic Summary | 🎯 |
| Context & Background | 🧬 |
| Goal / Objectives | 🏁 |
| Submission Details | 🗂️ |
| Submission Lifecycle | 🚦 |
| Acceptance Criteria | ✅ |
| Risks & Mitigations | ⚠️ |
| Submission Chronology | 📅 |
| Notes | 📝 |

**Not on the epic (and where it lives instead)**

- **Aggregate counts** (participants, diagnoses, therapies, biospecimens, files, data volume): the **Expected Counts** table on the Data Loading task (DO-LOAD v9), filled from the Release Package and checked by testers at each tier.
- **Node, property, CDE, and permissible value detail; base data types; mapping files**: the CDE Request Workbook and the submission data modeling task.
- **Planning documents** (SOW deliverables, submission documentation): the Process Documentation in SharePoint.
- **Data model versions**: the submission data modeling task and the `ctdc-model` repo.

**Links and hierarchy**

- **Children (Epic Link `customfield_12350` = this epic):** the dbGaP validation, IndexD registration, data loading, and megazip tasks. They link to each other with `Relates`, never `Blocks`.
- **Submission data modeling task:** stays a child of the Submission Data Modeling epic and links to this epic with `Relates`.
- **DHDM tracker:** `Related To` / `Relates`.
- **Prior version's epic or user story:** `Relates` (version lineage).
- No `Supports` links; the Epic Link replaces them.

**Metadata**

- Issue type **Epic**; priority **Major**; component **Data**; labels **`Task-1.2.1`** and **`Data-Concierge`**.
- Status follows the epic workflow; the epic closes with resolution `Completed` when the study is verified in Production and every child is Closed.
- Epics are not placed in sprints. The child tasks go into the standing `CTDC Data Related` sprint (id 8612) at creation.

**Content rules**

- No Jira ticket keys in the body except the DHDM row.
- No em dashes; colons for label lead-ins.
- No `TBD` placeholders: leave a row out, or use the skeleton phrase `_To be completed by the Data Concierge._` for an empty prose section.
- Underscored identifiers in `{{...}}` monospace; underscores in link URLs percent-encoded as `%5F`, parentheses as `%28` / `%29`; curly braces escaped as `\{...\}`.

**Writing-and-publishing workflow**

1. **Existing Data Submission user story:** convert it in the Jira UI (**Move → Issue type: Epic**, which asks for an Epic Name); this keeps the key, history, comments, and links. **New submission:** `jira_create_issue` with `issue_type = "Epic"`, a one-line placeholder description, and the Epic Name in `customfield_12351`.
2. Set the summary and Epic Name to the title convention; set labels `Task-1.2.1` and `Data-Concierge`, priority Major, component Data.
3. Push the full description via `jira_update_issue` (raw Jira wiki in `additional_fields` as `{"description": "..."}`).
4. Set the Epic Link on each child task via `jira_update_issue` `additional_fields` `{"customfield_12350": "<EPIC>"}` and confirm with JQL `"Epic Link" = <EPIC>`; rename children to the title token.
5. Remove any `Supports` links left from the user story era; switch the submission data modeling task's link to `Relates`.
6. Verify the render in the Jira UI.

**Changelog**

- **v1 (2026-09-25)**: Initial template, piloted on CTDC-2110 (CMB v6). Replaces the Data Submission user story (DO-STORY, retired) and the standing CTDC Data Integration epic (CTDC-1664). Built from CTDC-1664's section structure plus every field found across its 10 user stories; Scope, Stakeholders, Key Definitions, Performance & Quality, Dependencies, Assumptions, Constraints, and Documentation & Compliance dropped in favor of the Process Documentation link. Data Details moved to the Data Loading task's Expected Counts. Chronology excludes data model versions. Title `CTDC Data Submission: <Program Short Name> <Study Short Name> <version>`.
