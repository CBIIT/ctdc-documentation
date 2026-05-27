### 7h. 🔖 IndexD Registration Task Template (v2)

> **Use this template for every CTDC data management task that registers a study's files in CRDC IndexD — minting GUIDs that the downstream Data Loading Task will reference.** The canonical example is **CTDC-2060** (Index NCTN-NCORP TCIA Images-Only AHEP0731 Files), drafted 2026-05-26 as the first Sprint 28 ingestion ticket. CTDC-2060 was authored under this template's v1, then iterated to v2 shape directly in Jira; the v2 template now matches CTDC-2060's structure. This template covers the **upstream artifact creation** work pattern within the loading-data sub-function — it is the prerequisite to a Data Loading Task (Section 7e), not a substitute for one. See "When NOT to use this template" at the end.

**Why this template**

The CTDC team has two primary functions — software development and data management — and data management has two sub-functions: loading data and modeling data. The Data Loading Task template (Section 7e) covers the *promotion of a CRDC submission's contents* into CTDC's databases through Jenkins. But before that load can run, every file in the submission needs a globally unique identifier (GUID) registered in **CRDC IndexD** — and that registration is performed by an **external team at the University of Chicago Center for Translational Data Science (CTDS)**, not by the CTDC engineering team.

CTDC's role in IndexD registration is **coordination**, not engineering: validate the indexd.tsv manifest that ships inside the CRDC Submission Portal's Release Package, hand it off to the external CTDS/DCFS team via the agreed channels, then verify the minted GUIDs resolve correctly before the downstream Data Loading Task can proceed.

**Tasks are operational, not argued.** The v1 of this template was epic-shaped — nine sections including standalone ownership directories and external-coordination contact lists. That structure was right for the deliverables it was modeled after (User Stories, Application Pages Epics, Features Epics), but wrong for a Task. Tasks are operational work units the assignee executes, not deliverables that justify themselves to multiple audiences. The v2 template is task-shaped: **six sections totaling under 700 words, with ownership and external-coordination details folded into the workflow steps where they're used.** The assignee can read this template-shaped ticket and know exactly what to do without paging through narrative.

The five most common antipatterns this template prevents:

1. **Treating IndexD registration as internal pipeline work.** It is not. There is no Jenkins job, no Memgraph write, no environment promotion. IndexD is a single centralized service operated by CTDS/DCFS that mints GUIDs for the entire CRDC platform. The bottleneck is an external team's queue, not our pipeline capacity.
2. **Treating the indexd.tsv as a manifest CTDC authors.** It is not. The indexd.tsv ships *inside* the validated Release Package the CRDC Submission Portal produces. CTDC's role is extraction and validation, not authoring. Earlier versions of this template (and the CTDC-1907 lineage) suggested otherwise.
3. **Duplicating Jira's native Links panel inside the description body.** A standalone "Linked Work" section in a Task description duplicates what the right-sidebar Links panel already shows: Epic Link, Relates links, Blocks links, remote links. The v2 template omits this section entirely — set links via the Jira native mechanisms and trust the sidebar.
4. **Manifest details buried in prose.** The Release Package bucket, the Object Files bucket, the consent group string for the `acl` field — these are the artifacts the external team and the data engineer need at a glance. The Submission & Artifacts table makes them scannable.
5. **No verification step recorded on the ticket.** When CTDS/DCFS reports "registration complete," CTDC needs to verify by resolving a sample of GUIDs against the IndexD resolution endpoint. The Verification section codifies the spot-check method and acceptance criteria so the close trigger is reproducible.

**Service & handoff anatomy (read once before drafting)**

