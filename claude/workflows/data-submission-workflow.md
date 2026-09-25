# CTDC Data Submission: Process SOP

*Internal operational reference for Claude (Sprint Command Center). Owner: CTDC Data Concierge. Covers active data submissions end to end: the order the work happens in, and which Jira artifact and template to create at each step. This is the internal/technical companion to the separately maintained, submitter-facing Active Data Submission SOP. Revised 2026-09-25 for the Data Submission Epic (DO-EPIC).*

## The issue family: one submission epic per study submission

Every study submission (and every new version of a study) is its own **submission epic**. It replaces the Data Submission user story (DO-STORY, retired 2026-09-25) and the single standing CTDC Data Integration epic (CTDC-1664). The epic carries study identity and closes when the study is verified in Production.

| Artifact | Template | Parent and links | Count |
|---|---|---|---|
| **Submission epic** | **DO-EPIC** | Labels `Task-1.2.1` and `Data-Concierge`; `Relates` to the DHDM tracker and to the prior version's epic or story | 1 per study submission / version |
| **Submission Data Modeling** task | **DO-MODEL** | Child of the Submission Data Modeling epic; `Relates` to the submission epic | 1 per submission, when the model must change |
| **dbGaP Validation** task: consent-group gate before indexing and loading | **DO-DBGAP** | Child of the submission epic (Epic Link); `Data-Concierge` label | 1 per submission / version |
| **IndexD Registration** task | **DO-INDEX** | Child of the submission epic; `Data-Concierge` label | 1 per registration handoff |
| **Data Loading** task | **DO-LOAD** | Child of the submission epic; no label | 1 per load event |
| **Megazip** task: one zip per `data_file_type` | **DO-ZIP** | Child of the submission epic; no label | 1 per study, when files should download as bundles |

**Title token.** The epic title is `CTDC Data Submission: <Program Short Name> <Study Short Name> <version>`, omitting any part that does not apply (CMB has no program: `CTDC Data Submission: CMB v6`). Every task reuses the same token verbatim:

| Task | Title |
|---|---|
| DO-MODEL | `Submission Data Modeling: <token>` |
| DO-DBGAP | `PREFECT dbGaP Validation: <token>` |
| DO-INDEX | `Data Indexing: <token>` |
| DO-LOAD | `Data Loading: <token>` |
| DO-ZIP | `Create, Index, and Load Megazip files for <token>` |

**Links.** Tasks under the submission epic link to each other with `Relates`, never `Blocks`. No `Supports` links: the Epic Link replaces them. New tickets stay **unassigned** unless directed; data-management tasks don't need the Developer field. Every task goes into the standing `CTDC Data Related` sprint (board 641, id 8612) at creation; the epic is not placed in a sprint.

## The flow (▶ = create a Jira artifact)

1. **SRF submitted**: the CRDC Data Submission Request Date is now known.
2. **SRF approved**: ▶ **Create the submission epic (DO-EPIC).** Assign the Data Concierge, create the study's SharePoint folder, and get the Process Documentation link from the TPM for the first row of Submission Details. An existing Data Submission user story is converted instead (Jira UI: Move, Issue type Epic), which keeps its key, history, and links.
3. **Data modeling starts** (hand in hand with the submitter, learning their data and what the model must accommodate): ▶ **Create the Submission Data Modeling task (DO-MODEL)** under the Submission Data Modeling epic and link it `Relates` to the submission epic. Build out the CDE Request Workbook, the term-level source of truth.
4. **CDE Workbook complete**: submit the **DHDM ticket** to Data Hub; link it `Relates` to the submission epic and record it in the modeling task. The Workbook must be finished before the DHDM ticket goes in.
5. **dbGaP IDs in hand**: required gate before the Data Hub portal submission can begin.
6. **Submission created in the Portal**: the Submission ID is assigned (can be months after SRF approval); record it in the epic's Submission Details.
7. **Released**: the Release Package lands in the metadata bucket (`nci-cbiit-clinicaltrialdatacommons-metadata`); hand it off to engineering and add the milestone to the epic's Submission Chronology.
8. **dbGaP validation**: ▶ **Create the dbGaP Validation task (DO-DBGAP).** Run the established `dbgap_validatation_prod` Prefect deployment (Prefect Cloud `crdc-workspace`, `FNLCRDCPrefectCurators` account) with the CRDC Submission ID as `submission_id` and `check_consent_group` toggled on; attach the results screenshot and resolve any consent-group / ACL mismatches. Nothing is handed off for indexing or loading until the run is clean; that gate is a process rule, so the Jira links stay `Relates`.
9. **Indexing**: ▶ **Create the IndexD Registration task (DO-INDEX).** Runs **in parallel** with loading.
10. **Dev load**: ▶ **Create the Data Loading task (DO-LOAD)** and fill its **Expected Counts** table once from the Release Package. Load metadata to **Dev only** (the object files are not in the bucket yet) and spot-check the study with the submitter.
11. **Marked Complete**: once Dev testing passes, the submission is marked Complete in the Portal, which moves the object files into the data bucket (`nci-crdc-data-bucket-prod`).
12. **Megazip** (when the study's files should download as one zip per data file type): ▶ **Create the Megazip task (DO-ZIP)**, preferably before the QA load so downloads resolve.
13. **QA, Stage, and Prod loads**: continue on the same Data Loading task, one Jenkins job per tier. Testers check counts against Expected Counts at every tier and initial Testing Signoff. Nothing loads directly to Prod.
14. **Verify and close**: Production Verification Testing (PVT) or Post-Deployment Testing (PDT) confirms the study renders and its files download. Close each task, then close the submission epic with resolution `Completed`. The next version of the study gets its own epic.

## Standing rules

- **The epic holds identity; tasks execute.** Study identity, risks, and chronology live on the submission epic, never duplicated on child tasks. Open questions go in Jira comments.
- **The chronology records submission milestones and decisions only**, never data model versions (the modeling task and the `ctdc-model` repo track those).
- **Counts live on the Data Loading task** (Expected Counts), not on the epic.
- **dbGaP validation gates indexing and loading as a process rule**, not a Jira `Blocks` link.
- **Indexing never blocks loading**: they run in parallel.
- **Reloads are always a new Data Loading task**, never folded into a modeling ticket.
- **Scope is active data submissions only.** Model changes the CTDC project itself initiates (Data Model Update, DO-INTMODEL, under the Internal Data Modeling epic) are a different workflow, out of scope here.
- **Template IDs** are the stable DO codes in SKILL.md Section 7. Older references resolve as 7e = DO-STORY (retired), 7f = DO-MODEL, 7g = DO-INDEX, 7h = DO-LOAD, 7i = DO-ZIP, 7k = DO-DBGAP.

## Canonical example

**CTDC-2110** (*CTDC Data Submission: CMB v6*), the pilot converted on 2026-09-25. Children: CTDC-2233 (dbGaP validation), CTDC-2206 (indexing), CTDC-2205 (loading, first with Expected Counts), CTDC-2207 (megazip). Modeling: CTDC-2111 under the Submission Data Modeling epic, `Relates` to CTDC-2110.
