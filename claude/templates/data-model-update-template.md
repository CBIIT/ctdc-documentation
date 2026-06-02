### 7f. 🧬 Data Model Update Task Template (Drafted v6)

> **Use this template for every CTDC data model change to `CBIIT/ctdc-model` that is driven internally — by the CTDC project itself — rather than by an incoming study submission.** That spans the full weight range: a lightweight additive change (new optional properties or enum values requested by the CTDC application roadmap, e.g. Program/Portfolio curation fields) *and* a heavyweight infrastructure change (breaking MAJOR-version schema changes, model framework upgrades, cross-cutting multi-repo refactors). The distinguishing factor is the **driver**, not the weight. **Study-driven** changes — properties, enums, or permissible values requested by a study submission — use the **Data Modeling for Study Submission** template (Section 7g) instead. Like the other data management templates, this one moves through Dev → QA → Stage → Prod, but the mechanism is the **Data Model Contribution** process — a git feature branch → PR into `develop` → PR `develop` → `prod` → tag and GitHub Release — **not** a data-loading pipeline. It is validated against schema state and the Data Model Navigator, and propagates downstream to the CRDC Data Hub via the release tag. The Promotion Workflow (Section 4) follows [`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md), which is authoritative.

**The governing rule: every model change has a CDE Request Workbook**

There is no "no workbook" path for a CTDC data model change. Every change — additive or breaking, study-driven or internal — records its changes and CDE assignments in a CDE Request Workbook. There are exactly two kinds of workbook, and the **driver** of the change selects which one:

- **Study-specific workbook** — owned by the Data Concierge handling that study. Used by study-driven changes (Section 7g).
- **Internal CTDC workbook** — the single persistent **CTDC Internal Data Modeling CDE Request Workbook**, which belongs to the **CTDC project itself with no individual owner**. Used by internally-driven changes (this template).

This rule is the reason the split between 7f and 7g is drawn on the driver: *who drives the change decides which workbook holds the term-level truth.* An internally-driven change records in the internal workbook even when it is a single optional property; a study-driven change records in that study's workbook even when it is small.

**The ticket is a milestone tracker, not a term inventory**

This template is deliberately lean. The CDE Request Workbook is the term-level source of truth — per-property CDE bindings, caDSR coordination status, permissible-value decisions, the full list of nodes and properties touched. The ticket does **not** duplicate any of that. It carries the workbook pointer, the SemVer/version milestones, the promotion-and-signoff record, and the Jira links to related work. If a field would name a specific property, enum value, relationship, or CDE code, it belongs in the workbook, not the ticket.

**Why this template**

The CTDC team's data management function has two sub-functions, and the modeling sub-function splits into two work patterns by driver:

- **Loading data** — taking a CRDC submission's *contents* (study metadata, files, IndexD entries) into CTDC's databases. Tracked with the Data Loading Task template (Section 7e) and, upstream, the IndexD Registration Task template (Section 7h).
- **Modeling data** — changing the *shape* of what CTDC's databases can hold. Two work patterns:
  - **Study-driven model changes** — requested by an incoming study submission, anchored on that study's CDE Request Workbook. Tracked with the Data Modeling for Study Submission template (Section 7g).
  - **Internally / CTDC-driven model changes** — initiated by the CTDC project itself (the application roadmap or the data team), anchored on the internal CDE Request Workbook. Tracked with **this** template, across the full additive-to-breaking weight range.

The boundary between 7g and 7f is *who's driving the change*. A study coming to CTDC with their workbook is 7g. An internal decision — to add curation properties for a new application page, to restructure how participants link to studies, or to upgrade the underlying Bento MDF framework — is 7f, and records in the internal workbook regardless of how light or heavy it is.

A model update covered by this template is a single end-to-end change that follows the Data Model Contribution SOP: confirm the CDE Request Workbook and any caDSR/ServiceNow coordination, create a feature branch off `develop`, edit the `model-desc/` YAML files together, update `version-history.md`, bump the YAML `Version:` field, commit and push, open a PR into `develop`, validate on Dev/QA, open a `develop` → `prod` PR, validate on Stage/Prod, verify the YAML `Version:` matches the planned tag, tag the release and publish GitHub Release notes, then verify the downstream sync in `crdc-datahub-models`. It is one piece of work in one ticket; related tickets are carried by native Jira issue links. A data-loading Jenkins pipeline is **not** part of this — that runs only if existing data must be re-loaded under the new schema, which is a separate Data Loading Task (Section 7e).

The canonical procedural reference for *how* model changes are designed, branched, committed, reviewed, tagged, and synced downstream is the SOP in the model repo itself: **[`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md)**. The CDE-coordination steps (the ServiceNow request to caDSR for new or updated CDEs/PVs, and the formal Data Commons sign-off gate) derive from the NCI Data Hub **"DM Updates" Kanban SOP**. This Jira template does **not** duplicate either SOP — it tracks the model change as a ticket and points at the SOPs for procedural detail. If anything in this template appears to conflict with a SOP, the SOP is authoritative. (Note: the DM Updates Kanban *lane lifecycle* — DH Coordinator hand-offs, Pending-Condition-Cleared, Active Submission, the validation loop — is submission-scoped and applies to 7g work, not to internally-driven 7f work, which has no submitter and no active submission. The caDSR/ServiceNow and DC-sign-off mechanics, however, are universal and are reflected below.)

