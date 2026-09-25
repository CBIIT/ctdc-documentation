### DO-DBGAP. 🧬 dbGaP Validation Task Template (v2)

> **Parent change (2026-09-25).** Each study submission is now tracked as a **submission epic** (Section DO-EPIC, `data-submission-epic-template.md`), which replaces the Data Submission user story (DO-STORY, retired) and the default CTDC Data Integration epic (CTDC-1664). Wherever this template says "parent submission user story" or "parent user story", read **submission epic**: this task is a child of that epic via the Epic Link (`customfield_12350`), not a `Relates` link, and the epic is the home for study identity, open questions, and risks. The title token is `<Program Short Name> <Study Short Name> <version>`, omitting parts that do not apply (for example `CMB v6`, `NCTN AHOD0831`).


> **Use this template for every CTDC data management task that validates a released study submission against dbGaP before it is indexed or loaded: running the established `dbgap_validatation_prod` Prefect deployment to confirm the submission's consent group / ACL values reconcile with the consent groups registered in dbGaP.** The canonical example is **CTDC-2141** (*dbGaP Validation: <Study Name vN>*), drafted 2026-07-09 as the first ticket of this pattern. This template covers a node in the data-submission workflow that sits **after the study is released in the CRDC Submission Portal (workflow step 7) and before the paired IndexD Registration (Section DO-INDEX) and Data Loading (Section DO-LOAD) tasks run**: a consent-group gate. It is **not** the IndexD registration that mints file GUIDs (DO-INDEX), and **not** the load that promotes the submission through Jenkins (DO-LOAD); it is the validation that gates both. See "When NOT to use this template" at the end.

**Why this template**

The CTDC team has two primary functions (software development and data management), and data management has two sub-functions: loading data and modeling data. Within loading data, before a released submission is registered in IndexD (DO-INDEX) and loaded through Jenkins (DO-LOAD), CTDC must confirm the submission is safe to expose: every file's access-control metadata (its consent group / ACL) must match the consent groups the study is actually registered for in **dbGaP** (the NIH database of Genotypes and Phenotypes). If a file were released under the wrong consent group, it could be exposed to users who are not authorized for that consent tier. This validation is the **gate** that catches such mismatches before any indexing or loading happens.

The validation is **internal automation, not an external handoff and not a Jenkins load.** It is performed by an established **Prefect** deployment (`dbgap_validatation_prod`) that CTDC runs directly. CTDC's role is to run the deployment against the released submission, read the result, attach the evidence to the ticket, and remediate any mismatch before the study moves on.

**Tasks execute; user stories deliberate.** A Task carries only what the assignee needs to run the validation and record the result. Open questions, risks, and unresolved decisions belong on the parent submission user story (Section DO-STORY), where the team negotiates scope and tracks risk at the program level. If a mismatch turns out to be a program-level data problem, it is raised on the user story, not buried in this Task.

**Service & tooling anatomy (read once before drafting)**

- **Prefect Cloud**: the orchestration platform that runs the validation flow. The run is performed in the `crdc-workspace` workspace under the **`FNLCRDCPrefectCurators`** account.
- **`dbgap_validatation_prod` deployment**: the established, versioned Prefect deployment that performs the dbGaP validation. The deployment name is reproduced verbatim (including its current spelling) so it matches the Prefect UI exactly; do not "correct" it in the ticket.
- **`submission_id` parameter**: the CRDC Submission ID issued by the CRDC Submission Portal; identifies the released package the flow validates. Entered on the custom run.
- **`check_consent_group` parameter**: the boolean toggle that turns on consent group / ACL reconciliation against dbGaP. **Always toggled on** for this gate; it is the check the whole task exists to run.
- **Release Package**: the per-release directory in the CTDC metadata S3 bucket (`nci-cbiit-clinicaltrialdatacommons-metadata`), created when the study is released from the CRDC Submission Portal. Holds the metadata loading TSVs and the `indexd.tsv` manifest whose `acl` column carries each file's consent group.
- **dbGaP accession (phsXXXXXX)**: the study's registration in dbGaP; defines the authorized consent groups the released package's ACLs must match. It lives on the parent submission user story; the validation task repeats it only as the validation anchor.
- **Paired downstream tasks**: the **IndexD Registration Task (DO-INDEX)** and the **Data Loading Task (DO-LOAD)**. This validation is the consent-group gate for both as a matter of process: a failing run holds the handoff to engineering. In Jira the three tasks are linked with `Relates`, not `Blocks` (see Required content rules).

