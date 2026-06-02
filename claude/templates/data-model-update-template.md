### 7f. 🧬 Data Model Update Task Template (Drafted v9)

> **Use this template for every CTDC data model change to `CBIIT/ctdc-model` that is driven internally — by the CTDC project itself — rather than by an incoming study submission.** That spans the full weight range: a lightweight additive change (new optional properties or enum values requested by the CTDC application roadmap, e.g. Program/Portfolio curation fields) *and* a heavyweight infrastructure change (breaking MAJOR-version schema changes, model framework upgrades, cross-cutting multi-repo refactors). The distinguishing factor is the **driver**, not the weight. **Study-driven** changes — properties, enums, or permissible values requested by a study submission — use the **Data Modeling for Study Submission** template (Section 7g) instead. As of v9 the two modeling templates share one identical seven-section shape; they differ only in context (internal vs. study workbook, internal driver vs. study submission). Like the other data management templates, this one moves through Dev → QA → Stage → Prod, but the mechanism is the **Data Model Contribution** process — a git feature branch → PR into `develop` → PR `develop` → `prod` → tag and GitHub Release — **not** a data-loading pipeline. It is validated against schema state and the Data Model Navigator, and propagates downstream to the CRDC Data Hub via the release tag. The branch-and-release flow follows [`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md), which is authoritative; this ticket tracks its milestones in Branch & Release Signoff (Section 5), not the procedure itself.

**The governing rule: every model change has a CDE Request Workbook**

There is no "no workbook" path for a CTDC data model change. Every change — additive or breaking, study-driven or internal — records its changes and CDE assignments in a CDE Request Workbook. There are exactly two kinds of workbook, and the **driver** of the change selects which one:

- **Study-specific workbook** — owned by the Data Concierge handling that study. Used by study-driven changes (Section 7g).
- **Internal CTDC workbook** — the single persistent **CTDC Internal Data Modeling CDE Request Workbook**, which belongs to the **CTDC project itself with no individual owner**. Used by internally-driven changes (this template).

This rule is the reason the split between 7f and 7g is drawn on the driver: *who drives the change decides which workbook holds the term-level truth.* An internally-driven change records in the internal workbook even when it is a single optional property; a study-driven change records in that study's workbook even when it is small.

**The ticket is a milestone tracker, not a term inventory**

This template is deliberately lean. The CDE Request Workbook is the term-level source of truth — per-property CDE bindings, caDSR coordination status, permissible-value decisions, the full list of nodes and properties touched, and how many of each. The ticket does **not** duplicate any of that. It carries the workbook pointer, the SemVer/version milestones, the branch-and-release signoff, and the Jira links to related work. If a field would name a specific property, permissible value, enum value, relationship, or CDE code — or count how many are changing — it belongs in the workbook, not the ticket.

**Why this template**

The CTDC team's data management function has two sub-functions, and the modeling sub-function splits into two work patterns by driver:

- **Loading data** — taking a CRDC submission's *contents* (study metadata, files, IndexD entries) into CTDC's databases. Tracked with the Data Loading Task template (Section 7e) and, upstream, the IndexD Registration Task template (Section 7h).
- **Modeling data** — changing the *shape* of what CTDC's databases can hold. Two work patterns:
  - **Study-driven model changes** — requested by an incoming study submission, anchored on that study's CDE Request Workbook. Tracked with the Data Modeling for Study Submission template (Section 7g).
  - **Internally / CTDC-driven model changes** — initiated by the CTDC project itself (the application roadmap or the data team), anchored on the internal CDE Request Workbook. Tracked with **this** template, across the full additive-to-breaking weight range.

The boundary between 7g and 7f is *who's driving the change*. A study coming to CTDC with their workbook is 7g. An internal decision — to add curation properties for a new application page, to restructure how participants link to studies, or to upgrade the underlying Bento MDF framework — is 7f, and records in the internal workbook regardless of how light or heavy it is.

A model update covered by this template is a single end-to-end change that follows the Data Model Contribution SOP: confirm the CDE Request Workbook and any caDSR/ServiceNow coordination, create a feature branch off `develop`, edit the `model-desc/` YAML files together, update `version-history.md`, bump the YAML `Version:` field, commit and push, open a PR into `develop`, validate on Dev/QA, open a `develop` → `prod` PR, validate on Stage/Prod, verify the YAML `Version:` matches the planned tag, tag the release and publish GitHub Release notes, then verify the downstream sync in `crdc-datahub-models`. It is one piece of work in one ticket; related tickets are carried by native Jira issue links. A data-loading Jenkins pipeline is **not** part of this — that runs only if existing data must be re-loaded under the new schema, which is a separate Data Loading Task (Section 7e).

The canonical procedural reference for *how* model changes are designed, branched, committed, reviewed, tagged, and synced downstream is the SOP in the model repo itself: **[`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md)**. The CDE-coordination steps (the ServiceNow request to caDSR for new or updated CDEs/PVs, and the DM Federal Lead & Subject Matter Expertise Review with its DHDM Jira Issue) derive from the NCI Data Hub **"DM Updates" Kanban SOP**. This Jira template does **not** duplicate either SOP — it tracks the model change as a ticket and points at the SOPs for procedural detail. If anything in this template appears to conflict with a SOP, the SOP is authoritative. (Note: the DM Updates Kanban *lane lifecycle* — DH Coordinator hand-offs, Pending-Condition-Cleared, Active Submission, the validation loop — is submission-scoped and applies to 7g work, not to internally-driven 7f work, which has no submitter and no active submission. The caDSR/ServiceNow request and the DM Federal Lead & Subject Matter Expertise Review, however, are universal and are reflected in Section 3 below.)