The most common antipatterns we've seen on model update tickets:

1. **Treating an internal change as "no workbook."** Every model change records in a CDE Request Workbook. An internally-driven change records in the internal CTDC workbook — there is no path where a model change skips the workbook. (This was the original miss on the first pilot, CTDC-2068, which was filed asserting "no CDE Request Workbook" and "no caDSR CDE binding" before CDEs turned out to be required.)
2. **Routing by weight instead of driver.** The split between 7f and 7g is *who drives the change*, not how heavy it is. A study-driven change is 7g even if it is tiny; an internally-driven change is 7f even if it is a single optional property. Don't send a lightweight internal addition to 7g just because it "feels 7g-sized" — it has no study and no study workbook, so it belongs here, recorded in the internal workbook.
3. **Duplicating term-level detail into the ticket.** Listing the specific properties, enum values, relationships, or caDSR CDE codes in the ticket duplicates the workbook and drifts out of sync. Name the workbook (Section 2); keep the per-term inventory there. The ticket records milestones, not terms.
4. **One ticket per affected repo.** Splits a single model change across the `ctdc-model` ticket, the dataloader ticket, and the frontend ticket, with manual cross-linking. A model change is one end-to-end unit; the multi-repo touch is implementation detail. Track it within the one ticket and with native Jira issue links, not as separate tickets that lose the connection to the model change.
5. **Treating schema changes as software development.** Schema changes generate code work (loader updates, frontend renderers), but the *concern* is data shape — what nodes exist, what properties they carry, what relationships connect them. That is data management. The cascading code work is owned by the data management ticket, not split out as standalone software development tickets that lose the connection to the model change.
6. **YAML `Version:` field doesn't match the git tag.** The downstream Data Hub reads the version from `model-desc/ctdc_model_file.yaml`, not from the tag name. A mismatch silently fails to advance the displayed version downstream. The Section 3 Model Change Details table makes the target version explicit, with a note that the value goes into both the YAML field and the tag, so they can be verified together before tagging.
7. **Three required-together artifacts updated independently.** Adding a property to `ctdc_model_properties_file.yaml` without referencing it in the relevant node's `Props:` list in `ctdc_model_file.yaml` will pass validation but the property will not appear in the Data Model Navigator. Likewise, forgetting to update `version-history.md` leaves the Navigator's Version tab stale. Section 3's checklist enforces the trio.
8. **No per-environment signoff record.** When QA, Stage, and Prod testers initial their work in Slack threads or comments, there's no consolidated record. The Testing Signoff table in Section 5 is the single source of truth, and the per-environment verification detail lives inline in the Promotion Workflow steps (Section 4).
9. **Missing SemVer classification.** "Update the model" doesn't communicate whether the change is MAJOR (breaking), MINOR (backward-compatible addition), or PATCH (bug fix). Downstream consumers need to know whether to plan a migration. Section 3 makes SemVer impact a required field.
10. **Skipping the caDSR / ServiceNow request or the DC sign-off.** New or updated CDEs and permissible values must be requested from caDSR via a ServiceNow ticket, and DC model changes require a formal Data Commons sign-off before promotion to `prod`. Both live in Section 2 and gate the Promotion Workflow in Section 4 — they are not optional even for additive internal changes.

