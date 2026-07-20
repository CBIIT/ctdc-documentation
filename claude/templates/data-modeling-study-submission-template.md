### 7f. 📚 Data Modeling for Study Submission Template (Drafted v9)

> **Use this template for every CTDC data modeling task driven by a study submission** — whether the study is brand-new to CTDC or an existing study requesting new properties, permissible values, or enums. Canonical example: **[CTDC-2051](https://tracker.nci.nih.gov/browse/CTDC-2051)** (NCI-MATCH Arm Z1D IHC data modeling). This template covers the **modeling-data** sub-function of data management when the work is anchored on a study's **CDE Request Workbook**. It is **not** for infrastructure-level or internally-driven model changes — those use the Data Model Update Task template (Section 7j). It is **not** for loading the study's data after the model is in place — that's a Data Loading Task (Section 7h). As of v7 this template and 7j share one identical five-section shape; they differ only in context (study workbook vs. internal workbook, study submission vs. internal driver).

**Why this template**

CTDC modeling work driven by study submissions is straightforward in shape: a study comes to us, we agree on terms via a CDE Request Workbook, we add them to `ctdc-model`, we tag and release, the Submission Portal picks up the new model, the study can submit. The ticket exists to track *when* milestones land. The workbook exists to track *what* is being modeled.

The hard rule: **the workbook is the term-level source of truth, not the ticket.** Pasting term inventories, per-term status, per-term decisions, or term counts into the ticket creates a stale snapshot that the team has to maintain in two places. The sections below are enough — anything more drifts toward duplicating workbook content or reproducing context that already lives on the parent user story.

**Three commitments**

1. **One Task per study's modeling work.** Single source of truth for the modeling concern from initial workbook review through Submission Portal verification. One study, one ticket, one workbook. Future additions to the same study get their own ticket.
2. **Parent user story carries study identity.** Modeling ticket links to the parent user story (e.g., CTDC-2051 → CTDC-1666). Study identity, submission chronology, document references, and study description live on the user story; this ticket holds only the modeling concern.
3. **CDE Request Workbook is the term-level source of truth.** The ticket references it in a structurally explicit section; the workbook owns the inventory and status of every term. The ticket does not duplicate term inventories, per-term decisions, or counts.

**Section order (5 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + **bold** title format shown. Don't omit, reorder, or merge sections. Don't add sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header.

1. `### 🎯 **Modeling Summary**` — Two to three sentences. Which study is driving this modeling work, whether it's a new study coming to CTDC or an addition to an existing study, and what the modeling work enables. Describe *what kind* of change it is, not *which terms* or *how many* (those live in the workbook). **Include a pointer to the parent user story** for full study identity, chronology, and submission context. Example: *"Update the CTDC data model to support the **NCI-MATCH Arm Z1D IHC Data Submission**. This is a new submission and study being onboarded to CTDC (full study identity, chronology, and submission context in **CTDC-1666**)."*

2. `### 📚 **CDE Request Workbook**` — Required section. The CDE Request Workbook is the **system of record** for this modeling effort. The ticket tracks *when* milestones land; the workbook tracks *what* is being modeled. Per-term decisions, term status, and caDSR/SI coordination live in the workbook — not in this ticket.

   This section anchors the ticket to the workbook and declares the scope boundary between ticket and workbook. **Do not** include study identity here — that lives on the parent user story.

   | Field | Value |
   |---|---|
   | Workbook | *(SharePoint URL)* |
   | Workbook Owner | *(@username — CTDC Data Concierge or TPM)* |
   | Source-of-Truth Scope | *(e.g., "Term inventory, per-term status, CDE bindings, nodes and properties touched, per-term decisions")* |

   The fields are intentionally lean. The Workbook URL points readers to the source of truth; the Workbook Owner names the responsible party; the Source-of-Truth Scope row is the boundary statement that keeps workbook content out of the ticket over time. Term counts, specific terms, and per-term decisions are not in this table — those belong in the workbook, the parent user story, or (for milestone state) Sections 4–5 below.

3. `### 🔍 **DM Federal Lead & Subject Matter Expertise Review**` — Required on **every** model change, study-driven or internal. The **Communication Channel** (row 1) records the Microsoft Teams channel or chat where the Data Concierge coordinates the hand-off with the DH Lead. The **DHDM Jira Issue is filed first** (row 2) on the DHDM Jira board and tracks the review. The **Data Concierge files the caDSR II Help Desk Request Form** (row 3) in caDSR's Help Desk system (a ServiceNow-based ticketing system) when new or updated CDEs/PVs are needed, adding the DM Federal Lead (Heather Creasy) as a **watcher on the caDSR ticket** — not duplicated as a watcher on this Jira ticket; its link goes in row 3 once it exists. Per-term decisions and caDSR coordination detail live in the caDSR Help Desk ticket and the workbook, not in this Jira ticket. This review gates promotion to `prod`. For study-driven work, the study's **Submission Request ticket is a native Jira "Relates" link on this ticket — not a row in the table below.** The DH DataModel Updates Kanban workflow that governs this review — for **every** model change — is the [05 SOP for DataModel Updates documentation in JIRA](https://nih.sharepoint.com/:w:/r/sites/NCI-CBIIT-CRDC-DataHubSubmissions/Shared%20Documents/Data%20Hub%20Submissions/06%20SOP%20Library%20For%20Data%20Submissions/05%20SOP%20for%20DataModel%20Updates%20documentation%20in%20JIRA%20012626.docx?d=wa0dfdc8bc4384e638d4b98eebc56c719&csf=1&web=1&e=HwRTEA), administered by the DM Fed Lead.

   | Coordination Item | Value | Owner |
   |---|---|---|
   | Communication Channel | *(link to the applicable Microsoft Teams channel or chat for this submission)* | Data Concierge |
   | DHDM Jira Issue | *(link to the DHDM Jira Issue — filed first on the DHDM Jira board)* | DH Coordinator / DH Lead |
   | caDSR II Help Desk Request Form | *(link — added once the Data Concierge files it in caDSR's Help Desk)* | Data Concierge |

4. `### 🪜 **Steps to Completion**` — Numbered list. The path **this ticket** follows from open to closed. The *how* of each model edit lives in [`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md); the DM Fed Lead review runs on the DH DataModel Updates Kanban under its own SOP (Section 3). This ticket tracks **our** work to done — not that board's lane movements, which are the DM Fed Lead's to manage. Keep the steps to the workflow spine; never enumerate the specific terms being modeled (those live in the workbook).

   1. The Data Concierge updates the CDE Request Workbook with all CDEs being requested, any values needing validation, and any additional permissible values being requested.
   2. File a caDSR II Help Desk Request Form using [this link](https://service.cancer.gov/cadsr-curation?id=cadsr_helpdesk_request&sys_id=a03d31151b8d05106daea681f54bcbd0).
      - Add the Federal Lead, Heather Creasy, as a watcher on the caDSR ticket. Do not duplicate Heather's watcher status on the corresponding CTDC Jira ticket when a caDSR ticket was filed.
      - In the **Short Description** box, match the corresponding Jira ticket title (e.g., "Submission Data Modeling: NCI-MATCH Arm Z1D IHC") to ensure a 1:1 mapping.
      - In the **Details** box, include which Data Commons the request is from, the project/submission, a brief description of the requested work (no property-level detail), and a link to the appropriate CDE workbook.
      - Paste the caDSR II Help Desk Request Form/ticket link in the Value column of the Section 3 table.
   3. Once approval is established from the caDSR II Help Desk Request Form/ticket, the Semantics Infrastructure (SI) team updates the respective columns (Resolution, Resolution Date, CDE Code, CDE Version, Notes) in the linked CDE Workbook. Use the designated Teams channel to coordinate with the SI team.
   4. Complete the data model updates in the `ctdc-model` GitHub repository per the [Data Model Contribution SOP](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md).
   5. Verify the new model version is present on all Verification Surfaces (Section 5).
   6. Paste links to all pull requests in the comments of this Jira ticket.
   7. Update the Status column in the CDE Workbook.
   8. Move this Jira ticket to Ready for QA testing.
   9. Do **not** close this ticket until the related submission is complete in the CRDC Submission Portal — a single study submission can require many data model changes over its course.

5. `### 🧪 **Verification Surfaces**` — Bullet list, stated as confirmation actions. The Data Model Navigator runs as two separate instances — one in the CRDC Submission Portal (submitters), one in CTDC (researchers) — both reading the same model. Both reflect a model version bump on the same trigger (the `prod` merge and release tag); confirm the new version on both.

   - **CRDC Submission Portal** — Confirm submitters can see the new version of the data model in the Data Model Navigator.
   - **CTDC** — Confirm researchers can see the new version of the data model in the Data Model Navigator.

**Standing emoji set (6 entries — identical to the Data Model Update Task template)**

| Section | Emoji |
|---|---|
| Modeling Summary | 🎯 |
| CDE Request Workbook | 📚 |
| DM Federal Lead & Subject Matter Expertise Review | 🔍 *(the caDSR/PV governance review gate)* |
| Steps to Completion | 🪜 |
| Verification Surfaces | 🧪 |

**Required content rules**

- **Five sections, no further additions.** Open Questions & Risks, Linked Work, Term Status Summary, Notes, and Modeling & Promotion Workflow are deliberately *not* sections in this template. Open items and risks are not task-level — track them as Jira comments so the audit trail lives with the work. Linked Work belongs in Jira's native issue-link UI. Term Status Summary duplicates workbook content. Notes (operational context, cadence, sibling-ticket pointers) belongs in Jira comments or on the parent user story, not on the task. Modeling & Promotion Workflow duplicates the SOP.
- **Scope is study-driven model changes only.** A study submission's CDE Request Workbook drives the work. Infrastructure-level or internally-driven model changes use the Data Model Update Task template (Section 7j). Data loading uses the Data Loading Task template (Section 7h).
- **No specifics, ever.** No per-property, per-enum, per-permissible-value, per-relationship, or per-CDE-code listing in the ticket — and no counts. Describe the kind of change ("permissible-value and CDE-mapping changes"), never the specific terms or the tally. It all lives in the CDE Workbook.
- **Versioning and procedure live in the SOP and GitHub, not the ticket.** How the model is edited, which SemVer level applies, and how it is tagged and released are governed by the Data Model Contribution SOP and recorded in GitHub (the YAML `Version:` field, the git tag, the GitHub Release). The ticket does not classify or track the version. Any downstream re-load of existing data that a breaking change may require is a **separate Data Loading Task (Section 7h)**, linked via native Jira issue links, never folded into the modeling ticket.
- **One Task per study's modeling work.** New study onboarding gets one ticket. Subsequent additions for the same study get their own ticket. Don't roll forward into a single open ticket.
- **A parent user story is required.** Every modeling ticket links to a parent user story that carries study identity. If no parent user story exists, file it before this modeling ticket. Identity content lives on the user story — never duplicated on the modeling ticket.
- **The DM Federal Lead & Subject Matter Expertise Review is required on every model change.** The DHDM Jira Issue is filed first on the DHDM board and tracks the review (Section 3, row 2); the Data Concierge files the caDSR II Help Desk Request Form (caDSR's ServiceNow-based Help Desk) when new/updated CDEs or PVs are needed, adding the DM Federal Lead as a watcher on the caDSR ticket, and its link is added to row 3 once it exists. This review gates `develop` → `prod` promotion. The study's Submission Request ticket is a native Jira "Relates" link, never a row in the Section 3 table.
- **Issue type is Task** on this tracker, matching CTDC-2051's convention.
- **Title convention:** `Data Modeling: <Study Name vN>` — reuse the exact `<Study Name vN>` token from the parent Data Submission user story title (Section 7e) verbatim; no acronym or version drift. Internal/non-study model updates (7j) instead use `Data Modeling: <change description>`.
- **Parent Epic field set on the ticket itself** via `customfield_12350` when a study onboarding or release epic exists.
- **The CDE Request Workbook is the term-level source of truth, not this ticket.** Section 2 anchors the URL with an explicit Source-of-Truth Scope row. Do not replicate the workbook's term inventory or per-term status anywhere on the ticket.
- **Verification on both Data Model Navigator instances is the close trigger.** Not `prod` merge, not the GitHub Release — the actual confirmation that both the Submission Portal's and CTDC's Data Model Navigator show the new model version is what closes the ticket.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as every other Jira description on this tracker.

**Writing-and-publishing workflow**

1. Confirm the parent user story exists. If it doesn't, file it first using the User Story template — the modeling ticket assumes it as the context anchor.
2. Confirm a CDE Request Workbook exists or is in active authoring. If it doesn't, the modeling ticket is premature — author the workbook first.
3. Confirm this work is study-driven (anchored on a CDE Request Workbook), not infrastructure-level or internally-driven (use Section 7j) and not loading (use Section 7h).
4. The SemVer level, version bump, tag, and release are governed by the Data Model Contribution SOP and recorded in GitHub — not classified or tracked on this ticket.
5. Create the data modeling task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields`. Assign per the working-meeting cadence.
6. Push the description in two steps: create with the placeholder, then `jira_update_issue` with the full Markdown body. Same two-step pattern as every other CTDC template. **Do not hand-edit the description in the Jira UI afterward** — the wiki editor mangles monospace tokens, URLs (eats underscores), and Markdown bullets; re-push through `jira_update_issue` instead.
7. Add native "Relates" issue links to the parent user story (required), the study's Submission Request ticket (study-driven), the DHDM Jira Issue, and any sibling modeling tickets for the same study or program.
8. Verify the rendered description with a UI screenshot — wiki source is unreliable as a render preview.
9. As work progresses, the data engineer adds GitHub PR links as Jira comments and advances the ticket through its Jira states — those states carry the branch/release progress a description table used to hold. Section 3 records the DHDM Jira Issue (row 2) first, then the caDSR II Help Desk Request Form (row 3) once it exists; operational context that develops along the way goes in Jira comments, not the ticket body.
10. **Verification on both Data Model Navigator instances plus tag-and-release together are the close trigger.** Once both the Submission Portal's and CTDC's Data Model Navigator show the new model version (Section 5, Verification Surfaces) and the workbook's Status column is fully reconciled, move the ticket to its Closed state.

**When to expand vs trim**

- **New study onboarding with a large workbook** → use the template as written. The sections handle any scale because counts and per-term state aren't on the ticket; the workbook absorbs scale.
- **Existing study, small addition** → use the template as written. Same shape.
- **Workbook still in early authoring** → file the ticket as a draft; Section 2 fields can be PLACEHOLDER until the workbook stabilizes.
- **No new or changed CDEs/PVs** (e.g., a pure mapping or structural tweak agreed with the study) → the DHDM review (Section 3, row 2) still happens; the caDSR II Help Desk Request Form row stays empty until/unless a caDSR request is filed.
- **A single property addition driven internally by the data team, not a study** → this template is the wrong fit. Use the Data Model Update Task (Section 7j).

**When NOT to use this template**

- **Infrastructure-level or internally-driven model changes** (breaking, framework upgrades, multi-repo refactors, or roadmap-driven additions initiated by the CTDC project itself) → Data Model Update Task (7j).
- **Loading study data after the model lands** → Data Loading Task (7h). Different verification surface, different ladder. This is also where a breaking change's downstream re-load is tracked.
- **Software development that does not touch the schema** → software development template family.
- **CDE Workbook authoring as standalone work** — the workbook is a SharePoint artifact. Workbook authoring is data-adjacent operational work, not its own structured ticket.
- **Bento Core MDF framework changes** — upstream concern; file with the Bento team.

**Changelog**

> *Numbering note: the template IDs in the Changelog entries below predate the 2026-06-16 process-order renumber of the data-management templates. The full old -> new mapping is recorded in [`README.md`](./README.md).*

- **v9 (2026-07-20)** — Re-synced the template to the finalized CTDC-2051 ticket, which drifted ahead of v8. Two substantive changes, no section-shape change (still 5 sections). (1) **Section 3, row 3 renamed and re-owned:** "ServiceNow Ticket" (owner: DM Fed Lead) → "**caDSR II Help Desk Request Form**" (owner: **Data Concierge**). The Data Concierge — not the DM Fed Lead — files the caDSR Help Desk Request Form (caDSR's Help Desk runs on ServiceNow, so it is the same underlying system, renamed to how the team refers to it) and adds the DM Federal Lead (Heather Creasy) as a **watcher on the caDSR ticket**, not on the Jira ticket. Prose in Section 3, the Required-content-rules DM-review bullet, the "No new or changed CDEs/PVs" expand/trim bullet, and workflow step 9 updated to match. (2) **Steps to Completion expanded** from the 4-step spine to the fuller nine-step workflow the canonical ticket now carries: workbook update → file caDSR Help Desk Request Form (with the 1:1 Short Description title rule, Details-box contents, and Fed-Lead-as-watcher note) → SI team curates the workbook columns on approval → model updates per the Data Model Contribution SOP → verify Verification Surfaces → paste PR links in comments → update workbook Status → move to Ready for QA → do **not** close until the submission is complete in the CRDC Submission Portal. The "no specifics / no term enumeration" rule still holds — the expanded steps describe the workflow, never the terms. Sibling template 7j re-synced in lockstep to its own canonical example, CTDC-2068.
- **v8 (2026-06-24)** — Added a **Communication Channel** coordination item as the first row of the DM Federal Lead & SME Review table (the Microsoft Teams channel or chat where the Data Concierge coordinates the DH Lead hand-off; owner: Data Concierge), and reworded **Steps to Completion** step 2 to "Contact the DH Lead **using the elected Communication Channel (listed in the table above)**" — matching the finalized CTDC-2051 ticket. Renumbered the DHDM Jira Issue (now row 2) and ServiceNow ticket (now row 3) references throughout the prose. No section-shape change — the 5-section shape is unchanged.
- **v7 (2026-06-04)** — Trimmed to a **5-section** shape and aligned the example to the finalized CTDC-2051 / CTDC-2068 tickets. (1) **Removed the Notes section** — operational context, cadence, and sibling-ticket pointers belong in Jira comments or on the parent user story, not on a task. (2) Slimmed the **Modeling Summary** to a 1-2 sentence headline and dropped the SOP-elevation paragraph. (3) Rewrote **Steps to Completion** as four actionable steps, dropping the explicit Close-this-ticket spine step and all ticket-facing Section-N cross-references. (4) Removed the **Out-of-Scope for Ticket** row from the CDE Request Workbook table. (5) Restated **Verification Surfaces** as confirmation actions and captured the two-instance Data Model Navigator architecture (one DMN in the CRDC Submission Portal for submitters, one in CTDC for researchers, both reading the same model). Author-facing section pointers retained. Sections are now Modeling Summary - CDE Request Workbook - DM Federal Lead & SME Review - Steps to Completion - Verification Surfaces.
- **v6 (2026-06-04)** — Trimmed and reshaped to a **6-section** shape. (1) Removed **🧬 Model Change Details** and **✅ Branch & Release Signoff**: SemVer/versioning is governed by the Data Model Contribution SOP and tracked in GitHub (YAML `Version:`, git tag, GitHub Release), and the branch/release milestones are carried by the ticket's Jira states rather than a description table. (2) Dropped the schema-artifact checklist (it lived inside Model Change Details) — the SOP enforces the three-files trio. (3) Trimmed the Modeling Summary's "covers the model change only / separate Data Loading Task / next version refresh" sentences and elevated the SOP as the authority for how to edit and version the model. (4) Made the **DM Federal Lead & SME Review universal** (every change, no "Not required" escape); reordered Section 3 so the **DHDM Jira Issue is row 1 (filed first)** and the **ServiceNow ticket is row 2 (filed by the DM Fed Lead, linked once it exists)**; the study's **Submission Request ticket** is now a native Jira "Relates" link, never a table row. (5) Added a **🪜 Steps to Completion** section — a high-level spine pointing to the two governing SOPs (the DM Updates JIRA SOP and the CTDC Data Model Contribution SOP), not a procedure restatement — and linked the **DM Updates JIRA SOP** in the DM Federal Lead & SME Review section. Sections are now Modeling Summary · CDE Request Workbook · DM Federal Lead & SME Review · Steps to Completion · Verification Surfaces · Notes.
- **v5 (2026-06-02)** — Converged 7g with 7f onto one identical seven-section shape; they now differ only in context (study vs. internal workbook, study submission vs. internal driver). (1) Added **🧬 Model Change Details** (new Section 4) so study-driven changes record their assessed **SemVer impact**, current/target version, and backward compatibility — plus the three-files schema-artifact checklist — which the template previously had nowhere to capture. (2) **Dropped Open Questions & Risks** (was Section 6) — open items and risks are not task-level and belong in Jira comments. (3) Reframed the Modeling Summary guidance and example: the old "no new properties or nodes, no breaking changes" framing wrongly implied study modeling is inherently non-breaking; a change may be any SemVer level, and any downstream re-load is a separate Data Loading Task. (4) Generalized the verification-surface wording from "new permissible values" to "the new model version / model content" (a modeling change isn't always a PV change), replaced "No counts" with the broader **No specifics** rule, and renumbered (Branch & Release Signoff → 5, Verification Surfaces → 6, Notes → 7). Now **7 sections**, identical to 7f v9. Canonical examples CTDC-2051 (retrofitted to this shape) and CTDC-1799 (pending retrofit).
- **v4 (2026-06-02)** — Added the **🔍 DM Federal Lead & Subject Matter Expertise Review** section, bringing 7g into lockstep with 7f and the canonical pilot CTDC-2068. The same caDSR/PV governance applies to study-driven modeling — arguably more directly, since study work always has a Submission Request. Moved Open Questions & Risks to the ⚠️ emoji (later dropped in v5); added an explicit no-counts rule.
- **v3 (2026-05-20)** — Reduced to the canonical 6-section shape per Gina's correction; v2 had bloated to 10 sections. v3 restored the lean intent: the workbook is the source of truth for term-level state, and the ticket exists for milestone tracking only. CTDC-2051 and CTDC-1799 were both in v3 shape as of 2026-05-20.
- **v2 (2026-05-20, superseded)** — Bloated to 10 sections; not approved. Do not use.
- **v1 (2026-05-20, superseded)** — Initial draft modeled on CTDC-1799 pre-cleanup. Section 3 combined study identity with workbook anchor; superseded when the parent-user-story-carries-identity pattern was established.