- **CRDC IndexD** — The actual service. Open source, maintained by UChicago CTDS (`github.com/uc-cdis/indexd`). Mints 128-bit GUIDs (`dg.4DFC/00006197-6407-5014-8175-c82efdf6cf0f`) that resolve to physical S3 locations and carry access-control metadata (`acl` / `authz` fields). One centralized instance serves the entire CRDC platform — there is no Dev/QA/Stage/Prod separation for registration the way there is for the CTDC application.
- **Source artifacts** — Release Package + Object Files live in CRDC-owned AWS S3 buckets. The Release Package bucket is universal: `nci-cbiit-clinicaltrialdatacommons-metadata`. The Object Files bucket is typically `nci-crdc-data-bucket-prod`. The **indexd.tsv manifest is part of the Release Package** — do not regenerate it.
- **DCF Google Drive** — The drop-off point for the indexd.tsv copy extracted from the Release Package. CTDS/DCFS monitors this folder for new manifests. Folder: `https://drive.google.com/drive/u/2/folders/1ZVsv2vFEcTPBT2IYsaOb_XCjpWjjMGTb`.
- **CRINTAKE Jira board** — `tracker.nci.nih.gov/projects/CRINTAKE/`. The external team's intake queue. Filing a ticket here tells CTDS the manifest is ready and gives them a place to coordinate the work and report completion.
- **Resolution endpoint** — `https://nci-crdc.datacommons.io/index/<guid>`. Public endpoint for resolving a GUID to its IndexD record. Used for verification spot-checks.
- **Downstream Data Loading Task** — The CTDC ticket that this registration unblocks. Once the GUIDs are minted and verified, the load ticket can proceed with the metadata loading file referencing those GUIDs in its `data_file_uuid` column.