The template below resolves all ten with four commitments:

1. **Every change has a workbook.** Section 2 anchors the change to its CDE Request Workbook — the internal CTDC workbook for this template's internally-driven scope — and captures the caDSR/ServiceNow coordination and DC sign-off. The workbook is the term-level source of truth; the ticket tracks milestones.
2. **One Task per end-to-end model update.** Single source of truth from initial schema edit through Prod signoff and downstream verification. The Testing Signoff section is the consolidated completion record.
3. **Branching follows the Contribution SOP.** Work happens on a feature branch off `develop`; `develop` is validated on Dev + QA and `prod` on Stage + Prod, with a `develop` → `prod` PR and a tag-and-release between them. This is the git contribution flow, not a data-loading pipeline.
4. **Explicit schema, explicit milestones.** Section 3 names the schema artifacts being changed (the three-files trio) and the SemVer impact. Per-environment verification is folded into the Promotion Workflow (Section 4) and signed off in Testing Signoff (Section 5). Everything term-level stays in the workbook.

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

**Section order (6 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header — same rule as every other CTDC template.

1. `### 🎯 **Model Change Summary**` — Two to three sentences. What's changing in the model, why, the SemVer impact in plain English, and that the change is internally/CTDC-driven (this template's scope). For a breaking (MAJOR) change, also state in one line what existing data does under the new schema. Example: *"Add four optional curation properties to the `program` node to support the new Portfolio and Program Details pages. This is an internally/CTDC-driven, additive MINOR change — new optional properties — so existing data remains valid under the new schema and no re-load is required for correctness. The Data Model Navigator will reflect the new properties in its next version refresh."*