**Section order (4 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Keep the order so a reader scanning multiple validation tickets sees the same visual flow. The section shapes deliberately mirror the IndexD Registration (DO-INDEX) and Data Loading (DO-LOAD) templates.

1. `### 🎯 **Validation Summary**`: **One sentence**: that the established dbGaP validation job is run against the released submission to reconcile consent group / ACL values with dbGaP, gating the paired indexing and loading. Do **not** restate study identity, consent codes, or submission chronology: those live on the parent submission user story (linked via the native Links panel) and are visible in Jira statuses. Example: *"Run the established dbGaP validation Prefect job against the released study submission to confirm its consent group / ACL values reconcile with the consent groups registered in dbGaP, gating the paired Data Indexing and Data Loading tasks that follow."*

2. `### 📦 **Submission & Artifacts**`: Required. A six-row table holding what the assignee needs to run the deployment. Study identity lives on the parent submission user story, not in this table.

   | Field | Value | Notes |
   |---|---|---|
   | CRDC Submission ID | *(Submission Portal ID)* | Issued by the CRDC Submission Portal; one per submission. Entered as the `submission_id` parameter on the Prefect run. |
   | AWS Account ID | `101183076466` | Constant for CTDC: the CTDC data commons AWS account. |
   | AWS S3 Bucket | `nci-cbiit-clinicaltrialdatacommons-metadata` | Constant for CTDC: the metadata bucket that holds every release package. |
   | Release Package | *(directory name)* | Directory name only, within the AWS S3 Bucket above. Created when the study is released from the CRDC Submission Portal; PLACEHOLDER until release. |
   | dbGaP Accession | *(phsXXXXXX)* | The study's dbGaP accession whose registered consent groups the job reconciles against. Lives on the parent submission user story; repeated here only as the validation anchor. |
   | Prefect Deployment | `dbgap_validatation_prod` | The established deployment in the Prefect Cloud `crdc-workspace`, run under the `FNLCRDCPrefectCurators` account. |

3. `### 🚦 **Validation Workflow**`: Numbered list grouped into two phases. Standard CTDC sequence:

   **Run the Prefect job**
   1. Log in to Prefect Cloud using the `FNLCRDCPrefectCurators` account and open the `crdc-workspace` workspace.
   2. From the sidebar, click *Deployments* and select the `dbgap_validatation_prod` deployment.
   3. Click *Run* (upper-right corner), then choose *Custom run* from the dropdown. Set the *Run Name* to the full study name; set the `submission_id` parameter to the CRDC Submission ID; toggle on `check_consent_group`; click *Submit*.
   4. Monitor the flow run in the Prefect Cloud UI until it reaches a terminal state.

   **Record and remediate**
   5. Attach a screenshot of the flow-run results to this task.
   6. Resolve any errors the run identifies (for example, consent group / ACL mismatches against dbGaP), re-running the deployment after each fix until the run completes clean. Surface any program-level blockers on the parent submission user story, not on this task.

   **Step count: 6 (4 run, 2 record and remediate).**

4. `### 🧪 **Verification**`: How CTDC confirms the validation passed, and the close trigger. Bullet list (italic-labelled, colon separators, the rendering-safe pattern):

   - *How to confirm a pass*: The `dbgap_validatation_prod` flow run reaches a *Completed* state and the `check_consent_group` result reports zero consent group / ACL mismatches between the released package and the consent groups registered in dbGaP for the accession.
   - *Evidence on the ticket*: The results screenshot from workflow step 5 is attached to this task.
   - *If the run fails*: Do not hand the study off to Indexing or Loading; re-run after remediation, and if the mismatch is a program-level data issue, raise it on the parent submission user story so it is tracked at program level.
   - *Close trigger*: A clean `dbgap_validatation_prod` run with the results screenshot attached is the trigger to close this task.

**Standing emoji set (4 entries)**

| Section | Emoji |
|---|---|
| Validation Summary | 🎯 *(shared with IndexD Registration and Data Loading tasks)* |
| Submission & Artifacts | 📦 *(shared with IndexD Registration and Data Loading tasks)* |
| Validation Workflow | 🚦 *(shared with IndexD Registration and Data Loading tasks)* |
| Verification | 🧪 *(shared with IndexD Registration task; scoped to the consent-group check)* |

**Required content rules**

- **Scope is dbGaP consent-group validation only.** Running the `dbgap_validatation_prod` Prefect deployment with `check_consent_group` on. **IndexD registration** (minting GUIDs) uses the IndexD Registration Task template (Section DO-INDEX). **Data loading** uses the Data Loading Task template (Section DO-LOAD). **Schema or model changes** use a modeling template (Section DO-MODEL or DO-INTMODEL). See "When NOT to use this template."
- **No Acceptance Criteria section.** This is operational SOP work; the completion bar is the clean validation run with the screenshot attached (Section 4). AC belongs on user stories, not on Tasks.
- **No Open Questions / Risks section.** Open questions and risks live on the parent submission user story, not on this Task.
- **One Task per released submission / version.** Reuse the submission epic's title token (`<Program Short Name> <Study Short Name> <version>`); a reload under a new release is its own new validation task, paired with its own loading task.
- **Issue type is Task** on this tracker, matching CTDC-2141. Do not use Story or Subtask.
- **Title convention:** `PREFECT dbGaP Validation: <Program Short Name> <Study Short Name> <version>`: reuse the exact token from the submission epic title (Section DO-EPIC) verbatim (for example, `PREFECT dbGaP Validation: CMB v6`, CTDC-2233).
- **Parent Epic field set via `customfield_12350`** to the study's submission epic (Section DO-EPIC). CTDC-1664 is no longer the default.
- **`Data-Concierge` label is mandatory.** The validation is performed by the Data Concierge, so the task carries the `Data-Concierge` label, set at creation via the `labels` field: the same posture as the IndexD Registration (Index) task.
- **Leave the ticket Unassigned at creation** per standing team convention. Data management tasks do not require the Developer field.
- **Sprint: always add the ticket to the standing `CTDC Data Related` sprint at creation** (board 641, sprint id 8612; a permanent future-state sprint that holds data-operations work). Never leave it in the backlog and never place it in a numbered development sprint; pass the new key to `jira_add_issues_to_sprint` right after creation.
- **`Relates` link to the study-specific Data Hub tracker (DHDM-XXX) when one exists.** The submission epic (the Epic Link) is the canonical record of study identity and the home for open questions and risks.
- **`Relates` links to the paired IndexD Registration (DO-INDEX) and Data Loading (DO-LOAD) tasks, never `Blocks`.** The team's standing posture is that data-operations tasks in a submission family are linked with `Relates`, and this task is no exception: indexing and validation do not block the loading task in Jira terms, and the consent-group gate is enforced by the Data Concierge's process (do not hand off until the run is clean; see Verification), not by a Jira link. `Blocks` links are reserved for true tooling dependencies where one ticket cannot be worked at all until another closes. If the paired tasks do not exist yet at validation-ticket creation time, add the `Relates` links as soon as they do.
- **Submission & Artifacts table is mandatory and complete at ticket creation.** All six rows present. Use PLACEHOLDER explicitly when a value is pending upstream (typically the Release Package directory, which does not exist until release), never silently omit a row.
- **Results screenshot is mandatory evidence.** The flow-run results screenshot (workflow step 5) must be attached to the task; a free-text "validation passed" note is not sufficient. The screenshot is the reproducible record behind the close trigger.
- **Two-step create:** `jira_create_issue` (issue_type `Task`, short placeholder description, parent epic via `customfield_12350` in `additional_fields`, `Data-Concierge` in `labels`, Unassigned) then `jira_update_issue` with the full Markdown body. Same pattern as DO-INDEX/DO-LOAD.
- **Rendering-safe authoring patterns**: section headers use `### **Title**` Markdown form (round-trips to `h3.`); italic-label bullets use `* *Label*: content` (italic label, colon separator), NOT `- **Label:** content`; tables use Jira-wiki `||header||` syntax, NOT GitHub-flavored `|h|h|`; numbered workflow steps use Markdown `1.` ordered-list form (round-trips to wiki `#` items), NOT a leading wiki `#`, which the Markdown converter turns into an `h1.` heading. Confirm the render with a UI screenshot; wiki source is not a reliable preview.
- **All four sections are required**, in order: Validation Summary, Submission & Artifacts, Validation Workflow, Verification.

**Writing-and-publishing workflow**

1. Confirm the study has been **released** in the CRDC Submission Portal and its Release Package directory exists in `nci-cbiit-clinicaltrialdatacommons-metadata`. Validation before release is premature; the Release Package is the thing being validated. Surface any open questions on the parent submission user story, not on the Task.
2. Confirm this is dbGaP consent-group validation, not indexing (DO-INDEX), loading (DO-LOAD), or modeling (DO-MODEL/DO-INTMODEL).
3. Identify the submission epic (Section DO-EPIC) and the study-specific Data Hub tracker (DHDM-XXX). The epic is set as the Epic Link at creation; the DHDM tracker goes on via a `Relates` link.
4. Identify the paired IndexD Registration (DO-INDEX) and Data Loading (DO-LOAD) tasks (or note they will follow); this validation `Relates` to both.
5. Create the validation task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, the parent epic via `customfield_12350` in `additional_fields` (the submission epic), and the `Data-Concierge` label via the `labels` field. Leave Unassigned.
6. Push the full description in a second call via `jira_update_issue` with the full Markdown body.
7. Add the ticket to the `CTDC Data Related` sprint (id 8612) via `jira_add_issues_to_sprint`.
8. Add the `Relates` links (DHDM tracker, paired IndexD and Loading tasks, megazip task) via `jira_create_issue_link`.
9. Run the deployment per the Validation Workflow, attach the results screenshot, and remediate any mismatch.
10. Verify the rendered description with a UI screenshot.
11. After a clean run with the screenshot attached (Verification section), transition the ticket to Closed with resolution `Fixed`. **A clean validation run plus attached screenshot is the close trigger.**

**When NOT to use this template**

- **IndexD registration (minting GUIDs)** → IndexD Registration Task template (Section DO-INDEX).
- **Data loading (promoting the submission through Jenkins)** → Data Loading Task template (Section DO-LOAD).
- **Data modeling** → Data Modeling for Study Submission (Section DO-MODEL) for study-driven changes, or Data Model Update Task (Section DO-INTMODEL) for internally-driven changes.
- **Software development work** → the software development template family.
- **CRDC platform changes** (Fence, IndexD, Submission Portal, or the shared Prefect platform itself) → owned by CRDC platform teams; out of CTDC scope. CTDC runs the established `dbgap_validatation_prod` deployment but does not own the platform.

**Canonical example**

**CTDC-2141**: *dbGaP Validation: <Study Name vN>* (drafted 2026-07-09 as the first ticket of this pattern). The ticket carries:

- 4 sections in the standard order (Validation Summary, Submission & Artifacts, Validation Workflow, Verification)
- 6-row Submission & Artifacts table (CRDC Submission ID, AWS Account ID, AWS S3 Bucket, Release Package, dbGaP Accession, Prefect Deployment)
- 6-step workflow grouped Run the Prefect job / Record and remediate
- Parent Epic CTDC-1664 set via `customfield_12350`; `Data-Concierge` label; Unassigned
- Per-study `Relates` links (parent user story, DHDM tracker, paired IndexD Registration and Data Loading tasks) added at instantiation time

**Changelog**

- **2026-09-25 parent change** (no version bump unless noted): tasks from this template are children of the study's submission epic (DO-EPIC) via `customfield_12350`; the Data Submission user story (DO-STORY) and the default CTDC-1664 epic are retired. `Relates` links to the parent user story are replaced by the Epic Link. Title token is `<Program Short Name> <Study Short Name> <version>`. Second example: CTDC-2233 (CMB v6).
- **v2 (2026-09-16)**: Link posture changed from `Blocks` to `Relates` for the paired IndexD Registration (DO-INDEX) and Data Loading (DO-LOAD) tasks. Indexing and validation do not block loading in Jira terms; the consent-group gate is a process rule carried by the Verification section, not a Jira dependency. Aligns the template with the CTDC-2142 and CTDC-2218 instances. Title convention on the tracker is `PREFECT dbGaP Validation: <Study Name vN>`. Added the rule that every ticket goes into the standing `CTDC Data Related` sprint (id 8612) at creation.
- **v1 (2026-07-09)**: Initial template. Establishes the dbGaP validation gate between "Released" (workflow step 7) and the paired IndexD Registration (DO-INDEX) / Data Loading (DO-LOAD) tasks, run via the established `dbgap_validatation_prod` Prefect deployment with `check_consent_group` toggled on. Canonical example CTDC-2141.