The most common antipatterns we've seen on model update tickets:

1. **Treating an internal change as "no workbook."** Every model change records in a CDE Request Workbook. An internally-driven change records in the internal CTDC workbook — there is no path where a model change skips the workbook. (This was the original miss on the first pilot, CTDC-2068, which was filed asserting "no CDE Request Workbook" and "no caDSR CDE binding" before CDEs turned out to be required.)
2. **Routing by weight instead of driver.** The split between 7f and 7g is *who drives the change*, not how heavy it is. A study-driven change is 7g even if it is tiny; an internally-driven change is 7f even if it is a single optional property. Don't send a lightweight internal addition to 7g just because it "feels 7g-sized" — it has no study and no study workbook, so it belongs here, recorded in the internal workbook.
3. **Duplicating term-level detail into the ticket.** Listing the specific properties, permissible values, enum values, relationships, or caDSR CDE codes — or counting how many of each are changing — duplicates the workbook and drifts out of sync. Name the workbook (Section 2); keep the per-term inventory and the tallies there. The ticket records milestones, not terms or counts.
4. **One ticket per affected repo.** Splits a single model change across the `ctdc-model` ticket, the dataloader ticket, and the frontend ticket, with manual cross-linking. A model change is one end-to-end unit; the multi-repo touch is implementation detail. Track it within the one ticket and with native Jira issue links, not as separate tickets that lose the connection to the model change.
5. **Treating schema changes as software development.** Schema changes generate code work (loader updates, frontend renderers), but the *concern* is data shape — what nodes exist, what properties they carry, what relationships connect them. That is data management. The cascading code work is owned by the data management ticket, not split out as standalone software development tickets that lose the connection to the model change.
6. **YAML `Version:` field doesn't match the git tag.** The downstream Data Hub reads the version from `model-desc/ctdc_model_file.yaml`, not from the tag name. A mismatch silently fails to advance the displayed version downstream. The Section 4 Model Change Details table makes the target version explicit, with a note that the value goes into both the YAML field and the tag, so they can be verified together before tagging.
7. **Three required-together artifacts updated independently.** Adding a property to `ctdc_model_properties_file.yaml` without referencing it in the relevant node's `Props:` list in `ctdc_model_file.yaml` will pass validation but the property will not appear in the Data Model Navigator. Likewise, forgetting to update `version-history.md` leaves the Navigator's Version tab stale. Section 4's checklist enforces the trio.
8. **Restating the SOP procedure inside the ticket.** A step-by-step promotion procedure in the description duplicates `SOP_CTDC_Data_Model_Contribution.md`, which is authoritative, and drifts the moment the SOP changes. The ticket records *milestone completion* (Section 5, Branch & Release Signoff — a dated audit trail of when each milestone landed and who verified it) and *what to confirm before close* (Section 6, Verification Surfaces); it does not reproduce *how* to perform the steps. The dated milestone audit trail is not redundant with the ticket's Jira status — the status field shows only the current state, while the table preserves when each of the discrete git/release milestones completed.
9. **Missing SemVer classification.** "Update the model" doesn't communicate whether the change is MAJOR (breaking), MINOR (backward-compatible addition), or PATCH (bug fix). Downstream consumers need to know whether to plan a migration. Section 4 makes SemVer impact a required field.
10. **Skipping the DM Federal Lead & Subject Matter Expertise Review.** New or updated CDEs and permissible values must be requested from caDSR via a ServiceNow ticket, and a DHDM Jira Issue is created by the DH Coordinator and linked to the Submission Request ticket. Both live in Section 3 and gate `prod` promotion (tracked in Section 5) — they are not optional even for additive internal changes.

