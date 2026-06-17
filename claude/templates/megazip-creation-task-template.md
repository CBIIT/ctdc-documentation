### 7i. 🗜️ Megazip Creation Task Template (v1)

> **Use this template for every CTDC data management task that creates a single megazip bundling all of a study's object files, registers it in CRDC IndexD, and loads its metadata through Dev → QA → Stage → Prod so users can download the whole study as one file.** The canonical example is **CTDC-2104** (Create, Index, and Load Megazip file for NCTN-NCORP AHEP0731 Image Files). This template covers the **CTDC-internal artifact-creation** work pattern within the data-management lane: unlike a Data Loading Task (Section 7h) or an IndexD Registration Task (Section 7g), the team creates the artifact, mints its GUID, and loads it itself, with no external CRDC submission and no external CTDS handoff. See "When NOT to use this template" at the end.

**Why this template**

A megazip is a single `.zip` that bundles **all** of a study's object files into one downloadable artifact, created **in addition to** the individual files (the individual files stay in the bucket; the megazip is an extra object). It exists so a user can download an entire study in one click from the Study Details page, the download-button feature tracked on its user story (CTDC-1909 lineage).

Creating and publishing a megazip is a superset of three operations the team already runs separately:

- **Create**: a Prefect job reads the study's object-files directory and writes the megazip into that same directory, then a file-metadata loading file is authored for the CRDC Submission Portal CLI.
- **Index**: the team mints a GUID for the megazip in-house (generate a UUID, prepend the `dg.4DFC/` prefix) and authors the `indexd.tsv` manifest for it. This is the key difference from a study-file IndexD Registration Task (Section 7g): there is no external CTDS/DCFS handoff and no CRINTAKE intake ticket; the megazip is a CTDC-internal object, so the team registers it directly.
- **Load**: the megazip metadata is promoted through Dev → QA → Stage → Prod using the dedicated per-tier Jenkins data-loading jobs, exactly as a Data Loading Task (Section 7h) does, with a Testing Signoff table as the completion record.

Because it spans all three, this template carries **both** a 🧪 Verification section (the GUID spot-check, inherited from 7g) and a ✅ Testing Signoff section (the per-environment promotion record, inherited from 7h). It is the only data-management template that carries both.

**Tasks execute; user stories deliberate.** A Task carries only what the assignee needs to build, register, and load the megazip. Open questions, risks, ownership directories, and link inventories belong on the parent user story (the download-feature story), not on the Task. Jira's native Links panel carries every relationship, so the description never restates them.

**Pipeline & store anatomy (read once before drafting)**

- **Creator**: a Prefect job builds the megazip. The individual object files are the input and must remain in place; the megazip is written alongside them in the same object-files directory.
- **Object-files bucket and directory**: the megazip is stored **inside** the study's object-files directory in the CRDC prod data bucket (typically `nci-crdc-data-bucket-prod`), next to the individual files, not at the bucket root.
- **Megazip filename**: use underscores, not spaces or other separators (e.g., `NCTN_AHEP0731_Radiology_Images.zip`). Name it for the study and its data type.
- **IndexD**: the megazip's GUID is minted in-house with the `dg.4DFC/` prefix and recorded in an `indexd.tsv` manifest authored by the team. The same centralized CRDC IndexD service resolves it; there is no Dev/QA/Stage/Prod separation for the registration itself.
- **Loading pipeline**: **Jenkins, with a dedicated data-loading job for each of the four tiers (Dev, QA, Stage, Prod).** There is no lower/upper-tier grouping for data loading: that two-tier split belongs to the `ctdc-model` contribution flow, not here.
- **Resolution endpoint**: `https://nci-crdc.datacommons.io/index/<GUID>`, used for the GUID spot-check in Verification.

**Parameters (fill these in per megazip)**

- Study ID and data type (e.g., AHEP0731, Radiology Images).
- CRDC Submission ID.
- Release Package directory (within the metadata bucket).
- Object Files bucket and directory (where the individual files live and where the megazip is written).
- Resulting megazip filename (underscores).
- Minted GUID (`dg.4DFC/` prefix), recorded once Index is complete.

