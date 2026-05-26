### 7h. 🔖 IndexD Registration Task Template (Drafted)

> **Use this template for every CTDC data management task that registers a study's files in CRDC IndexD — minting GUIDs that the downstream Data Loading Task will reference.** Canonical example: **CTDC-1907** (Index NCTN-NCORP TCIA CMB Image Files, 2,632 radiology files, closed 2026-03-11). CTDC-1907 is canonical for the **workflow shape** (manifest upload → CRINTAKE → DCFS confirmation → GUID spot-check), but predates the parent-submission-user-story pattern (see Section 2 below); a one-time retrofit of CTDC-1907 to add the parent user story link is recommended as part of adopting this template. This template covers the **upstream artifact creation** work pattern within the loading-data sub-function — it is the prerequisite to a Data Loading Task (Section 7e), not a substitute for one. See "When NOT to use this template" at the end.

**Why this template**

The CTDC team has two primary functions — software development and data management — and data management has two sub-functions: loading data and modeling data. The Data Loading Task template (Section 7e) covers the *promotion of a CRDC submission's contents* into CTDC's databases through Jenkins. But before that load can run, every file in the submission needs a globally unique identifier (GUID) registered in **CRDC IndexD** — and that registration is performed by an **external team at the University of Chicago Center for Translational Data Science (CTDS)**, not by the CTDC engineering team.

CTDC's role in IndexD registration is **coordination**, not engineering: prepare and validate the IndexD manifest, hand it off to the external CTDS/DCFS team via the agreed channels, then verify the minted GUIDs resolve correctly before the downstream Data Loading Task can proceed.

This template formalizes that pattern. The most common antipatterns we've seen on registration tickets:

1. **Treating IndexD registration as internal pipeline work.** It is not. There is no Jenkins job, no Memgraph write, no environment promotion. IndexD is a single centralized service operated by CTDS/DCFS that mints GUIDs for the entire CRDC platform. Writing the ticket as if it's a deployment obscures that the bottleneck is an external team's queue, not our pipeline capacity.
2. **No explicit link to the downstream Data Loading Task.** Registration tickets exist *to unblock* a specific load. If the load ticket isn't referenced (and ideally linked via `Blocks`), the registration's purpose is unclear and the dependency is invisible to anyone reading either ticket alone.
3. **Manifest details buried in prose.** The CRDC Submission Portal Release Package GUID, the Object Files bucket GUID, the SharePoint path to the IndexD manifest, the consent group string for the ACL field — these are the artifacts the external team needs. If they're buried in narrative description, the data engineer has to dig.
4. **No verification step recorded on the ticket.** When CTDS/DCFS reports "registration complete," CTDC needs to verify by resolving a sample of GUIDs against `https://nci-crdc.datacommons.io/index/<guid>`. If that spot-check isn't recorded on the ticket, there's no audit trail proving the registration actually worked before the load proceeded.
5. **Duplicating study identity instead of linking to the parent submission user story.** Every CTDC data submission has a parent user story that owns study identity (program, study name, dbGaP ID, submitter, POC team, chronology, document references, SharePoint folders). Re-stating that content on the registration ticket creates two sources of truth that drift. The registration ticket references the parent; it does not re-state the parent.

The template below resolves all five with four commitments:

1. **Workflow framed as external handoff.** The four numbered steps mirror the workflow CTDC-1907 used in practice: manifest upload → CRINTAKE intake ticket → DCFS confirmation → GUID spot-check. Each step names the external system and the responsible party.
2. **Explicit `Blocks` link to the downstream Data Loading Task.** The registration ticket blocks the load ticket. The link is required at ticket creation when the load ticket exists, or as soon as it's filed.
3. **Required Jira-to-Jira remote link to the CRINTAKE ticket** once step 2 is complete. The CRINTAKE ticket is the external team's tracking artifact and lives on a separate Jira project on the same instance. Linking it back makes the registration's external dependency visible without leaving the CTDC ticket.
4. **Mandatory `Relates` link to the parent submission user story.** Per the universal CTDC data-submission pattern (SKILL.md Section 7d, verified on CTDC-2051 ↔ CTDC-1666 and CTDC-1799 ↔ CTDC-1804), the parent user story carries study identity and chronology; the registration ticket references it via `Relates` and does not duplicate its content. The Parent Epic field (`customfield_12350`) stays set to CTDC-1664 (CTDC Data Integration) — the user story link is a separate relationship, not a re-parenting.