The template below resolves all ten with four commitments:

1. **Every change has a workbook, and a review gate.** Section 2 anchors the change to its CDE Request Workbook — the internal CTDC workbook for this template's internally-driven scope. Section 3 captures the DM Federal Lead & Subject Matter Expertise Review (the ServiceNow request and the DHDM Jira Issue). The workbook is the term-level source of truth; the ticket tracks milestones.
2. **One Task per end-to-end model update.** Single source of truth from initial schema edit through Prod validation and downstream verification. The ticket's Jira status reflects the current state; the Branch & Release Signoff table (Section 5) is the dated audit trail of each milestone as it lands.
3. **Branching follows the Contribution SOP.** Work happens on a feature branch off `develop`; `develop` is validated on Dev + QA and `prod` on Stage + Prod, with a `develop` → `prod` PR and a tag-and-release between them. This is the git contribution flow, not a data-loading pipeline — and the procedure lives in the SOP, not restated in the ticket.
4. **Explicit schema, explicit milestones.** Section 4 names the schema artifacts being changed (the three-files trio) and the SemVer impact. Section 5 (Branch & Release Signoff) is the dated milestone audit trail; Section 6 (Verification Surfaces) names the surfaces that must reflect the new version before close. Everything term-level stays in the workbook.

**Pipeline, branch, & store anatomy (read this once before drafting)**

A CTDC model update touches multiple systems in sequence. Knowing the chain helps every section land:

- **Source-of-truth repo** — **`CBIIT/ctdc-model`** holds the canonical model definition. Two branches matter: **`develop`** (pre-release; what Dev and QA serve) and **`prod`** (release; what Stage and Prod serve, and what the downstream Data Hub tracks via tag).
- **Schema artifacts** — three files in `ctdc-model` must update together for a property/node change to be complete:
  - **`model-desc/ctdc_model_file.yaml`** — node and relationship definitions. The `Version:` field at the top of this file is the canonical model version.
  - **`model-desc/ctdc_model_properties_file.yaml`** — property definitions (description, type, enum values, caDSR CDE binding, required vs preferred vs optional).
  - **`model-navigator-resources/version-history.md`** — what end users see in the Data Model Navigator's Version tab.