**Section order (5 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections.

1. `### 🎯 **Summary**`: One to two sentences naming the study and its data type, stating that a megazip of all the study's object files is being created, indexed, and loaded so the whole study downloads as one file, and noting that the individual object files remain in the bucket. Example: *"Create a megazip of all AHEP0731 Radiology Image object files via a Prefect job, register it in CRDC IndexD, and load its metadata through Dev → QA → Stage → Prod so users can download every Radiology Image for this study from the CTDC Study Details page as one file. The individual object files remain in the bucket; the megazip is created in addition to them."*

2. `### 📦 **Submission & Artifacts**`: Required. A Jira-wiki table. The AWS Account ID and the metadata bucket are constant for CTDC; everything else varies per megazip. Values pending upstream are filled with `To be created` or `TBD`.

   ||Field||Value||Notes||
   |CRDC Submission ID|`<submission-id>`|Issued by the CRDC Submission Portal; one per submission.|
   |AWS Account ID|`101183076466`|Constant for CTDC. The CTDC data commons AWS account.|
   |Metadata S3 Bucket|`nci-cbiit-clinicaltrialdatacommons-metadata`|Constant for CTDC. Holds every release package.|
   |Release Package|`<release-package-directory>`|Directory name within the Metadata S3 Bucket; this study's release artifacts.|
   |Object Files S3 Bucket|`nci-crdc-data-bucket-prod`|CRDC prod data bucket holding the individual object files.|
   |Object Files Directory|`<object-files-directory>`|Source directory of the files; the megazip is written here too.|
   |Megazip File|`<STUDY_DATATYPE>.zip`|Created by the Prefect job; stored in the Object Files Directory alongside the individual files. Use underscores.|
   |Metadata Loading File|To be created|File-metadata loading file for the CRDC Submission Portal CLI.|
   |IndexD Manifest|To be created|`indexd.tsv` for the megazip.|
   |GUID|TBD|Minted in-house for the megazip; `dg.4DFC/` prefix.|

3. `### 🚦 **Workflow**`: Grouped into the three phases. Use Markdown ordered-list markers (`1.`) under each bold phase label so they render as Jira numbered lists; numbering restarts per phase.

   **Create**
   1. Run the Prefect job to create the megazip from the Object Files Directory, writing `<STUDY_DATATYPE>.zip` to that same directory; the individual files must remain in place.
   2. Author the file-metadata loading file for the megazip for use with the CRDC Submission Portal CLI.

   **Index**
   1. Author `indexd.tsv` for the megazip: generate a UUID (e.g., uuidgenerator.net), prepend `dg.4DFC/` to form the GUID, and record the GUID and the `indexd.tsv` AWS location in the Submission & Artifacts table.

   **Load**
   1. Confirm the release package and the megazip loading file in SharePoint before loading.
   2. Run the dedicated Jenkins **Dev** data-loading job; verify the megazip appears on the Study Details page and check application metrics/pages for errors. Record in Testing Signoff.
   3. Run the dedicated Jenkins **QA** data-loading job; assign for QA testing; the tester verifies download of the megazip on the Study Details page. Tester initials Testing Signoff.
   4. Run the dedicated Jenkins **Stage** data-loading job; assign for Stage testing and signoff. Tester initials Testing Signoff.
   5. Run the dedicated Jenkins **Prod** data-loading job; assign for Prod verification and signoff. Tester initials Testing Signoff. **This is the trigger to close the ticket.**

4. `### 🧪 **Verification**`: How CTDC confirms the megazip resolves. Bullet list (italic-labelled, colon separators, the rendering-safe pattern):

   - *How to spot-check*: Resolve the minted GUID at `https://nci-crdc.datacommons.io/index/<GUID>`. A pass returns the full IndexD record with `urls` pointing to the megazip's S3 location and non-empty `size` and `hashes`.
   - *Record the confirmed GUID*: Enter it in the Submission & Artifacts table above.

5. `### ✅ **Testing Signoff**`: The completion record. The tester fills in date and initials per environment as work progresses. **Prod signoff is the trigger to transition the ticket to Closed.**

   ||Environment||Testing Completion Date||Tester Initials||
   |Dev| | |
   |QA| | |
   |Stage| | |
   |Prod| | |

**Standing emoji set (5 entries)**

| Section | Emoji |
|---|---|
| Summary | 🎯 *(shared with Data Loading and IndexD Registration tasks)* |
| Submission & Artifacts | 📦 *(shared with Data Loading and IndexD Registration tasks)* |
| Workflow | 🚦 *(shared with Data Loading and IndexD Registration tasks)* |
| Verification | 🧪 *(shared with IndexD Registration task; GUID resolution spot-check)* |
| Testing Signoff | ✅ *(shared with Data Loading task; per-environment promotion record)* |

**Required content rules**

- **Scope is megazip creation, in-house indexing, and loading only.** Loading an external CRDC submission's study contents uses the Data Loading Task template (Section 7h). Registering study files through the external CTDS handoff uses the IndexD Registration Task template (Section 7g). Schema/model changes use a modeling template (7j/7f). See "When NOT to use this template."
- **The megazip is created in addition to the individual files.** Never describe it as replacing or moving them; the individual object files remain in the Object Files Directory.
- **Megazip filename uses underscores** and is stored inside the Object Files Directory, alongside the individual files, not at the bucket root.
- **The GUID is minted in-house**, with the `dg.4DFC/` prefix. There is no external CTDS/DCFS handoff and no CRINTAKE intake ticket for a megazip.
- **A dedicated Jenkins data-loading job per tier.** Run the correct per-tier job (Dev, QA, Stage, Prod). The lower/upper-tiers split is a `ctdc-model` contribution concept and does **not** apply to data loading.
- **Issue type is Task.** Do not use Story or Subtask.
- **Parent Epic field set via `customfield_12350`.** Default parent CTDC-1664 (CTDC Data Integration) unless a release-specific epic exists.
- **`Relates` link to the download-feature user story** (the megazip exists to serve it), and **`Relates` links to the paired study Index and Load tasks** when they exist. The megazip runs alongside those; use `Relates`, never `Blocks`.
- **Leave the ticket Unassigned at creation** per standing convention. Data management tasks do not require the Developer field.
- **Submission & Artifacts table is mandatory and complete**: the two constant rows (AWS Account ID, Metadata S3 Bucket) are hardcoded; use `To be created` or `TBD` for values pending upstream.
- **Rendering-safe authoring**: `### **Title**` headers (round-trip to `h3.`); italic-label bullets as `* *Label*: content`, not `- **Label:**`; Jira-wiki `||header||` tables; inside table cells use `{{monospace}}` rather than backticks. Push Markdown via `jira_update_issue` (two-step create-then-update) and confirm the render with a UI screenshot; wiki source is not a reliable preview.

**Writing-and-publishing workflow**

1. Confirm the study's individual object files are present in the Object Files Directory and the Release Package exists in the metadata bucket.
2. Confirm this is megazip work, not a plain study load (7h) or an external IndexD registration (7g).
3. Identify the download-feature user story and any paired study Index and Load tasks; add them via native `Relates` links after creation (never `Blocks`).
4. Create via `jira_create_issue` with `issue_type = "Task"`, a placeholder description, and the parent epic via `customfield_12350`. Leave Unassigned.
5. Push the full body via `jira_update_issue` (Markdown in; converts server-side).
6. Add the `Relates` links.
7. Verify the rendered description with a UI screenshot.
8. As Create, Index, and Load progress, fill in the minted GUID, then the per-environment Testing Signoff rows.
9. **Prod signoff is the close trigger**: once the Prod row is filled in, transition to Closed.

**When NOT to use this template**

- **Loading an external CRDC submission's study contents** → Data Loading Task template (Section 7h).
- **Registering study files via the external CTDS/DCFS handoff** → IndexD Registration Task template (Section 7g).
- **Data modeling** → Data Modeling for Study Submission (Section 7f) for study-driven changes, or Data Model Update Task (Section 7j) for internally-driven changes.
- **Software development** → software development template family.
- **CRDC platform changes** (Fence, IndexD, Submission Portal upgrades) → owned by CRDC platform teams; out of CTDC scope.

A megazip task is distinct from its siblings because it is CTDC-internal end to end: the team creates the artifact (Prefect), mints its GUID in-house, and loads it through the per-tier Jenkins jobs, all on one ticket, with both a GUID spot-check and a per-environment Testing Signoff.

**Canonical example**

**CTDC-2104**: *Create, Index, and Load Megazip file for NCTN-NCORP AHEP0731 Image Files*. The ticket carries:

- 5 sections in the standard order (Summary, Submission & Artifacts, Workflow, Verification, Testing Signoff)
- A Submission & Artifacts table with the study's real CRDC Submission ID, Release Package directory, Object Files bucket and directory, and the megazip filename `NCTN_AHEP0731_Radiology_Images.zip` written into the object-files directory
- A three-phase Workflow (Create via Prefect, Index by minting the GUID and authoring `indexd.tsv`, Load through the per-tier Jenkins jobs)
- Both a 🧪 Verification GUID spot-check and a ✅ Testing Signoff table
- `Relates` links to the download-feature user story (CTDC-1909) and to the paired study Index and Load tasks (CTDC-2060, CTDC-2063), set via the Jira native Links panel, not duplicated in the description

**Changelog**

> *Numbering note: the template IDs in the Changelog entries below predate the 2026-06-16 process-order renumber of the data-management templates. The full old -> new mapping is recorded in [`README.md`](./README.md).*

- **v1 (2026-06-11)**: First version. Built from the Data Loading Task (7e) and IndexD Registration Task (7h) templates as the closest siblings, with CTDC-2104 as the canonical example. Carries both Verification and Testing Signoff because a megazip is created, indexed, and loaded on one ticket. Colon/semicolon separators throughout; no em-dashes.