**Service & handoff anatomy (read this once before drafting)**

A CTDC IndexD registration touches systems across two organizations. Knowing the chain helps every section of the template land:

- **CRDC IndexD** — The actual service. Open source, maintained by UChicago CTDS (`github.com/uc-cdis/indexd`). Mints 128-bit GUIDs (`dg.4DFC/00006197-6407-5014-8175-c82efdf6cf0f`) that resolve to physical S3 locations and carry access-control metadata (`acl` / `authz` fields). One centralized instance serves the entire CRDC platform — there is no Dev/QA/Stage/Prod separation for registration the way there is for the CTDC application.
- **Source artifacts** — Release Package + Object Files live in CRDC-owned AWS S3 buckets (typically `nci-cbiit-clinicaltrialdatacommons-metadata` for the package and `nci-crdc-data-bucket-prod` for objects). The IndexD manifest TSV lives in **SharePoint** under the study's release folder until it's handed off.
- **DCF Google Drive** — The drop-off point for the IndexD manifest TSV. CTDS/DCFS monitors this folder for new manifests. The folder URL is fixed (see Section 5 below).
- **CRINTAKE Jira board** — `tracker.nci.nih.gov/projects/CRINTAKE/`. The external team's intake queue. Filing a ticket here tells CTDS the manifest is ready and gives them a place to coordinate the work and report completion.
- **NCI CRDC PM** (Leidos) and **NCI DCFS PM** — The two PMs who get notified when a due date matters. Not always required, but expected if the registration is time-bound.
- **Resolution endpoint** — `https://nci-crdc.datacommons.io/index/<guid>`. Public endpoint for resolving a GUID to its IndexD record. Used for verification spot-checks.
- **Downstream Data Loading Task** — The CTDC ticket that this registration unblocks. Once the GUIDs are minted and verified, the load ticket can proceed with the metadata loading file referencing those GUIDs in its `data_file_uuid` column.

The template walks all of this in order: artifacts → workflow → external handoff coordination → verification.