- **CDE source** — **caDSR**. New or updated CDEs and permissible values are requested via a **ServiceNow (SN)** ticket to the caDSR/SI team. Assigned CDE codes are recorded in the CDE Request Workbook; the model's properties file carries the caDSR binding (or a `TBD` placeholder until codes are minted).
- **Loader repo** — **`CBIIT/crdc-ctdc-dataloader`** (branch `master`). Python-based loader. Affected modules vary by change type: `data_loader.py`, `loader.py`, `file_loader.py`, `es_loader.py`, and `model-converter.py` (converts YAML schema to the GraphQL schema used by the frontend).
- **Frontend repos** — `CBIIT/crdc-ctdc-frontend` and related. Rendering changes when new fields need to be displayed.
- **Promotion mechanism** — **git + GitHub**, per the Data Model Contribution SOP. The model moves on feature branches and PRs (`develop` is validated on Dev/QA, `prod` on Stage/Prod) and propagates downstream via a git tag and GitHub Release. There is **no data-loading Jenkins pipeline** in model contribution; a Jenkins load runs only when existing data must be re-loaded under the new schema, tracked separately as a Data Loading Task (Section 7e).
- **Graph database** — **Memgraph**. The canonical metadata store. Model updates that add nodes or properties require a coordinated migration step before existing data can be reloaded under the new schema.
- **Search index** — **OpenSearch**. Reindex follows every load. If the schema change affects searchable fields, the index mapping may also need updating.
- **Downstream consumer** — **`CBIIT/crdc-datahub-models`**. After tagging a release on `ctdc-model`'s `prod` branch, GitHub Actions sync the new version into `crdc-datahub-models`. Verify by inspecting `cache/content.json` on the `prod` branch of that downstream repo.
- **Data Model Navigator** — the user-facing UI that displays the model. Reads from `version-history.md` and the YAML files; updates flow automatically once the new version is in place.

**Section order (7 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + **bold** title format shown. Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header — same rule as every other CTDC template.

1. `### 🎯 **Modeling Summary**` — Two to three sentences. What's changing in the model, why, and that the change is internally/CTDC-driven (this template's scope). Describe *what kind* of change it is, not *which terms* or *how many* (those live in the workbook); the assessed SemVer impact is recorded in Model Change Details (Section 4). A modeling change may be PATCH, MINOR, or MAJOR (breaking) — breaking is allowed, just uncommon. This ticket covers the model change only; any downstream re-load of existing data under the new schema is a separate Data Loading Task (Section 7e), not part of this work. Example: *"Add optional curation properties to the `program` node to support the new Portfolio and Program Details pages. This is an internally/CTDC-driven (feature-driven) change; the assessed SemVer impact is in Model Change Details, and the specific properties live in the CDE Request Workbook. This ticket covers the model change only — any downstream re-load of existing data is a separate Data Loading Task. The Data Model Navigator will reflect the new properties in its next version refresh."*