**Section order (6 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Unlike Application Pages or Features epic templates, **this template does not require every section to be present** — if a section has no real content for a given registration, omit the header entirely rather than stub it with "None at this time." Keep the remaining sections in the order shown so a reader scanning multiple registration tickets sees the same visual flow.

1. `### 🎯 **Registration Summary**` — One paragraph. What's being indexed (file count + file type if known), which submission this registration belongs to (named by parent user story key — set via Jira `Relates` link, NOT restated in description), and the downstream Data Loading Task this registration unblocks. **Do not duplicate study identity from the parent submission user story** — chronology, submitter, dbGaP IDs, POC team, SharePoint folders, and study description all live on the parent user story and the registration ticket references them by the native Links panel. Example (from CTDC-2060): *"Register the NCTN-NCORP TCIA Images-Only AHEP0731 study files in CRDC IndexD, minting GUIDs for the object files that the downstream Data Loading Task will reference. This is the first study from the NCTN-NCORP TCIA Images-Only submission (parent CTDC-1805) to receive a consent code (phs004135.c1 / GRU-COL) and proceed to indexing; the other eight studies in the submission remain On Hold pending their own consent codes."*

2. `### 📦 **Submission & Artifacts**` — Required field. A five-row table holding the artifacts the external CTDS team needs to do the work. Study identity (program, study name, dbGaP, submitter, chronology) lives on the parent submission user story linked via the native Links panel — not in this table. The five rows:

   | Field | Value | Notes |
   |---|---|---|
   | CRDC Submission ID | *(Submission Portal ID — one per submission)* | Use the ID issued by the CRDC Submission Portal. |
   | Release Package Location | AWS Bucket: `nci-cbiit-clinicaltrialdatacommons-metadata` | Universal CTDC Release Package bucket. The Release Package contains the metadata TSVs AND the indexd.tsv manifest. |
   | Object Files Location | AWS Bucket: `nci-crdc-data-bucket-prod` *(confirm before assuming)* | GUIDs are listed per-file in the indexd.tsv manifest inside the Release Package, not enumerated here. The bucket is the destination the manifest's `url` column points to. |
   | Consent group / ACL value | ID: *(e.g., `phs004135.c1`)*; Name: *(e.g., `GRU-COL`)*; Number: *(e.g., `1`)* | The ID value is what's placed in the `acl` field of every manifest row. Verify the `acl` value matches on every row of the indexd.tsv during validation (workflow step 1). Wrong value here produces registered records with wrong access control. |
   | Example GUID | *(after registration, the first minted GUID for spot-check reference)* | Used for spot-check anchor in the Verification section. Fill in once the Release Package is generated; the CRDC Submission Portal pipeline assigns the GUIDs ahead of registration. |

   **Naming discipline**: rows that point at *where artifacts live* end in "Location" (e.g., "Release Package Location", "Object Files Location"). This makes the row's purpose unambiguous — the row holds an address, not a content description.

   **Rows omitted compared to v1**: "GUID prefix" (always `dg.4DFC/` for CRDC — implicit; mention only if the study uses a non-standard prefix); "indexd.tsv manifest path" (it's part of the Release Package — saying so in the Release Package row's Notes is sufficient); "CTDC Data Model version" (belongs on the Data Loading Task, not the IndexD ticket — IndexD registration doesn't care about model versions).

3. `### 🚦 **Registration Workflow**` — Numbered list grouped into three phases. Standard CTDC sequence (verified on CTDC-2060):

   **Pre-registration**
   1. Extract the indexd.tsv manifest from the Release Package in the `nci-cbiit-clinicaltrialdatacommons-metadata` bucket. Validate that every row's `acl` field contains the consent group ID, the row count matches the file count for this study, every row's `url` resolves to a real location in the Object Files bucket, and the GUID placeholder format is consistent with the `dg.4DFC/` prefix.

   **External handoff**
   2. Upload the extracted indexd.tsv to the [DCF Google Drive folder](https://drive.google.com/drive/u/2/folders/1ZVsv2vFEcTPBT2IYsaOb_XCjpWjjMGTb) for indexing. Filename convention is preserved from the Release Package; do not rename.
   3. File a CRINTAKE intake ticket on the [CRDC CRs_INTAKE Jira board](https://tracker.nci.nih.gov/projects/CRINTAKE/) describing the study, the name of the indexd.tsv uploaded to DCF Google Drive in step 2, and any requested due date if the registration is time-bound for a planned Data Loading Task.
   4. Link the CRINTAKE ticket back to this CTDC ticket as a Jira-to-Jira remote link.
   5. If a due date is communicated, notify both the NCI CRDC (Leidos) PM and the NCI DCFS PM as early as possible.

   **Confirmation and verification**
   6. Confirm NCI DCFS has received the manifest and acknowledged the CRINTAKE intake ticket.
   7. Once indexing is complete (announced via CRINTAKE or by direct DCFS notification), spot-check minted GUIDs using the example GUID in the Submission & Artifacts section. GUID spot-check success is the trigger to transition the ticket to Closed and unblock the downstream Data Loading Task.

   **Step count: 7 (1 pre-registration, 4 external handoff, 2 confirmation and verification).** v1 had 9 steps; v2 merged the bucket-policy-refresh step into Open Questions (it's an upstream prerequisite, not a workflow step inside this ticket) and merged the example-GUID-backfill step into the close trigger (the Example GUID is captured in the table at ticket creation, not as a post-registration step).

4. `### 🧪 **Verification**` — How CTDC confirms the registration actually worked. Bullet list (italic-labelled, em-dash separators — this is the rendering-safe pattern verified on CTDC-2060):

   - *Spot-check method* — Resolve a sample of GUIDs by hitting the IndexD resolution endpoint at `nci-crdc.datacommons.io/index/` with the GUID appended. The endpoint returns the full IndexD record (`did`, `urls`, `hashes`, `size`, `acl`, `authz`, `rev`, `baseid`).
   - *Successful spot-check criteria* — The endpoint returns a non-error response; the `urls` field contains the expected S3 location in the Object Files bucket; the `acl` field contains the consent group ID; the `size` and `hashes` values are non-empty.
   - *How many GUIDs to spot-check* — At minimum, the first, middle, and last GUIDs in the manifest. If the study has more than 1,000 files, spot-check at least 5, including any GUIDs flagged by CTDS as edge cases.
   - *If a spot-check fails* — Do not close this ticket. Reopen the CRINTAKE ticket with the specific GUIDs and resolution-endpoint responses; coordinate the fix with CTDS before unblocking the downstream load.

5. `### 🔍 **Open Questions / Risks**` — Bullet list (same italic-labelled, em-dash form as Verification). Each bullet is one question or risk. Resolve in ticket comments so the audit trail lives with the registration. Common entries:

   - *Release Package GUID for the release* — May be PLACEHOLDER at ticket creation if the CRDC Submission Portal hasn't completed Release Package generation yet. Patrick Breads or Stephanie Singleton confirms.
   - *Object Files bucket policy refresh* — Has the Object Files bucket policy been refreshed for this submission to allow CTDS read access? DevOps owns; Charles Ngu confirms.
   - *Consent group / ACL value correctness* — Wrong value produces registered records with wrong access control; expensive to correct.
   - *Sequencing with adjacent registrations* — If this submission is one of several with similar timing, consider sequencing to avoid CRINTAKE queue thrash.
   - *GUID prefix variation* — `dg.4DFC/` is the CRDC standard; flag if the study uses a non-standard prefix.

6. `### 📝 **Notes**` — Bullet list. Optional content: terminology glossary, prior registration lessons learned, known constraints. If there's no meaningful note, omit this section entirely. Standard CTDC entries:

   - *Terminology* — GUID is the Globally Unique Identifier (128-bit, IndexD-minted). DCF is the Data Commons Framework. DCFS is the NCI Data Commons Framework Services. CTDS is the UChicago Center for Translational Data Science (operators of IndexD). CRDC is the Cancer Research Data Commons.
   - *Why CTDC-1907 is not the canonical reference* — CTDC-1907 covers the CMB TCIA submission, a different TCIA submission with different artifact paths and a different submission lineage. It is referenced for historical context only.

**Sections omitted compared to v1**

- ❌ **🔗 Linked Work** — Removed. Jira's native Links panel (right sidebar) already shows Epic Link, Relates links, Blocks links, and remote links. Duplicating this content in the description body is noise.
- ❌ **🌐 External Handoff Coordination** — Removed. The CTDS, DCFS, DCF Google Drive folder, CRINTAKE board, and PM contacts are folded into the workflow steps where they're used. A standalone directory section was duplicative.
- ❌ **🤝 Collaboration & Handoffs** — Removed. Ownership stays implicit via the Jira assignee field + comment audit trail. Standalone ownership directory was epic-shaped.

**Standing emoji set (6 entries)**

| Section | Emoji |
|---|---|
| Registration Summary | 🎯 *(shared with Data Loading Task)* |
| Submission & Artifacts | 📦 *(shared with Data Loading Task)* |
| Registration Workflow | 🚦 *(shared with Data Loading Task)* |
| Verification | 🧪 *(shared with Data Loading Task; scoped to GUID resolution spot-check)* |
| Open Questions / Risks | 🔍 *(shared with Data Loading Task)* |
| Notes | 📝 *(shared with Data Loading Task)* |

**Required content rules**

- **Scope is IndexD registration only.** Minting GUIDs for files via the external CTDS/DCFS handoff. **Data loading** uses the Data Loading Task template (Section 7e). **Schema or model changes** use the Data Modeling for Study Submission template (Section 7g) or the Data Model Update Task template (Section 7f). See "When NOT to use this template" below.
- **No Acceptance Criteria section.** IndexD registration is operational SOP work; the completion bar is the GUID spot-check passing (Section 4). AC belongs on user stories, not on Tasks.
- **One Task per registration handoff** — one CRINTAKE intake ticket, one IndexD registration ticket on the CTDC side. If a single Data Loading Task depends on two separate registrations (e.g., one for the Release Package, one for the Object Files), file two registration tickets and have the load ticket linked from both via `Blocks`.
- **Issue type is Task** on this tracker, matching the convention used on CTDC-2060. Do not use Story or Subtask.
- **Parent Epic field set via `customfield_12350`.** Default parent is CTDC-1664 (CTDC Data Integration) unless a release-specific epic exists.
- **`Relates` link to the parent submission user story is mandatory.** Set via `jira_create_issue_link` after ticket creation. The parent user story is the canonical record of study identity — program, study name, dbGaP IDs, submitter, POC team, chronology, SharePoint folders, study description. The Task description does not duplicate that content.
- **`Relates` link to the study-specific Data Hub tracking ticket (DHDM-XXX) when one exists.** Set the same way as the parent submission user story link. This gives the ticket two anchors: the program-level user story (CTDC-XXXX) and the study-specific Data Hub tracker (DHDM-XXX). Verified on CTDC-2060 (linked to CTDC-1805 + DHDM-143).
- **`Blocks` link to the downstream Data Loading Task is mandatory** when that load ticket exists. Pass the registration ticket as the inward issue, the load ticket as the outward issue. If the load ticket has not been filed yet at registration ticket creation time, add the link as soon as the load ticket exists.
- **Remote link to the CRINTAKE ticket is mandatory** once workflow step 4 is complete. Use `jira_create_remote_issue_link` with the CRINTAKE ticket URL. A free-text reference to the CRINTAKE ticket key is **not** sufficient — the remote link makes the cross-project dependency visible from both sides.
- **Submission & Artifacts table is mandatory and complete at ticket creation.** All five rows present. Use PLACEHOLDER explicitly when a value is pending upstream — never silently omit a row.
- **Spot-check method and acceptance criteria explicit in the Verification section.** Naming "spot-check the GUIDs" without a method or success criteria is a gap; the spot-check is the verified close trigger and needs to be reproducible by anyone reading the ticket.
- **Rendering-safe authoring patterns** — section headers use `### **Title**` Markdown form (round-trips cleanly to `h3.` Jira-wiki); bullet lists with italic labels use `* *Label* — content` (italic-and-em-dash), NOT `- **Label:** content` (bold-and-colon, which the converter collapses to broken `**...:*` mismatched-asterisk damage); tables use Jira-wiki `||header||` syntax, NOT GitHub-flavored Markdown `|h|h|`. Verified on CTDC-2060 second push.
- **Empty sections are omitted, not stubbed.** Unlike the Data Loading Task template, this template does not require "None at this time" placeholders for empty sections. If a section has no real content for this registration, leave it out — the structure stays clean and the remaining sections retain their order.

**Writing-and-publishing workflow**

1. Confirm the upstream artifacts exist before drafting the registration ticket. If the Release Package isn't generated yet in `nci-cbiit-clinicaltrialdatacommons-metadata`, or the Object Files aren't in `nci-crdc-data-bucket-prod`, or the consent code isn't confirmed, the registration ticket is premature — those upstream items should land first.
2. Confirm this work is IndexD registration, not data loading or modeling. If the work is promoting an existing-and-registered submission through Jenkins, use the Data Loading Task template (Section 7e). If the schema is changing, use a modeling template (Section 7f or 7g).
3. **Identify the parent submission user story and the study-specific Data Hub tracker.** Every CTDC data submission has a program-level user story (e.g., CTDC-1805) and per-study Data Hub tracking tickets (e.g., DHDM-143 for AHEP0731). Both go on the ticket via `Relates` links after creation.
4. Confirm the downstream Data Loading Task exists (or will exist before this registration completes). The two tickets are paired by design; registration without a downstream load is unusual and should be questioned.
5. Create the IndexD registration task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields` (default: CTDC-1664). Assign to the TPM initially — the TPM owns the external coordination through CRINTAKE.
6. Push the full description in a second call via `jira_update_issue` with the full Markdown body. Same two-step pattern as the Data Loading Task and Features templates.
7. Add `Relates` links from the registration ticket to the parent submission user story AND the study-specific DHDM tracker using `jira_create_issue_link`. Order: registration ticket as inward issue.
8. Add a `Blocks` link from the registration ticket to the downstream Data Loading Task using `jira_create_issue_link`. Registration is the inward issue; load is the outward issue.
9. Verify the rendered description with a UI screenshot.
10. As the workflow progresses, add the CRINTAKE remote link via `jira_create_remote_issue_link` once step 3 of the workflow is complete.
11. After GUID spot-check passes (Verification section), transition the ticket to Closed with resolution `Fixed`. **GUID spot-check success is the close trigger.**

**When to expand vs trim**

- **Standard single-study registration** → use the template as written; expect 6 sections present.
- **Multi-manifest registration** (one study, multiple manifest files) → expand the Submission & Artifacts table with manifest-specific rows, or add a row per manifest; expand the workflow step 2 to enumerate each manifest by filename. Keep one ticket — the CRINTAKE intake is one unit of work.
- **Re-registration after a file correction** → use the template as written, and use the 📝 Notes section to explain why the re-registration is needed and reference IndexD's `baseid` / `rev` versioning model.
- **Tiny registration (under 50 files, no consent-group complexity)** → omit Open Questions / Risks and Notes entirely; the Workflow and Verification sections are sufficient.

**When NOT to use this template**

The CTDC team has two primary functions: software development and data management. Data management has two sub-functions: loading data and modeling data. Within loading data, there are two work patterns: *promoting a CRDC submission's contents into CTDC's databases* (Data Loading Task — Section 7e) and *creating upstream artifacts that the load consumes* (this template). This template covers the IndexD registration work pattern only.

**Data loading** — does NOT use this template. Use the **Data Loading Task** template (Section 7e) instead. That sibling template covers the actual end-to-end promotion of metadata through Dev → QA → Stage → Prod after IndexD has minted the GUIDs.

**Data modeling** — does NOT use this template. Use the **Data Modeling for Study Submission** template (Section 7g) for study-driven model additions, or the **Data Model Update Task** template (Section 7f) for infrastructure-level model changes.

**Software development work** — does NOT use this template. Use the appropriate template from the software development family.

**Other upstream artifact creation work** — partially overlaps. Megazip generation and metadata loading file creation are also upstream artifact work in the data management lane, but they are CTDC-internal operations, not external handoffs. Until those work patterns have their own template, file them as standalone Tasks under CTDC-1664 (CTDC Data Integration) with a free-form description and link them from the Data Loading Task's Linked Work via the native Jira mechanism. If a recurring pattern emerges, draft a template using this one as the closest sibling.

**CRDC platform changes** — Fence, IndexD, Submission Portal upgrades owned by CRDC platform teams. Out of CTDC scope entirely; CTDC files dependency tickets if affected, but does not own the work.

**Canonical example**

**CTDC-2060** — *Index NCTN-NCORP TCIA Images-Only AHEP0731 Files* (drafted 2026-05-26). This is the v2-shaped canonical reference. The ticket carries:

- 6 sections in the v2 order (Registration Summary, Submission & Artifacts, Registration Workflow, Verification, Open Questions / Risks, Notes)
- 5-row Submission & Artifacts table with the "Location" naming discipline
- 7-step workflow grouped Pre-registration / External handoff / Confirmation and verification
- `Relates` links to CTDC-1805 (program-level user story) and DHDM-143 (study-specific Data Hub tracker), set via Jira native Links panel — not duplicated in description
- Parent Epic CTDC-1664 set via `customfield_12350`

The retrofit recommendation that existed in v1 (against CTDC-1907) has been removed — CTDC-1907 is a different TCIA submission lineage (CMB, not Images-Only) and was never going to be the right canonical anchor for this template. The 📝 Notes section preserves the brief explanation for historical context only.