**Section order (9 sections, recommended sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Unlike the Data Loading Task and Application Pages epic templates, **this template does not require every section to be present**. IndexD registration work is short and bounded enough that strict 9-section discipline would add noise. If a section genuinely has no content, omit the header entirely rather than writing "None at this time" — but keep the remaining sections in the order shown so a reader scanning multiple registration tickets sees the same visual flow.

1. `### 🎯 **Registration Summary**` — One paragraph. What's being indexed (file count + file type), which submission this registration belongs to (named by parent user story key), and the downstream Data Loading Task this registration unblocks. **Do not duplicate study identity from the parent submission user story** — chronology, submitter, dbGaP IDs, POC team, SharePoint folders, and study description all live on the parent user story and the registration ticket references them by link. The summary names *this registration* — what files, how many, which load it unblocks. Example: *"Register 2,632 radiology image files in CRDC IndexD for the NCTN-NCORP TCIA Images-Only submission (parent user story CTDC-1805). Files have been validated in the CRDC Submission Portal and uploaded to the production CRDC object storage bucket. GUIDs minted here will be referenced by the metadata loading file in the downstream Data Loading Task CTDC-XXXX."*

2. `### 🔗 **Linked Work**` — Bullet list of every related ticket. Required entries when applicable:
   - **Parent Submission User Story:** CTDC-XXXX — *Submission name* (linked via `Relates`; **this is the source of truth for study identity** — chronology, submitter, POC team, dbGaP IDs, SharePoint folders, study description. The registration ticket does not duplicate that content.)
   - **Parent Epic:** CTDC-1664 — *CTDC Data Integration* (the standing data integration epic; set via `customfield_12350`. The user story link above is a `Relates` relationship, not a re-parent.)
   - **Downstream Data Loading Task:** CTDC-XXXX — *Load [study] [release] release package* (linked via `Blocks`, with this registration ticket as the inward issue; see Section 10 of SKILL.md for the link-type rule)
   - **External CRINTAKE ticket:** CRINTAKE-XXXX — *(filed during workflow step 2; added as a Jira-to-Jira remote link, not a free-text reference)*
   - **Upstream artifact tickets:** CTDC-XXXX — *(megazip creation, file inventory, or other upstream artifact tickets if this registration consumes their outputs)*
   - **Related registrations:** CTDC-XXXX — *(if this registration supersedes or follows a prior registration that needs to be referenced, e.g., a re-registration after a file correction)*

3. `### 📦 **Submission & Artifacts**` — Required field. The single source of truth for *where every artifact this registration needs lives*. Study identity (program, study name, dbGaP, submitter, chronology) lives on the parent submission user story named in Section 2 — this table focuses on the artifacts the external CTDS team needs to do the work. Fill in every row at ticket creation; leave PLACEHOLDER values only for items genuinely pending upstream work, and update them as soon as the data engineer or DevOps confirms them. **A reader of this ticket should be able to find every artifact without asking anyone.**

   | Field | Value | Notes |
   |---|---|---|
   | CRDC Submission ID | *(Submission Portal ID — one per submission)* | Use the ID issued by the CRDC Submission Portal. |
   | Release Package | AWS Bucket + GUID | The bucket holding the metadata Release Package, and its GUID identifier. CTDC-1907 example: `nci-cbiit-clinicaltrialdatacommons-metadata` / `1f63fbf5-953d-4668-8923-89f303a904b9`. |
   | Object Files | AWS Bucket + GUID | The bucket holding the physical object files, and its GUID identifier. CTDC-1907 example: `nci-crdc-data-bucket-prod` / `9c3d4b26-ebbd-4cc1-9e41-4ed1075024b6`. |
   | IndexD manifest (SharePoint path) | *(SharePoint URL to the manifest TSV)* | The TSV the external CTDS team consumes. Standard naming: `<timestamp>-<release-package-guid>-indexd.tsv`. Lives in the study's SharePoint release folder until uploaded to DCF Google Drive. |
   | GUID prefix | `dg.4DFC/` *(standard CRDC prefix — confirm before assuming)* | The CRDC-assigned IndexD prefix for the resulting GUIDs. `dg.4DFC/` is the standard for CRDC; verify before drafting if a different prefix applies to this study. |
   | Consent group / ACL value | *(e.g., the study's consent group string)* | Must be added to the `acl` field for every row in the IndexD manifest before upload. The external team will not infer this — if it's wrong, the registered records will have wrong access control. |
   | Example GUID | *(after registration, the first minted GUID for spot-check reference)* | Filled in after registration. Used in workflow step 4 for verification. |

   Pending values: PLACEHOLDER. Confirm with **Patrick Breads** or **Stephanie Singleton** (data engineering — manifest TSV preparation), **Charles Ngu** (DevOps — bucket access and policy), and the CRDC Submission Portal team for Submission IDs.

4. `### 🚦 **Registration Workflow**` — Numbered list of the end-to-end registration steps. Standard CTDC sequence (verified on CTDC-1907):

   **Pre-registration (one-time)**
   1. Locate the IndexD manifest TSV in the SharePoint folder named in Section 3. Open the TSV and confirm:
      - Every row has the expected file metadata (GUID placeholder, file URL, hash, size, file name)
      - The `acl` field on every row contains the consent group string named in Section 3
      - The row count matches the file count in Section 3
      - Each file URL resolves to a real location in the Object Files bucket named in Section 3
   2. Confirm the Object Files bucket policy permits CTDS read access for the listed files. **DevOps owns this** (Charles Ngu); if the bucket policy hasn't been refreshed for this submission, registration will fail with permission errors on the CTDS side.

   **External handoff**
   3. **Upload the IndexD manifest TSV to the DCF Google Drive folder** for indexing: [DCF Google Drive](https://drive.google.com/drive/u/2/folders/1ZVsv2vFEcTPBT2IYsaOb_XCjpWjjMGTb). Once uploaded, the file is visible to the CTDS/DCFS team. Filename convention: keep the standard `<timestamp>-<release-package-guid>-indexd.tsv` form so the external team can match it to the intake ticket.
   4. **File a CRINTAKE intake ticket** on the [CRDC CRs_INTAKE Jira board](https://tracker.nci.nih.gov/projects/CRINTAKE/). The ticket description must include:
      - A description of the study and the data release being indexed
      - The exact name(s) of the manifest(s) uploaded to DCF Google Drive in step 3
      - A requested due date if the registration is time-bound (e.g., gating a planned Data Loading Task)
   5. **Link the CRINTAKE ticket back to this CTDC ticket as a Jira-to-Jira remote link.** Both tickets live on the same Jira instance (`tracker.nci.nih.gov`), so a remote link is straightforward and gives readers of either ticket visibility into the cross-project dependency. Update Section 2 (Linked Work) with the CRINTAKE ticket key as soon as it's filed.
   6. **If a due date is communicated**, notify both the NCI CRDC (Leidos) PM and the NCI DCFS PM as soon as possible. The CRINTAKE description alone is not sufficient when timing matters.

   **Confirmation and verification**
   7. **Confirm the NCI DCFS team has received the manifest** and acknowledged the CRINTAKE intake ticket. This is the "in queue" signal; registration itself may not start immediately.
   8. **Once indexing is complete** (announced via the CRINTAKE ticket or by direct notification from DCFS), spot-check minted GUIDs by accessing the IndexD resolution endpoint. See Section 6 (Verification) for the method.
   9. **Update Section 3 with the example GUID** used in spot-checking. This is the artifact downstream readers reference when troubleshooting GUID resolution issues. **GUID spot-check success is the trigger to transition the ticket to Closed and unblock the downstream Data Loading Task.**

5. `### 🌐 **External Handoff Coordination**` — Names and channels for the cross-organizational seam. The standard CTDC set:
   - **CTDS (UChicago Center for Translational Data Science)** — The team operating CRDC IndexD. Open-source service at `github.com/uc-cdis/indexd`. Indexing requests are queued via the CRINTAKE board.
   - **NCI DCFS (Data Commons Framework Services)** — The NCI-side counterpart that coordinates with CTDS. Receives manifests via DCF Google Drive and tracks intake via CRINTAKE.
   - **DCF Google Drive folder** — `https://drive.google.com/drive/u/2/folders/1ZVsv2vFEcTPBT2IYsaOb_XCjpWjjMGTb`. Manifest upload location.
   - **CRINTAKE Jira board** — `https://tracker.nci.nih.gov/projects/CRINTAKE/`. Intake ticketing.
   - **NCI CRDC PM (Leidos)** — Notify when a due date matters.
   - **NCI DCFS PM** — Notify when a due date matters.
   - **Communication norm**: if the registration is time-bound, notify both PMs as early as possible — CRINTAKE intake ticket alone does not guarantee timing.

6. `### 🧪 **Verification**` — How CTDC confirms the registration actually worked. The verified method:
   - **Spot-check method**: resolve a sample of GUIDs by hitting the public IndexD resolution endpoint: `https://nci-crdc.datacommons.io/index/<guid>`. The endpoint returns the full IndexD record (`did`, `urls`, `hashes`, `size`, `acl`, `authz`, `rev`, `baseid`) for the GUID.
   - **What counts as a successful spot-check**:
     - The endpoint returns a non-error response
     - The `urls` field contains the expected S3 location in the Object Files bucket
     - The `acl` field contains the consent group string from Section 3
     - The `size` and `hashes` values are non-empty
   - **How many GUIDs to spot-check**: at minimum, the first, middle, and last GUIDs in the manifest. For registrations larger than 1,000 files, spot-check at least 5 — including any GUIDs flagged by CTDS as edge cases (large files, files with non-standard hashes, files added late in the registration).
   - **What to do if a spot-check fails**: do not close the ticket. Reopen the CRINTAKE ticket with the specific GUIDs and resolution-endpoint responses; coordinate the fix with CTDS before unblocking the downstream load.

7. `### 🤝 **Collaboration & Handoffs**` — Bullet list of the people and touchpoints required for this registration. The standard CTDC set:
   - **Data engineering** — owns IndexD manifest TSV preparation and consent group / ACL configuration (Patrick Breads, Stephanie Singleton)
   - **DevOps** — owns AWS bucket policy refresh for CTDS read access (Charles Ngu)
   - **TPM** — owns CRINTAKE ticket filing, external PM communication, end-to-end coordination, and Section 9 close-out (Gina Kuffel)
   - **External: CTDS (UChicago)** — performs the registration; queue managed via CRINTAKE
   - **External: NCI DCFS** — coordinates with CTDS; tracks intake; reports completion
   - **Downstream Data Loading Task owner** — consumes the minted GUIDs; named on the linked Data Loading Task (Section 2)

8. `### 🔍 **Open Questions / Risks**` — Bullet list of decisions or unknowns to resolve. Each bullet is one question. Resolve in ticket comments so the audit trail lives with the registration. Common entries on IndexD registrations:
   - Has the Object Files bucket policy been refreshed for this submission to allow CTDS read access? (DevOps owns; Charles Ngu confirms.)
   - Is the consent group / ACL value correct for the study's controlled-access tier? Wrong value here results in registered records with wrong access control and is expensive to correct.
   - Is the GUID prefix correct for this registration? `dg.4DFC/` is the CRDC standard but study-specific prefixes occasionally apply.
   - Is the requested due date realistic given CTDS's current queue depth? If unknown, ask via the CRINTAKE ticket.
   - If files in this submission already have GUIDs from a prior partial registration, do they need to be re-used or replaced? (Re-use is preferred per IndexD's `baseid` / `rev` versioning model.)

9. `### 📝 **Notes**` — Bullet list. Optional content: prior registration lessons learned, terminology translations, known constraints. If there's no meaningful note, omit this section entirely. Common content:
   - **Terminology**: *GUID* = Globally Unique Identifier (128-bit, IndexD-minted); *DCF* = Data Commons Framework; *DCFS* = NCI Data Commons Framework Services; *CTDS* = UChicago Center for Translational Data Science (operators of IndexD); *CRDC* = Cancer Research Data Commons.
   - **Prefix reference**: CRDC GUIDs use the `dg.4DFC/` prefix and resolve via `dataguids.org` distributed resolution if accessed without an instance-specific endpoint.
   - **Re-registration pattern**: if a file's content changes after registration, IndexD requires a new GUID. The old and new GUIDs are logically linked via a shared `baseid`; the loader should be pointed at the latest version via the IndexD API's "latest" endpoint.

**Standing emoji set (9 entries)**

| Section | Emoji |
|---|---|
| Registration Summary | 🎯 *(shared with data loading task)* |
| Linked Work | 🔗 *(shared with data loading task)* |
| Submission & Artifacts | 📦 *(shared with data loading task)* |
| Registration Workflow | 🚦 *(shared with data loading task — same operational shape: numbered workflow)* |
| External Handoff Coordination | 🌐 *(unique to IndexD registration task — names the cross-organizational seam)* |
| Verification | 🧪 *(shared with data loading task; scoped to GUID resolution spot-check, not application surfaces)* |
| Collaboration & Handoffs | 🤝 *(shared with data loading task)* |
| Open Questions / Risks | 🔍 *(shared with data loading task)* |
| Notes | 📝 *(shared with data loading task)* |

**Required content rules (IndexD Registration Task specific — universal Jira rules in 7b-shared also apply)**

- **Scope is IndexD registration only.** Minting GUIDs for files via the external CTDS/DCFS handoff. **Data loading — promoting a CRDC submission's contents into CTDC's databases — does not use this template.** Use the Data Loading Task template (Section 7e), which is the downstream sibling. **Schema or model changes do not use this template** — use the Data Modeling for Study Submission template (Section 7g) or the Data Model Update Task template (Section 7f). See "When NOT to use this template" at the end.
- **No Acceptance Criteria section.** IndexD registration is operational SOP work; the completion bar is the GUID spot-check passing (Section 6). AC belongs on user stories in the software development lane.
- **One Task per registration handoff** — one CRINTAKE intake ticket, one IndexD registration ticket on the CTDC side. If a single Data Loading Task depends on two separate registrations (e.g., one for the Release Package, one for the Object Files), file two registration tickets and have the load ticket linked from both via `Blocks`.
- **Issue type is Task** on this tracker, matching the convention used on CTDC-1907. Do not use Story or Subtask.
- **Parent Epic field set on the ticket itself** via `customfield_12350`. Default parent is CTDC-1664 (CTDC Data Integration) unless a release-specific epic exists.
- **`Relates` link to the parent submission user story is mandatory** when one exists. The parent user story is the canonical record of study identity — program, study name, dbGaP IDs, submitter, POC team, chronology, SharePoint folders, study description. The registration ticket references the parent via a `Relates` link (matching the Data Modeling for Study Submission template pattern verified on CTDC-2051 ↔ CTDC-1666 and CTDC-1799 ↔ CTDC-1804) and does not duplicate the parent's content. The Parent Epic field (`customfield_12350`) stays set to CTDC-1664 — the user story link is a separate relationship, not a re-parent. The expected example for the NCTN-NCORP TCIA Images-Only submission is CTDC-1805.
- **`Blocks` link to the downstream Data Loading Task is mandatory** when that load ticket exists. Pass the registration ticket as the inward issue and the load ticket as the outward issue (per SKILL.md Section 10's link-type rule on this tracker). If the load ticket has not been filed yet at registration ticket creation time, add the link as soon as the load ticket exists.
- **Remote link to the CRINTAKE ticket is mandatory** once step 2 of the workflow is complete. Use `jira_create_remote_issue_link` with the CRINTAKE ticket URL (e.g., `https://tracker.nci.nih.gov/browse/CRINTAKE-XXXX`). A free-text reference to the CRINTAKE ticket key is **not** sufficient — the remote link makes the cross-project dependency visible from both sides.
- **Submission & Artifacts table is mandatory and complete.** All required anchor fields (CRDC Submission ID, Release Package, Object Files, IndexD manifest path, GUID prefix, Consent group / ACL value) must be present. Study identity (program, study name, dbGaP, submitter, file count) lives on the parent submission user story, not in this table — file count goes in the Registration Summary, not in the artifacts table. Use PLACEHOLDER explicitly when a value is pending upstream — never silently omit a row.
- **Spot-check method and acceptance criteria explicit in Section 6.** Naming "spot-check the GUIDs" without a method or success criteria is a gap; the spot-check is the verified close trigger and needs to be reproducible by anyone reading the ticket.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as every other Jira description on this tracker.
- **Empty sections are omitted, not stubbed.** Unlike the Data Loading Task template, this template does not require "None at this time" placeholders for empty sections. If a section has no real content for this registration, leave it out — the structure stays clean and the remaining sections retain their order.

**Writing-and-publishing workflow**

1. Confirm the upstream artifacts exist before drafting the registration ticket. If the IndexD manifest TSV isn't in SharePoint yet, or the Object Files aren't in the AWS bucket yet, or the consent group / ACL value isn't confirmed, the registration ticket is premature — those upstream tasks should land first.
2. Confirm this work is IndexD registration, not data loading or modeling. If the work is promoting an existing-and-registered submission through Jenkins, use the Data Loading Task template (Section 7e). If the schema is changing, use a modeling template (Section 7f or 7g).
3. **Identify the parent submission user story.** Every CTDC data submission has one — it tracks the submission's chronology, submitter, dbGaP IDs, POC team, and document references. If the parent user story doesn't exist yet, the submission isn't far enough along for an IndexD registration ticket to be useful. If the parent exists but is in the wrong status (e.g., `On Hold` pending consent codes that the registration depends on), flag in Section 8 (Open Questions / Risks) before proceeding.
4. Confirm the downstream Data Loading Task exists (or will exist before this registration completes). The two tickets are paired by design; registration without a downstream load is unusual and should be questioned.
5. Create the IndexD registration task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields` (default: CTDC-1664). Assign to the TPM (Gina Kuffel) initially — the TPM owns the external coordination through CRINTAKE.
6. Push the description in two steps: create with the placeholder, then `jira_update_issue` with the full Markdown body. Same two-step pattern as the Data Loading Task and Features templates.
7. Add a `Relates` link between the registration ticket and the parent submission user story using `jira_create_issue_link`. Order does not matter for `Relates`, but convention: pass the registration ticket as the inward issue.
8. Add a `Blocks` link between the registration ticket and the downstream Data Loading Task using `jira_create_issue_link`. Registration is the inward issue; load is the outward issue.
9. Verify the rendered description with a UI screenshot.
10. As the workflow progresses, update the description with the CRINTAKE ticket key once step 2 of the registration workflow is complete (Section 2 — Linked Work), and add the CRINTAKE remote link via `jira_create_remote_issue_link`.
11. After GUID spot-check passes (Section 6), update Section 3 with the example GUID for downstream reference, then transition the ticket to Closed with resolution `Fixed`. **GUID spot-check success is the close trigger.**

**When to expand vs trim**

- **Standard single-study registration** → use the template as written; expect 6–9 sections present, depending on whether Notes adds value.
- **Multi-manifest registration** (one study, multiple manifest files) → expand Section 3 (Submission & Artifacts) to a table per manifest, or add manifest-specific rows; expand Section 4 (Registration Workflow) step 3 to enumerate each manifest by filename. Keep one ticket — the CRINTAKE intake is one unit of work.
- **Re-registration after a file correction** → use the template as written, and use Section 2 (Linked Work) → "Related registrations" to point at the original (now-superseded) registration. Section 9 (Notes) should explain *why* the re-registration is needed and reference IndexD's `baseid` / `rev` versioning model.
- **Tiny registration (under 50 files, no consent-group complexity)** → omit Open Questions / Risks (Section 8) and Notes (Section 9) entirely; the workflow steps and verification are sufficient.

**When NOT to use this template**

The CTDC team has two primary functions: software development and data management. Data management has two sub-functions: loading data and modeling data. Within loading data, there are two work patterns: *promoting a CRDC submission's contents into CTDC's databases* (Data Loading Task — Section 7e) and *creating upstream artifacts that the load consumes* (this template). This template covers the IndexD registration work pattern only.

**Data loading** — does NOT use this template. Use the **Data Loading Task** template (Section 7e) instead. That sibling template covers the actual end-to-end promotion of metadata through Dev → QA → Stage → Prod after IndexD has minted the GUIDs.

**Data modeling** — does NOT use this template. Use the **Data Modeling for Study Submission** template (Section 7g) for study-driven model additions, or the **Data Model Update Task** template (Section 7f) for infrastructure-level model changes.

**Software development work** — does NOT use this template. Use the appropriate template from the software development family.

**Other upstream artifact creation work** — partially overlaps. Megazip generation and metadata loading file creation are also upstream artifact work in the data management lane, but they are CTDC-internal operations, not external handoffs. Until those work patterns have their own template, file them as standalone Tasks under CTDC-1664 (CTDC Data Integration) with a free-form description and link them from the Data Loading Task's Section 2 (Linked Work). If a recurring pattern emerges, draft a template using this one as the closest sibling.

**CRDC platform changes** — Fence, IndexD, Submission Portal upgrades owned by CRDC platform teams. Out of CTDC scope entirely; CTDC files dependency tickets if affected, but does not own the work.

**One-time retrofit: CTDC-1907**

CTDC-1907 (Index NCTN-NCORP TCIA CMB Image Files, closed 2026-03-11) is the canonical workflow example for this template, but it predates the parent-submission-user-story discipline formalized here. The ticket's parent epic is set to CTDC-1664 (correct), but there is no `Relates` link to the corresponding submission user story.

As a one-time cleanup when adopting this template, add a `Relates` link from CTDC-1907 to its corresponding submission user story. This brings the canonical example into conformance with the template so the next person modeling a registration ticket on CTDC-1907 sees the complete pattern. **This is a one-time retrofit, not a broader normalization sweep** — other registration tickets that predate this template stay as-is.

> *Note on identifying CTDC-1907's parent user story:* CTDC-1907 covers the **TCIA CMB radiology images submission** (closed 2026-03-11), which is distinct from the **NCTN-NCORP TCIA Images-Only submission** tracked by CTDC-1805 (which remains On Hold pending consent codes). Locate the correct parent user story for CTDC-1907 in the CTDC submission user story registry before linking — do not assume CTDC-1805 is the correct parent.