2. `### 📚 **CDE Request Workbook**` — Required section. **Every model change records its changes and CDE assignments in a CDE Request Workbook — there is no "no workbook" path.** Because this template covers internally/CTDC-driven changes, the workbook is the single persistent **CTDC Internal Data Modeling CDE Request Workbook**, which belongs to the **CTDC project itself (no individual owner)**. The workbook is the term-level source of truth (per-property CDE bindings, caDSR coordination status with the SI team, permissible-value decisions, and how many of each); this ticket tracks *when* milestones land, not *what* each term resolves to or *how many* there are.

   | Field | Value |
   |---|---|
   | Workbook | [CTDC-Internal-Data-Modeling_CDE_Request_Workbook.xlsx](https://nih.sharepoint.com/:x:/r/sites/NCI-CBIIT-FNL-CTDC-CTDCProjectManagement/Shared%20Documents/CTDC%20Data/CTDC%20Data%20Model/CTDC-Internal-Data-Modeling_CDE_Request_Workbook.xlsx?d=wab1b301e20ef474b849f13f3d6e283ef&csf=1&web=1&e=CIAPgf) |
   | Source-of-Truth Scope | Per-property CDE bindings, caDSR CDE codes, permissible-value decisions, nodes and properties touched |

3. `### 🔍 **DM Federal Lead & Subject Matter Expertise Review**` — Required section. New CDEs and new PVs require that a ticket is created by the DH Coordinator and linked to the corresponding Submission Request ticket. Per-term decisions and caDSR coordination detail are tracked in the ServiceNow ticket, not duplicated in this Jira ticket. This review gates promotion to `prod` (tracked in Section 5).

   | Coordination Item | Value | Owner |
   |---|---|---|
   | ServiceNow Ticket | *(link — or "Not required: no new or changed CDEs/PVs")* | DM Fed Lead |
   | DHDM Jira Issue | *(link — created by the DH Coordinator, linked to the Submission Request ticket)* | DH Lead |

4. `### 🧬 **Model Change Details**` — Required section. The milestone-level record of the change. Per-property, per-enum, and per-relationship detail — and the counts — live in the workbook (Section 2), not here.

   | Field | Value | Notes |
   |---|---|---|
   | SemVer impact | *(MAJOR / MINOR / PATCH)* | MAJOR = breaking schema change (removed required prop, type change, removed node, removed enum value, tighter constraint). MINOR = backward-compatible addition (new optional prop/node, added enum value, new optional relationship). PATCH = doc-only or bug fix. Breaking is allowed, just uncommon. |
   | Current model version | *(e.g., `v2.0.0`)* | From `model-desc/ctdc_model_file.yaml` `Version:` field on `prod` branch today. |
   | Target model version | *(e.g., `v2.1.0`)* | Must follow SemVer from the current version. This value goes into the YAML `Version:` field **and** the git tag — they must match exactly. The downstream Data Hub reads from the YAML, not the tag; a mismatch silently fails the sync. |
   | Backward compatibility | *(Yes / No / Partial — explain)* | If No or Partial, the downstream re-load of existing data under the new schema is a separate Data Loading Task (Section 7e), linked via native Jira issue links — not part of this ticket. |

   **Schema artifact checklist** (the three-files trio — all must update together):

   | Artifact | Updated? | Description of change |
   |---|---|---|
   | `model-desc/ctdc_model_file.yaml` | ☐ | Node definitions, relationships, `Props:` lists, and the top-level `Version:` field |
   | `model-desc/ctdc_model_properties_file.yaml` | ☐ | Property definitions (description, type, enum values, caDSR binding, required/preferred/optional) |
   | `model-navigator-resources/version-history.md` | ☐ | Release notes entry — what end users see in the Data Model Navigator's Version tab |

   Reference: the canonical procedure for editing, branching, tagging, and downstream-syncing model changes lives at **[`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md)** on the `prod` branch of `ctdc-model`. This ticket tracks the change; the SOP describes the procedure.

5. `### ✅ **Branch & Release Signoff**` — Milestone audit trail. Each row gets a date and reviewer initials as the milestone lands. This is a dated record of *when* each milestone completed and *who* verified it — information the ticket's single Jira status field can't carry. The procedure behind these milestones lives in the SOP; this table only records their completion.

   | Milestone | Completion Date | Reviewer Initials | Notes |
   |---|---|---|---|
   | Feature branch PR opened into `develop` (`ctdc-model`) | | | Link the PR in the notes column |
   | `develop` PR merged | | | |
   | `develop` → `prod` PR opened | | | Link the second PR |
   | `prod` PR merged | | | |
   | Git tag created (matches YAML `Version:` field) | | | Confirm character-for-character match before tagging |
   | GitHub Release published | | | |
   | **CTDC Data Model Navigator shows the new model version** | | | |
   | **Downstream `crdc-datahub-models` sync verified** | | | `cache/content.json` on `prod` reflects the new version |

6. `### 🧪 **Verification Surfaces**` — The downstream surfaces that must reflect the new model version before this ticket closes. Both update on the same trigger — the `prod` merge and release tag.

   - **CTDC Data Model Navigator (DMN)** — confirms the model change appears at the new version.
   - **Downstream Data Hub sync (`crdc-datahub-models`)** — confirms `cache/content.json` on the `prod` branch reflects the new version. Prod validation plus this downstream verification together are the close trigger.

7. `### 📝 **Notes**` — Bullet list. Optional content: links to design discussions or model-owner meetings, prior model-change retrospectives, terminology translations (Bento "Case" → CTDC "Participant"), and any open item that needs to travel with the ticket (open items are otherwise tracked as Jira comments, not as a description section). If there's no meaningful note, write "None at this time."

**Standing emoji set (7 entries — identical to the study-submission modeling template)**

| Section | Emoji |
|---|---|
| Modeling Summary | 🎯 |
| CDE Request Workbook | 📚 |
| DM Federal Lead & Subject Matter Expertise Review | 🔍 *(the caDSR/PV governance review gate)* |
| Model Change Details | 🧬 |
| Branch & Release Signoff | ✅ |
| Verification Surfaces | 🧪 |
| Notes | 📝 |

**Required content rules (Data Model Update Task specific — universal Jira rules in 7b-shared also apply)**

- **Scope is internally / CTDC-driven model changes, across the full weight range.** Lightweight additive changes *and* heavyweight infrastructure changes. The driver — the CTDC project itself, not an incoming study — is what selects this template. **Study-driven** model changes use the **Data Modeling for Study Submission** template (Section 7g). Loading new study data uses the Data Loading Task template (Section 7e). Software development that does not touch the schema uses the software development template family.
- **Every change records in a CDE Request Workbook (Section 2).** For this template that is the internal CTDC workbook, owned by the project (no individual owner). There is no "no workbook" path. The workbook is the term-level source of truth; the ticket does not duplicate per-term inventory.
- **No specifics, ever.** The ticket is a milestone tracker, not a term inventory. No per-property, per-enum, per-permissible-value, per-relationship, or per-CDE-code listing in the ticket — and no counts — that all lives in the workbook. Name the node/scope and the *kind* of change; nothing more granular. Model Change Details carries SemVer, versions, and backward compatibility only.
- **A modeling change may be any SemVer level.** PATCH, MINOR, or MAJOR (breaking) — breaking is allowed, just uncommon. Classify the impact in Model Change Details (Section 4). The downstream re-load of existing data that a breaking change may require is a **separate Data Loading Task (Section 7e)**, linked via native Jira issue links, never folded into the modeling ticket.
- **caDSR / ServiceNow and the DM Federal Lead & Subject Matter Expertise Review are not optional.** New or updated CDEs/PVs go through a ServiceNow request to caDSR (Section 3). A DHDM Jira Issue is created by the DH Coordinator and linked to the Submission Request ticket (Section 3), gating `prod` promotion (tracked in Section 5). This holds even for additive internal changes.
- **No Acceptance Criteria section.** Model updates are operational SOP work; the completion bar is the ticket reaching Closed once the Branch & Release Signoff milestones (Section 5) and Verification Surfaces (Section 6) are complete. AC belongs on user stories that consume the new schema, not on the model change itself.
- **Don't restate the SOP procedure.** Branch & Release Signoff (Section 5) records milestone *completion*; the SOP describes *how* to perform each step. A step-by-step promotion procedure in the description duplicates the SOP and drifts when it changes.
- **One Task per end-to-end model update** — develop branch through Prod, not one ticket per repo. Native Jira issue links carry the multi-repo and cross-ticket relationships.
- **Issue type is Task** on this tracker. Do not use Story or Subtask.
- **Parent Epic field set on the ticket itself** via `customfield_12350` when a release-level epic exists.
- **YAML `Version:` field and git tag must match exactly.** Surface this in Section 4's Target model version row, with the note that the value goes into both the YAML field and the tag. A mismatch silently fails the downstream sync.
- **Three schema artifacts updated together.** The checklist in Section 4 enforces the trio: `ctdc_model_file.yaml`, `ctdc_model_properties_file.yaml`, `version-history.md`.
- **SemVer impact is a required field.** MAJOR / MINOR / PATCH must be classified at ticket creation per the SOP's rules.
- **The model SOP is authoritative for procedure.** This template tracks the change; the SOP at `https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md` describes how to do it, and the DM Updates Kanban SOP is authoritative for the caDSR/ServiceNow request and the DM Federal Lead & Subject Matter Expertise Review steps.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as every other Jira description on this tracker.

**Writing-and-publishing workflow**

1. Confirm the work is internally/CTDC-driven (this template), not study-driven (Section 7g) and not a pure data load (Section 7e). If it is study-driven — requested by an incoming study submission with that study's CDE Request Workbook — use 7g instead.
2. Confirm the internal CDE Request Workbook is recording this change (Section 2). If new/updated CDEs or PVs are involved, line up the ServiceNow request to caDSR and capture the link in Section 3.
3. Confirm the SemVer classification per the SOP — MAJOR / MINOR / PATCH. Additive optional properties/enums are MINOR; breaking changes are MAJOR; doc-only is PATCH. All are valid under this template when internally driven.
4. Verify the target YAML `Version:` and git tag will be identical. Record the target version in Section 4's Target model version row.
5. Create the data model update task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields`. Assign to the TPM (Gina Kuffel) initially — the TPM coordinates the multi-repo work and reassigns per-environment as work progresses.
6. Push the description in two steps: create with the placeholder, then `jira_update_issue` with the full Markdown body. `jira_create_issue` renders inconsistently on long Markdown; `jira_update_issue` performs clean Markdown-to-Jira-wiki conversion. **Do not hand-edit the description in the Jira UI afterward** — the wiki editor mangles monospace tokens (property names with underscores), URLs (eats underscores), and Markdown bullets; re-push through `jira_update_issue` instead.
7. Add native Jira issue links (Relates / Blocks) to the parent epic, any downstream Data Loading Task, and consuming feature tickets.
8. Verify the rendered description with a UI screenshot — wiki source is unreliable as a render preview.
9. As milestones land, the assignee fills in the Branch & Release Signoff rows (Section 5) with dates and initials and advances the ticket's Jira status. The DM Federal Lead & Subject Matter Expertise Review is recorded in Section 3 (the ServiceNow ticket and the DHDM Jira Issue) as those artifacts are created.
10. **Prod validation plus downstream-sync verification together are the close trigger.** Once Prod is validated *and* `crdc-datahub-models` `cache/content.json` on `prod` shows the new version (Section 6), transition the ticket to Closed.

**When to expand vs trim**

- **MINOR additive change, internally driven** (new optional property, new optional node, added enum value — e.g., CTDC-2068's Program curation properties) → use this template as-is; it's already trimmed for this case. Verification (Section 6) is light. Section 2 (internal workbook) and the Section 3 caDSR/ServiceNow + DM Federal Lead & SME Review steps still apply if any CDE/PV is added or changed.
- **PATCH change** (description fix, examples, metadata text) → use the template; expect Verification (Section 6) to focus on the Navigator's display, and usually no caDSR/SN request.
- **MAJOR change** (removed required property, type change, removed node, removed enum, tightened constraint) → the Modeling Summary and the Backward compatibility row (Section 4) must state that existing data is unaffected until re-loaded; expect a separate Data Loading Task to follow with re-load of affected studies, linked via native Jira issue links.
- **Multi-property batched change** → one ticket per coherent change-set that ships as a single version bump. If changes are independent and could ship separately, use one ticket each.
- **Pure documentation update to `ctdc-model`** (README, owner guide, SOP edits) → this template is overkill. A free-form Task with the doc change description and a single PR link is enough.

**When NOT to use this template**

This template covers internally/CTDC-driven model changes across the full weight range. The following are different:

- **Study-driven model changes** — requested by an incoming study submission, anchored on that study's CDE Request Workbook. Use the **Data Modeling for Study Submission** template (Section 7g). The driver is the study, and the term-level truth lives in the study's workbook (owned by that study's Data Concierge), not the internal workbook.
- **Loading data into the existing schema** — new studies, new data added to existing studies, megazip loads. Use the **Data Loading Task** template (Section 7e). This is also where a breaking change's downstream re-load is tracked.
- **Software development that does not change the schema** — frontend features, backend refactors, microservice work, bug fixes, library upgrades. Use the software development family.
- **CRDC platform changes** — Fence, IndexD, Submission Portal upgrades owned by CRDC platform teams. Out of CTDC scope.
- **Bento Core MDF framework changes** — changes to the underlying MDF tooling itself (`bento-mdf` submodule). Upstream concern; file with the Bento team.

If a CRDC submission requires schema changes before it can be loaded, that's two pieces of data management work: a modeling ticket and a Data Loading Task, depending on each other but in separate tickets. If the change is requested by the study submission, the model work is a 7g ticket (study workbook). If it is initiated by the CTDC project itself, the model work is a 7f ticket (internal workbook) regardless of whether it is additive or breaking.

**Changelog**

- **v9 (2026-06-02)** — Converged 7f and 7g onto one identical seven-section shape; they now differ only in context (internal vs. study workbook, internal driver vs. study submission). (1) Renamed **Model Change Summary → Modeling Summary**. (2) **Dropped the Promotion Workflow section** — its step-by-step procedure duplicated `SOP_CTDC_Data_Model_Contribution.md`, which is authoritative; the ticket no longer restates it. (3) Added **✅ Branch & Release Signoff** (the dated milestone audit trail) and **🧪 Verification Surfaces** (the surfaces to confirm before close), both adopted from 7g — reversing v7's "no signoff table" stance, because a dated per-milestone audit trail carries information the single Jira status field can't, which is why 7g always kept it. (4) Reframed the Modeling Summary and Model Change Details guidance: a modeling change may be PATCH/MINOR/MAJOR (breaking allowed, uncommon), and any downstream re-load of existing data is a separate Data Loading Task, never part of the modeling ticket. (5) Reworked antipattern 8 from "no signoff table" to "don't restate the SOP procedure," updated commitments 2 and 4 accordingly, and sharpened the no-specifics rule to explicitly cover permissible values. Now **7 sections**.
- **v8 (2026-06-02)** — Brought the template into lockstep with the canonical pilot CTDC-2068. (1) **No counts:** removed the count ("four") from the example, added an explicit "No counts" rule, and extended antipattern 3. (2) Promoted the caDSR/ServiceNow coordination out of the CDE Request Workbook section into its own section, **🔍 DM Federal Lead & Subject Matter Expertise Review** (then Section 3), and replaced the **DC formal sign-off** concept with the **DHDM Jira Issue** created by the DH Coordinator and linked to the Submission Request ticket (owners: DM Fed Lead, DH Lead). (3) Dropped the **Out-of-Scope for Ticket** row from the workbook table. Renumbered to 6 sections and repointed the Promotion Workflow governance gate at Section 3.
- **v7 (2026-06-02)** — Removed the **Testing Signoff** section in favor of native Jira status. (Reversed in v9, which adopts the Branch & Release Signoff milestone audit trail from 7g.)
- **v6 (2026-06-02)** — Corrected the **Promotion Workflow** to the git contribution flow (feature branch → PR into `develop` → PR `develop` → `prod` → tag and GitHub Release) per the SOP, removing leaked data-loading-pipeline framing. (Promotion Workflow section later dropped entirely in v9.)
- **v5 (2026-06-01)** — Removed the **Linked Work** section; related tickets are carried by native Jira issue links.
- **v4 (2026-06-01)** — Slimmed to the lean shape settled on the canonical pilot CTDC-2068; moved term-level detail to the CDE Request Workbook.
- **v3 (2026-06-01, superseded)** — Reframed the 7f/7g split on the driver (internally/CTDC-driven vs. study-driven) rather than weight, and made the CDE Request Workbook universal (no "no workbook" path).
- **v2 (2026-05-20, superseded)** — Scoped to infrastructure-level changes only; routed additive work to 7g and had no CDE Request Workbook concept.