2. `### 📚 **CDE Request Workbook**` — Required section. **Every model change records its changes and CDE assignments in a CDE Request Workbook — there is no "no workbook" path.** Because this template covers internally/CTDC-driven changes, the workbook is the single persistent **CTDC Internal Data Modeling CDE Request Workbook**, which belongs to the **CTDC project itself (no individual owner)**. The workbook is the term-level source of truth (per-property CDE bindings, caDSR coordination status with the SI team, permissible-value decisions); this ticket tracks *when* milestones land, not *what* each term resolves to.

   | Field | Value |
   |---|---|
   | Workbook | [CTDC-Internal-Data-Modeling_CDE_Request_Workbook.xlsx](https://nih.sharepoint.com/:x:/r/sites/NCI-CBIIT-FNL-CTDC-CTDCProjectManagement/Shared%20Documents/CTDC%20Data/CTDC%20Data%20Model/CTDC-Internal-Data-Modeling_CDE_Request_Workbook.xlsx?d=wab1b301e20ef474b849f13f3d6e283ef&csf=1&web=1&e=CIAPgf) |
   | Source-of-Truth Scope | Per-property and per-enum CDE bindings, caDSR CDE codes (or `TBD`), permissible-value decisions, nodes/properties touched |
   | Out-of-Scope for Ticket | Per-term decisions and caDSR coordination detail — tracked in the workbook, not duplicated here |

   **caDSR / ServiceNow coordination.** New or updated CDEs and permissible values are requested from caDSR via a **ServiceNow (SN) request**. Assigned codes are recorded in the workbook. **Formal Data Commons (DC) sign-off** is required for DC model changes and gates promotion to `prod` (see Section 4).

   | caDSR / coordination item | Value |
   |---|---|
   | ServiceNow request ID(s) | *(link to the SN request — or "Not yet submitted" / "Not required: no new or changed CDEs")* |
   | DC formal sign-off | *(Approver and date — required before `develop` → `prod`; recorded here and in Section 5)* |

3. `### 🧬 **Model Change Details**` — Required field. The milestone-level record of the change. Per-property, per-enum, and per-relationship detail lives in the workbook (Section 2), not here.

   | Field | Value | Notes |
   |---|---|---|
   | SemVer impact | *(MAJOR / MINOR / PATCH)* | MAJOR = breaking schema change (removed required prop, type change, removed node, removed enum value, tighter constraint). MINOR = backward-compatible addition (new optional prop/node, added enum value, new optional relationship). PATCH = doc-only or bug fix. |
   | Current model version | *(e.g., `v2.0.0`)* | From `model-desc/ctdc_model_file.yaml` `Version:` field on `prod` branch today. |
   | Target model version | *(e.g., `v2.1.0`)* | Must follow SemVer from the current version. This value goes into the YAML `Version:` field **and** the git tag — they must match exactly. The downstream Data Hub reads from the YAML, not the tag; a mismatch silently fails the sync. |
   | Backward compatibility | *(Yes / No / Partial — explain)* | If No or Partial, describe what breaks and what existing data needs to do. Drives whether existing studies need re-loading and whether a Data Loading Task follows. |

   **Schema artifact checklist** (the three-files trio — all must update together):

   | Artifact | Updated? | Description of change |
   |---|---|---|
   | `model-desc/ctdc_model_file.yaml` | ☐ | Node definitions, relationships, `Props:` lists, and the top-level `Version:` field |
   | `model-desc/ctdc_model_properties_file.yaml` | ☐ | Property definitions (description, type, enum values, caDSR binding, required/preferred/optional) |
   | `model-navigator-resources/version-history.md` | ☐ | Release notes entry — what end users see in the Data Model Navigator's Version tab |

   Reference: the canonical procedure for editing, branching, tagging, and downstream-syncing model changes lives at **[`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md)** on the `prod` branch of `ctdc-model`. This ticket tracks the change; the SOP describes the procedure.

4. `### 🚦 **Promotion Workflow**` — The end-to-end steps follow the **Data Model Contribution SOP** ([`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md)), which is authoritative. The model promotes by git feature branch → PR into `develop` → PR `develop` → `prod` → tag and GitHub Release, with downstream propagation handled by GitHub Actions. **There is no data-loading Jenkins pipeline in this workflow** — a load runs only if existing data must be re-loaded under the new schema, which is a separate Data Loading Task (Section 7e).

   **Pre-change**
   1. Confirm this is an internally/CTDC-driven change (this template) and that it is recorded in the internal CDE Request Workbook (Section 2). If new or updated CDEs/PVs are involved, submit/track the ServiceNow request to caDSR and record the SN link in Section 2.
   2. Classify the SemVer impact in Section 3 (MAJOR / MINOR / PATCH) per the SOP.

   **`develop` branch — Dev + QA**
   3. Clone `ctdc-model`, `git checkout develop`, `git pull`. Create a feature branch named for the ticket: `git checkout -b CTDC-XXXX`.
   4. Make the changes locally, updating the three artifacts together (Section 3 checklist): `ctdc_model_properties_file.yaml` (the property definition), `ctdc_model_file.yaml` (the node's `Props:` list and/or a new node and its relationships), and `version-history.md`. Bump the `Version:` field in `ctdc_model_file.yaml` — this is what drives downstream Data Hub detection.
   5. Stage and commit with an imperative message and ticket reference (`git commit -m "CTDC-XXXX: <summary>"`), then push the branch. Ensure CI / integration tests pass; resolve any errors before moving on.
   6. Open a PR into `develop`. Title `CTDC-XXXX: <brief description>`; description covers what changed and why, the SemVer impact, validation performed, and migration notes if breaking. Assign a GitHub reviewer, assign the Jira ticket to the reviewer, and copy the PR link into the Jira comments.
   7. After approval, merge into `develop` and validate on the Dev and QA environments — confirm the schema change is present and the Data Model Navigator reflects the new version. Record in Section 5.

   **`develop` → `prod` — Stage + Prod, tag, release**
   8. Governance gate (per the DM Updates SOP): confirm the formal DC sign-off in Section 2 is recorded before promoting, and that any requested caDSR codes are recorded in the workbook (or that remaining `TBD` placeholders are an accepted, documented risk).
   9. ⚠️ Verify the `Version:` field in `ctdc_model_file.yaml` exactly matches the SemVer tag you are about to create. The Data Hub reads the version from the YAML, not the tag name — a mismatch silently fails to advance the displayed version downstream.
   10. Open a PR from `develop` → `prod`; repeat the review and CI process. After merge, validate on the Stage and Prod environments. Record in Section 5.
   11. Tag and release (required for production propagation): create a git tag matching the SemVer version (e.g., `v2.1.0`) and create/update the GitHub Release notes for that version.
   12. Downstream verification in `CBIIT/crdc-datahub-models`: GitHub Actions sync the CTDC model pointer; verify `cache/content.json` on the `prod` branch reflects the new version. Prod validation plus this downstream verification together are the trigger to close the ticket.

5. `### ✅ **Testing Signoff**` — The consolidated completion record. The tester fills in date and initials per environment as work progresses. The DC sign-off row records the formal Data Commons approval gating `prod` promotion. **Prod signoff is the trigger to transition the ticket to Closed.**

   | Milestone / Environment | Completion Date | Initials |
   |---|---|---|
   | DC formal sign-off (gates `prod`) | | |
   | Dev | | |
   | QA | | |
   | Stage | | |
   | Prod | | |

6. `### 📝 **Notes**` — Bullet list. Optional content: links to design discussions or model-owner meetings, prior model-change retrospectives, terminology translations (Bento "Case" → CTDC "Participant"), and any open item that needs to travel with the ticket (open items are otherwise tracked as Jira comments, not as a description section). If there's no meaningful note, write "None at this time."

**Standing emoji set (6 entries)**

| Section | Emoji |
|---|---|
| Model Change Summary | 🎯 |
| CDE Request Workbook | 📚 *(shared with study-submission modeling template — same source-of-truth role)* |
| Model Change Details | 🧬 *(unique to data model update task)* |
| Promotion Workflow | 🚦 *(shared with data loading task — same operational shape)* |
| Testing Signoff | ✅ |
| Notes | 📝 |

**Required content rules (Data Model Update Task specific — universal Jira rules in 7b-shared also apply)**

- **Scope is internally / CTDC-driven model changes, across the full weight range.** Lightweight additive changes *and* heavyweight infrastructure changes. The driver — the CTDC project itself, not an incoming study — is what selects this template. **Study-driven** model changes use the **Data Modeling for Study Submission** template (Section 7g). Loading new study data uses the Data Loading Task template (Section 7e). Software development that does not touch the schema uses the software development template family.
- **Every change records in a CDE Request Workbook (Section 2).** For this template that is the internal CTDC workbook, owned by the project (no individual owner). There is no "no workbook" path. The workbook is the term-level source of truth; the ticket does not duplicate per-term inventory.
- **The ticket is a milestone tracker, not a term inventory.** No per-property, per-enum, per-relationship, or per-CDE-code listing in the ticket — that lives in the workbook. Model Change Details carries SemVer, versions, and backward compatibility only.
- **caDSR / ServiceNow and DC sign-off are not optional.** New or updated CDEs/PVs go through a ServiceNow request to caDSR (record the SN link in Section 2). DC model changes require a formal DC sign-off recorded in Section 2 and Section 5, gating `prod` promotion (Section 4). This holds even for additive internal changes.
- **No Acceptance Criteria section.** Model updates are operational SOP work; the completion bar is the Testing Signoff table (Section 5) plus the verification steps embedded in the Promotion Workflow (Section 4). AC belongs on user stories that consume the new schema, not on the model change itself.
- **One Task per end-to-end model update** — develop branch through Prod, not one ticket per repo. The Testing Signoff table (Section 5) is the consolidated source of truth for where the change is in the pipeline; native Jira issue links carry the multi-repo and cross-ticket relationships.
- **Issue type is Task** on this tracker. Do not use Story or Subtask.
- **Parent Epic field set on the ticket itself** via `customfield_12350` when a release-level epic exists.
- **YAML `Version:` field and git tag must match exactly.** Surface this in Section 3's Target model version row, with the note that the value goes into both the YAML field and the tag. A mismatch silently fails the downstream sync.
- **Three schema artifacts updated together.** The checklist in Section 3 enforces the trio: `ctdc_model_file.yaml`, `ctdc_model_properties_file.yaml`, `version-history.md`.
- **SemVer impact is a required field.** MAJOR / MINOR / PATCH must be classified at ticket creation per the SOP's rules.
- **The model SOP is authoritative for procedure.** This template tracks the change; the SOP at `https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md` describes how to do it, and the DM Updates Kanban SOP is authoritative for the caDSR/ServiceNow and DC-sign-off steps.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as every other Jira description on this tracker.

**Writing-and-publishing workflow**

1. Confirm the work is internally/CTDC-driven (this template), not study-driven (Section 7g) and not a pure data load (Section 7e). If it is study-driven — requested by an incoming study submission with that study's CDE Request Workbook — use 7g instead.
2. Confirm the internal CDE Request Workbook is recording this change (Section 2). If new/updated CDEs or PVs are involved, line up the ServiceNow request to caDSR and capture the SN link.
3. Confirm the SemVer classification per the SOP — MAJOR / MINOR / PATCH. Additive optional properties/enums are MINOR; breaking changes are MAJOR; doc-only is PATCH. All are valid under this template when internally driven.
4. Verify the target YAML `Version:` and git tag will be identical. Record the target version in Section 3's Target model version row.
5. Create the data model update task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields`. Assign to the TPM (Gina Kuffel) initially — the TPM coordinates the multi-repo work and reassigns per-environment as work progresses.
6. Push the description in two steps: create with the placeholder, then `jira_update_issue` with the full Markdown body. `jira_create_issue` renders inconsistently on long Markdown; `jira_update_issue` performs clean Markdown-to-Jira-wiki conversion. **Do not hand-edit the description in the Jira UI afterward** — the wiki editor mangles monospace tokens (property names with underscores) and Markdown bullets; re-push through `jira_update_issue` instead.
7. Add native Jira issue links (Relates / Blocks) to the parent epic, any downstream Data Loading Task, and consuming feature tickets.
8. Verify the rendered description with a UI screenshot — wiki source is unreliable as a render preview.
9. As each environment completes, the assigned tester adds their initials and date to Section 5 (Testing Signoff). The DC sign-off row in Section 5 is filled when the formal DC approval lands.
10. **Prod signoff plus downstream-sync verification together are the close trigger.** Once Section 5's Prod row is filled in *and* `crdc-datahub-models` `cache/content.json` on `prod` shows the new version, transition the ticket to Closed.

**When to expand vs trim**

- **MINOR additive change, internally driven** (new optional property, new optional node, added enum value — e.g., CTDC-2068's four Program curation properties) → use this template as-is; it's already trimmed for this case. Verification in the Promotion Workflow is light. Section 2 (internal workbook) and the caDSR/ServiceNow + DC-sign-off steps still apply if any CDE/PV is added or changed.
- **PATCH change** (description fix, examples, metadata text) → use the template; expect the Promotion Workflow verification to focus on the Navigator's display, and usually no caDSR/SN request.
- **MAJOR change** (removed required property, type change, removed node, removed enum, tightened constraint) → the Model Change Summary and the Backward compatibility row (Section 3) must spell out what existing data does under the new schema; expect a Data Loading Task to follow with re-load of affected studies, linked via native Jira issue links.
- **Multi-property batched change** → one ticket per coherent change-set that ships as a single version bump. If changes are independent and could ship separately, use one ticket each.
- **Pure documentation update to `ctdc-model`** (README, owner guide, SOP edits) → this template is overkill. A free-form Task with the doc change description and a single PR link is enough.

**When NOT to use this template**

This template covers internally/CTDC-driven model changes across the full weight range. The following are different:

- **Study-driven model changes** — requested by an incoming study submission, anchored on that study's CDE Request Workbook. Use the **Data Modeling for Study Submission** template (Section 7g). The driver is the study, and the term-level truth lives in the study's workbook (owned by that study's Data Concierge), not the internal workbook.
- **Loading data into the existing schema** — new studies, new data added to existing studies, megazip loads. Use the **Data Loading Task** template (Section 7e).
- **Software development that does not change the schema** — frontend features, backend refactors, microservice work, bug fixes, library upgrades. Use the software development family.
- **CRDC platform changes** — Fence, IndexD, Submission Portal upgrades owned by CRDC platform teams. Out of CTDC scope.
- **Bento Core MDF framework changes** — changes to the underlying MDF tooling itself (`bento-mdf` submodule). Upstream concern; file with the Bento team.

If a CRDC submission requires schema changes before it can be loaded, that's two pieces of data management work: a modeling ticket and a Data Loading Task, depending on each other but in separate tickets. If the change is requested by the study submission, the model work is a 7g ticket (study workbook). If it is initiated by the CTDC project itself, the model work is a 7f ticket (internal workbook) regardless of whether it is additive or breaking.

**Changelog**

- **v6 (2026-06-02)** — Corrected the **Promotion Workflow** (Section 4): it had described the data-*loading* promotion ladder (Jenkins lower/upper-tier pipelines, per-environment data loads). Model contribution does not run a loading pipeline — it is a git feature-branch → PR into `develop` → PR `develop` → `prod` → tag and GitHub Release flow, with downstream propagation via GitHub Actions, exactly per [`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md). Also corrected the loading-pipeline framing that had leaked into the intro blockquote, the "why this template" paragraph, commitment 3, and the pipeline-anatomy bullet. A Jenkins data load runs only if existing data must be re-loaded under the new schema, which is a separate Data Loading Task (7e).
- **v5 (2026-06-01)** — Removed the **Linked Work** section; related tickets are carried by native Jira issue links (Relates / Blocks) and don't need a mirrored description section. Now **6 sections**.
- **v4 (2026-06-01)** — Slimmed to **7 sections** to match the structure settled on the canonical pilot CTDC-2068. Removed the **Affected Code & Loaders**, **Verification Surfaces**, **Per-Environment Verification**, **Collaboration & Handoffs**, and **Open Questions / Risks** sections — the multi-repo touch is carried by Jira issue links and the Promotion Workflow; per-environment verification is folded into the Promotion Workflow steps and signed off in Testing Signoff; open items are tracked as Jira comments. Trimmed **Model Change Details** to SemVer impact, current/target model version, and backward compatibility (plus the three-files schema-artifact checklist); removed the per-property / per-enum / per-relationship / node-type rows and the property-definitions block (term-level detail lives in the CDE Request Workbook). Removed the **Workbook Owner** row from the workbook table (ownership stated in the section prose) and the separate **caDSR CDE status** row. Kept **Linked Work** and the workbook's **Source-of-Truth / Out-of-Scope** rows. Added a note against hand-editing the description in the Jira UI (it mangles monospace and bullets).
- **v3 (2026-06-01, superseded)** — Reframed the 7f/7g split on the driver (internally/CTDC-driven vs. study-driven) rather than weight, and made the CDE Request Workbook universal (no "no workbook" path; internal CTDC workbook for 7f). Added the CDE Request Workbook section, caDSR/ServiceNow coordination, and the DC sign-off gate; 12 sections. Superseded the same day by v4's slimmed 7-section shape.
- **v2 (2026-05-20, superseded)** — Scoped to infrastructure-level changes only; routed additive work to 7g and had no CDE Request Workbook concept.
