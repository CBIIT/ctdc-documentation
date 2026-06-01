### 7f. 🧬 Data Model Update Task Template (Drafted v3)

> **Use this template for every CTDC data model change to `CBIIT/ctdc-model` that is driven internally — by the CTDC project itself — rather than by an incoming study submission.** That spans the full weight range: a lightweight additive change (new optional properties or enum values requested by the CTDC application roadmap, e.g. Program/Portfolio curation fields) *and* a heavyweight infrastructure change (breaking MAJOR-version schema changes, model framework upgrades, cross-cutting multi-repo refactors). The distinguishing factor is the **driver**, not the weight. **Study-driven** changes — properties, enums, or permissible values requested by a study submission — use the **Data Modeling for Study Submission** template (Section 7g) instead. Like the other data management templates, this one promotes through Dev → QA → Stage → Prod and is verified against application *behavior* (loader runs, page renders, regression suite) and schema state, not just the model repo.

**The governing rule: every model change has a CDE Request Workbook**

There is no "no workbook" path for a CTDC data model change. Every change — additive or breaking, study-driven or internal — records its changes and CDE assignments in a CDE Request Workbook. There are exactly two kinds of workbook, and the **driver** of the change selects which one:

- **Study-specific workbook** — owned by the Data Concierge handling that study. Used by study-driven changes (Section 7g).
- **Internal CTDC workbook** — the single persistent **CTDC Internal Data Modeling CDE Request Workbook**, which belongs to the **CTDC project itself with no individual owner**. Used by internally-driven changes (this template).

This rule is the reason the split between 7f and 7g is drawn on the driver: *who drives the change decides which workbook holds the term-level truth.* An internally-driven change records in the internal workbook even when it is a single optional property; a study-driven change records in that study's workbook even when it is small.

**Why this template**

The CTDC team's data management function has two sub-functions, and the modeling sub-function splits into two work patterns by driver:

- **Loading data** — taking a CRDC submission's *contents* (study metadata, files, IndexD entries) into CTDC's databases. Tracked with the Data Loading Task template (Section 7e) and, upstream, the IndexD Registration Task template (Section 7h).
- **Modeling data** — changing the *shape* of what CTDC's databases can hold. Two work patterns by driver:
  - **Study-driven model changes** — requested by an incoming study submission, anchored on that study's CDE Request Workbook. Tracked with the Data Modeling for Study Submission template (Section 7g).
  - **Internally / CTDC-driven model changes** — initiated by the CTDC project itself (the application roadmap or the data team), anchored on the internal CDE Request Workbook. Tracked with **this** template, across the full additive-to-breaking weight range.

The boundary between 7g and 7f is *who's driving the change*. A study coming to CTDC with their workbook is 7g. An internal decision — to add curation properties for a new application page, to restructure how participants link to studies, or to upgrade the underlying Bento MDF framework — is 7f, and records in the internal workbook regardless of how light or heavy it is.

A model update covered by this template is a single end-to-end change: confirm the CDE Request Workbook and any caDSR/ServiceNow coordination, edit `model-desc/` YAML files, update `version-history.md`, bump the YAML `Version:` field, update any dataloader / frontend code that consumes the new schema, run the lower-tiers Jenkins pipeline against Dev/QA, promote `develop` → `prod` in `ctdc-model`, tag the release, verify downstream sync, run upper-tiers pipeline against Stage/Prod. The work spans repos and pipelines, but it is one piece of work and lives in one ticket.

The canonical procedural reference for *how* model changes are designed, branched, committed, reviewed, tagged, and synced downstream is the SOP in the model repo itself: **[`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md)**. The CDE-coordination steps (the ServiceNow request to caDSR for new or updated CDEs/PVs, and the formal Data Commons sign-off gate) derive from the NCI Data Hub **"DM Updates" Kanban SOP**. This Jira template does **not** duplicate either SOP — it tracks the model change as a ticket and points at the SOPs for procedural detail. If anything in this template appears to conflict with a SOP, the SOP is authoritative. (Note: the DM Updates Kanban *lane lifecycle* — DH Coordinator hand-offs, Pending-Condition-Cleared, Active Submission, the validation loop — is submission-scoped and applies to 7g work, not to internally-driven 7f work, which has no submitter and no active submission. The caDSR/ServiceNow and DC-sign-off mechanics, however, are universal and are reflected below.)

The most common antipatterns we've seen on model update tickets:

1. **Treating an internal change as "no workbook."** Every model change records in a CDE Request Workbook. An internally-driven change records in the internal CTDC workbook — there is no path where a model change skips the workbook. (This was the original miss on the first pilot, CTDC-2068, which was filed asserting "no CDE Request Workbook" and "no caDSR CDE binding" before CDEs turned out to be required.)
2. **Routing by weight instead of driver.** The split between 7f and 7g is *who drives the change*, not how heavy it is. A study-driven change is 7g even if it is tiny; an internally-driven change is 7f even if it is a single optional property. Don't send a lightweight internal addition to 7g just because it "feels 7g-sized" — it has no study and no study workbook, so it belongs here, recorded in the internal workbook.
3. **One ticket per affected repo.** Splits a single model change across the `ctdc-model` ticket, the `crdc-ctdc-dataloader` ticket, and the frontend ticket, with manual cross-linking. A model change is one end-to-end unit; the multi-repo touch is implementation detail, not separate work. Use the Affected Code & Loaders table in Section 5 to track multi-repo updates within one ticket.
4. **Treating schema changes as software development.** Schema changes generate code work (loader updates, frontend renderers), but the *concern* is data shape — what nodes exist, what properties they carry, what relationships connect them. That is data management. The cascading code work is owned by the data management ticket, not split out as standalone software development tickets that lose the connection to the model change.
5. **YAML `Version:` field doesn't match the git tag.** The downstream Data Hub reads the version from `model-desc/ctdc_model_file.yaml`, not from the tag name. A mismatch silently fails to advance the displayed version downstream. The Section 4 Model Change Details table makes both values explicit so they can be verified together before tagging.
6. **Three required-together artifacts updated independently.** Adding a property to `ctdc_model_properties_file.yaml` without referencing it in the relevant node's `Props:` list in `ctdc_model_file.yaml` will pass validation but the property will not appear in the Data Model Navigator. Likewise, forgetting to update `version-history.md` leaves the Navigator's Version tab stale. Section 4's checklist enforces the trio.
7. **No per-environment signoff record.** When QA, Stage, and Prod testers initial their work in Slack threads or comments, there's no consolidated record. The Testing Signoff table in Section 10 is the single source of truth.
8. **Missing SemVer classification.** "Update the model" doesn't communicate whether the change is MAJOR (breaking), MINOR (backward-compatible addition), or PATCH (bug fix). Downstream consumers need to know whether to plan a migration. Section 4 makes SemVer impact a required field.
9. **Skipping the caDSR / ServiceNow request or the DC sign-off.** New or updated CDEs and permissible values must be requested from caDSR via a ServiceNow ticket, and DC model changes require a formal Data Commons sign-off before promotion to `prod`. Both live in Section 2 and gate the Promotion Workflow in Section 6 — they are not optional even for additive internal changes.

The template below resolves all nine with four commitments:

1. **Every change has a workbook.** Section 2 anchors the change to its CDE Request Workbook — the internal CTDC workbook for this template's internally-driven scope — and captures the caDSR/ServiceNow coordination and DC sign-off. The workbook is the term-level source of truth; the ticket tracks milestones.
2. **One Task per end-to-end model update.** Single source of truth from initial schema edit through Prod signoff and downstream verification. The Testing Signoff section justifies the one-ticket approach.
3. **Branching mirrors Jenkins tiering.** The `ctdc-model` repo's `develop` branch maps to Dev + QA (lower tiers); the `prod` branch maps to Stage + Prod (upper tiers). The promotion workflow is the same two-tier ladder as Data Loading, plus the tag-and-release step between tiers.
4. **Explicit schema, explicit code, explicit downstream.** Section 4 names the schema artifacts being changed (YAML files, version-history entry, SemVer impact). Section 5 names the code and downstream artifacts being touched. Verification confirms both the schema landed *and* the downstream consumers picked it up.

**Pipeline, branch, & store anatomy (read this once before drafting)**

A CTDC model update touches multiple systems in sequence. Knowing the chain helps every section of the template land:

- **Source-of-truth repo** — **`CBIIT/ctdc-model`** holds the canonical model definition. Two branches matter: **`develop`** (pre-release; what Dev and QA serve) and **`prod`** (release; what Stage and Prod serve, and what the downstream Data Hub tracks via tag).
- **Schema artifacts** — three files in `ctdc-model` must update together for a property/node change to be complete:
  - **`model-desc/ctdc_model_file.yaml`** — node and relationship definitions. The `Version:` field at the top of this file is the canonical model version.
  - **`model-desc/ctdc_model_properties_file.yaml`** — property definitions (description, type, enum values, caDSR CDE binding, required vs preferred vs optional).
  - **`model-navigator-resources/version-history.md`** — what end users see in the Data Model Navigator's Version tab.
- **CDE source** — **caDSR**. New or updated CDEs and permissible values are requested via a **ServiceNow (SN)** ticket to the caDSR team. Assigned CDE codes are recorded in the CDE Request Workbook; the model's properties file carries the caDSR binding (or a `TBD` placeholder until codes are minted).
- **Loader repo** — **`CBIIT/crdc-ctdc-dataloader`** (branch `master`). Python-based loader. Affected modules vary by change type:
  - Data Loader (`data_loader.py`, `loader.py`) — primary loader logic
  - File Loader (`file_loader.py`) — file metadata loader
  - ES Loader (`es_loader.py`) — OpenSearch loader
  - Model Converter (`model-converter.py`) — converts YAML schema to GraphQL schema used by the frontend
- **Frontend repos** — `CBIIT/crdc-ctdc-frontend` and related. Rendering changes when new fields need to be displayed.
- **Loading pipeline** — **Jenkins**. Same two pipelines as Data Loading: *lower tiers* (targets Dev and QA) and *upper tiers* (targets Stage and Prod). For model updates, the pipeline run also exercises the updated loader code against the new schema.
- **Graph database** — **Memgraph**. The canonical metadata store. Model updates that add nodes or properties require a coordinated migration step before existing data can be reloaded under the new schema.
- **Search index** — **OpenSearch**. Reindex follows every load. If the schema change affects searchable fields, the OpenSearch index mapping may also need updating.
- **Downstream consumer** — **`CBIIT/crdc-datahub-models`**. After tagging a release on `ctdc-model`'s `prod` branch, GitHub Actions sync the new version into `crdc-datahub-models`. Verify by inspecting `cache/content.json` on the `prod` branch of that downstream repo — the CTDC entry should reflect the new version number.
- **Data Model Navigator** — the user-facing UI that displays the model. Reads from `version-history.md` and the YAML files; updates flow automatically once the new version is in place.

The template walks all of this in order: workbook & CDE coordination → schema change definition → affected code surfaces → promotion workflow → verification per environment → signoff per environment.

**Section order (12 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header — same rule as every other CTDC template.

1. `### 🎯 **Model Change Summary**` — Two to three sentences. What's changing in the model, why, the SemVer impact in plain English, and that the change is internally/CTDC-driven (this template's scope). Example: *"Add four optional curation properties to the `program` node to support the new Portfolio and Program Details pages. This is an internally/CTDC-driven, additive MINOR change — new optional properties — so existing data remains valid under the new schema and no re-load is required for correctness. The Data Model Navigator will reflect the new properties in its next version refresh."*

2. `### 📚 **CDE Request Workbook**` — Required section. **Every model change records its changes and CDE assignments in a CDE Request Workbook — there is no "no workbook" path.** Because this template covers internally/CTDC-driven changes, the workbook is the single persistent **CTDC Internal Data Modeling CDE Request Workbook**, which belongs to the **CTDC project itself (no individual owner)**. The workbook is the term-level source of truth (per-property and per-enum CDE bindings, caDSR coordination status, permissible-value decisions, nodes/properties touched); the ticket tracks *when* milestones land, not *what* every term resolves to. Do not duplicate per-term inventory or per-term status into the ticket.

   | Field | Value |
   |---|---|
   | Workbook | CTDC Internal Data Modeling CDE Request Workbook — [SharePoint](https://nih.sharepoint.com/:x:/r/sites/NCI-CBIIT-FNL-CTDC-CTDCProjectManagement/Shared%20Documents/CTDC%20Data/CTDC%20Data%20Model/CTDC-Internal-Data-Modeling_CDE_Request_Workbook.xlsx?d=wab1b301e20ef474b849f13f3d6e283ef&csf=1&web=1&e=CIAPgf) |
   | Workbook Owner | CTDC project — no individual owner (internal project workbook) |
   | Source-of-Truth Scope | Per-property and per-enum CDE bindings, caDSR CDE codes (or `TBD` placeholders), permissible-value decisions, nodes/properties touched |
   | Out-of-Scope for Ticket | Per-term decisions and caDSR coordination detail — tracked in the workbook, not duplicated here |

   **caDSR / ServiceNow coordination.** New or updated CDEs and permissible values are requested from the caDSR team via a **ServiceNow (SN) request**. Record the SN ticket ID(s) as they are created; the resulting caDSR CDE codes are recorded in the workbook. Until codes are minted, the model carries `TBD` placeholders for the affected properties' CDE bindings. **Formal Data Commons (DC) sign-off** is required for DC model changes and gates promotion to `prod` (see Section 6).

   | caDSR / coordination item | Value |
   |---|---|
   | ServiceNow request ID(s) | *(SN-XXXXXX — or "Not yet submitted" / "Not required: no new or changed CDEs")* |
   | caDSR CDE status | *(e.g., "Requested — awaiting codes"; "Codes assigned, recorded in workbook"; "N/A — no CDE change")* |
   | DC formal sign-off | *(Approver + date — required before `develop` → `prod`; recorded here and in Section 10)* |

3. `### 🔗 **Linked Work**` — Bullet list of every related ticket. Required entries:
   - **Parent Epic / Release:** CTDC-XXXX — *Release or feature name* (the release-level ticket if one exists, or the parent epic this model update enables)
   - **Downstream data load tickets:** CTDC-XXXX — *(if this model change unblocks a pending Data Loading Task; the load is the consumer of the schema change)*
   - **Upstream consumer tickets:** CTDC-XXXX — *(if a frontend feature or backend feature depends on the new schema being in place)*
   - **Related model updates:** CTDC-XXXX — *(if this update supersedes, follows, or is bundled with another model change)*

4. `### 🧬 **Model Change Details**` — Required field. The single source of truth for *what* is changing in the model. Fill in every row at ticket creation; leave PLACEHOLDER values only for items genuinely pending design decisions, and update them as the change firms up.

   | Field | Value | Notes |
   |---|---|---|
   | SemVer impact | *(MAJOR / MINOR / PATCH)* | MAJOR = breaking schema change (removed required prop, type change, removed node, removed enum value, tighter constraint). MINOR = backward-compatible addition (new optional prop/node, added enum value, new optional relationship). PATCH = doc-only or bug fix. |
   | Current model version | *(e.g., `1.20.3`)* | From `model-desc/ctdc_model_file.yaml` `Version:` field on `prod` branch today. |
   | Target model version | *(e.g., `1.21.0`)* | Must follow SemVer from the current version. This value goes into the YAML `Version:` field and the git tag — they must match exactly. |
   | Target git tag | *(e.g., `1.21.0`)* | Must match the YAML `Version:` value above, character-for-character. The downstream Data Hub reads from the YAML, not the tag — a mismatch silently fails. |
   | Schema artifacts updated | *(Checklist below)* | All three must update together for a property/node change. Adding to one without the others passes validation but leaves the Navigator inconsistent. |
   | Backward compatibility | *(Yes / No / Partial — explain)* | If No or Partial, describe what breaks and what existing data needs to do. Drives whether existing studies need re-loading. |
   | Affected node types | *(Bullet list of node names — e.g., `study`, `participant`)* | The nodes touched by this change. Drives the Section 7 verification scope. |
   | Affected properties | *(Bullet list of property names with action — e.g., `study.consent_group (added)`, `participant.age (renamed from age_at_diagnosis)`)* | Property-level detail of the change. |
   | Affected relationships | *(Bullet list — or "None")* | Relationships added, renamed, or having cardinality changes. |
   | Affected enum values | *(Bullet list — or "None")* | Values added to or removed from enumerated properties. |

   **Schema artifact checklist** (the three-files trio):

   | Artifact | Updated? | Description of change |
   |---|---|---|
   | `model-desc/ctdc_model_file.yaml` | ☐ | Node definitions, relationships, `Props:` lists, and the top-level `Version:` field |
   | `model-desc/ctdc_model_properties_file.yaml` | ☐ | Property definitions (description, type, enum values, caDSR binding, required/preferred/optional) |
   | `model-navigator-resources/version-history.md` | ☐ | Release notes entry — what end users see in the Data Model Navigator's Version tab |

   Reference: the canonical procedure for editing, branching, tagging, and downstream-syncing model changes lives at **[`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md)** on the `prod` branch of `ctdc-model`. This ticket tracks the change; the SOP describes the procedure.

5. `### 🏗️ **Affected Code & Loaders**` — Required field. Every model update touches the model repo itself; some also touch loader code, frontend rendering, and Navigator resources. Mark each row affected; cross off rows that this specific change does not impact. For a lightweight additive change, only the always-required rows may be checked — that is expected and fine under this template (route by driver, not weight).

   | Artifact | Affected? | Owner | Notes |
   |---|---|---|---|
   | **`CBIIT/ctdc-model`** (branches `develop` → `prod`) | ☑️ Always | Data engineering | The model change itself. Feature branch named `CTDC-XXXX` off `develop`. |
   | **`CBIIT/crdc-ctdc-dataloader`** (branch `master`) | ☐ | Backend / data engineering | Loader code that consumes the schema. Required for new node types, new required properties, type changes, or anything affecting how the loader parses or validates incoming data; also when new optional properties need values populated at load. Modules: `data_loader.py`, `loader.py`, `file_loader.py`, `es_loader.py`, `model-converter.py`. |
   | **CTDC frontend repo(s)** (e.g., `CBIIT/crdc-ctdc-frontend`) | ☐ | Frontend engineering | Rendering changes when new fields, columns, filters, or facets need to surface in the UI. May also include the GraphQL schema regenerated by `model-converter.py`. Rendering is often owned by linked consumer stories rather than duplicated here. |
   | **Data Model Navigator** (`model-navigator-resources/` in `ctdc-model`) | ☑️ Always | Data engineering | The Navigator UI reads from `version-history.md` and the YAML files; updates flow automatically once the new version is tagged. |
   | **`CBIIT/crdc-datahub-models`** (downstream sync, branch `prod`) | ☑️ Always | Automated | GitHub Actions sync from `ctdc-model` `prod` tag. Verification only — no manual edits. Check `cache/content.json` on `prod` to confirm. |

   Pending decisions: PLACEHOLDER. Confirm with **Yizhen Chen** (tech lead — scope of dataloader and frontend touches), **Patrick Breads** or **Stephanie Singleton** (data engineering — model file edits), and **Charles Ngu** (DevOps — Jenkins pipeline if any pipeline change is needed for the new schema).

6. `### 🚦 **Promotion Workflow**` — Numbered list of the end-to-end promotion steps. The branching in `ctdc-model` mirrors the Jenkins two-tier pipeline: `develop` is for lower tiers (Dev + QA); `prod` is for upper tiers (Stage + Prod), with a tag-and-release step between the two. Standard CTDC sequence:

   **Pre-change (one-time)**
   1. Confirm the change is internally/CTDC-driven (this template) rather than study-driven (use 7g). Confirm the CDE Request Workbook in Section 2 is the internal workbook and is recording this change.
   2. If the change adds or updates any CDEs or permissible values, submit the **ServiceNow request to caDSR** and record the SN ID in Section 2. Record assigned CDE codes in the internal workbook as they arrive; the model carries `TBD` placeholders until then.
   3. Confirm the target SemVer in Section 4 is consistent with what's actually changing (MAJOR / MINOR / PATCH per the SOP's classification rules).
   4. Confirm the target YAML `Version:` and target git tag match exactly. The Data Hub reads from YAML; a mismatched tag will silently fail downstream sync.
   5. If the change affects loader code or frontend rendering (Section 5 rows checked beyond the always-required ones), confirm those repos have feature branches lined up to land alongside the model change.

   **Lower tiers — `develop` branch**
   6. Create feature branch `CTDC-XXXX` off `develop` in `ctdc-model`. Edit the three schema artifacts together. Commit with message `CTDC-XXXX: <change summary>`. Push and open PR into `develop`. CI must pass; assign reviewer.
   7. After merge to `develop`, run the Jenkins **lower-tiers** data loading pipeline targeting Dev (this exercises the updated loader against the new schema if the dataloader was also updated).
   8. Verify in Dev: Memgraph reflects the new schema (new node types / properties / relationships present), OpenSearch reindex completed, application pages affected by the change render correctly, Data Model Navigator shows the new version once propagated. Record results in Section 8 (Per-Environment Verification).
   9. Run the Jenkins **lower-tiers** pipeline targeting QA. Assign for QA testing. Tester verifies the schema and any sample loads against the new model. Tester initials Section 10 on completion.

   **Tag and release**
   10. **Confirm the formal DC sign-off is recorded** in Section 2 (and Section 10) for the model change. DC model changes must not promote to `prod` without it. If caDSR codes were requested, confirm assigned codes are recorded in the workbook (or that remaining `TBD` placeholders are an accepted, documented risk).
   11. Open a PR from `develop` → `prod` in `ctdc-model`. CI must pass; assign reviewer. After merge, create a git tag matching the YAML `Version:` field exactly (e.g., `1.21.0`), and publish a GitHub Release with release notes.
   12. Verify downstream sync: GitHub Actions update `CBIIT/crdc-datahub-models`. Inspect `cache/content.json` on the `prod` branch of that repo — the CTDC entry should reflect the new version. If sync fails, do not proceed to upper tiers.

   **Upper tiers — `prod` branch**
   13. Run the Jenkins **upper-tiers** data loading pipeline targeting Stage. Assign for Stage testing and signoff. Tester verifies the schema landed and the application surfaces still render correctly. Tester initials Section 10 on completion.
   14. Run the Jenkins **upper-tiers** pipeline targeting Prod. Assign for Prod verification and signoff. Verify with the live application URL and the live Data Model Navigator. Tester initials Section 10 on completion. **This is the trigger to close the ticket.**

7. `### 🧪 **Verification Surfaces**` — Bullet list naming every surface that must be checked after each environment promotion. The standard CTDC set, expanded based on the affected node types and properties named in Section 4:
   - **Memgraph schema** — confirm new node types, properties, and relationships are present; renamed entities reflect the new name; deprecated entities are gone (for MAJOR changes)
   - **OpenSearch index mapping** — confirm new searchable fields are indexed; reindex completed without error
   - **Application pages affected by the change** — for each node type touched in Section 4, confirm the relevant page renders correctly (Study Details for `study` changes, Participant Details for `participant` changes, etc.)
   - **Data Model Navigator** — confirm the new version appears in the Version tab and the schema diagram reflects the change
   - **GraphQL schema** — if regenerated via `model-converter.py`, confirm the new schema is in place in the backend service
   - **Loader behavior** — if the dataloader was updated, run a sample load and confirm new properties are populated, validation rules fire correctly, and no regressions on existing studies
   - **Frontend rendering** — confirm new fields/columns/filters appear; existing pages still render without errors
   - **Application error metrics** — confirm no spike in 4xx / 5xx after the model promotion
   - **Logs (CloudWatch or equivalent)** — confirm no schema-related exceptions during reindex or loader runs
   - **Downstream `crdc-datahub-models`** — confirm `cache/content.json` on `prod` reflects the new CTDC version

8. `### 📊 **Per-Environment Verification**` — Table to record observed schema state, loader behavior, and any anomalies, per environment. Same row shape across all four environments so anomalies stand out by visual comparison.

   | Environment | Model version (post-promotion) | Schema artifacts verified in Memgraph | Application surfaces verified | Anomalies / notes |
   |---|---|---|---|---|
   | Dev | | | | |
   | QA | | | | |
   | Stage | | | | |
   | Prod | | | | |

9. `### 🤝 **Collaboration & Handoffs**` — Bullet list of the people and touchpoints required for this update. The standard CTDC set:
   - **Data engineering / model owner** — owns the schema change itself (Patrick Breads, Stephanie Singleton)
   - **Data Concierge** — owns the internal CDE Request Workbook entries and caDSR/ServiceNow coordination (Stephanie Singleton)
   - **Backend engineering** — owns dataloader updates when Section 5's dataloader row is checked (Adam Davenport, Eric Miller)
   - **Frontend engineering** — owns rendering updates when Section 5's frontend row is checked (Nahom Tesfatsion, with Yizhen Chen as tech lead consult)
   - **DevOps** — owns Jenkins pipeline execution and any pipeline configuration needed for the new schema (Charles Ngu)
   - **Data Commons sign-off authority** — provides the formal DC approval that gates `prod` promotion (DM Fed Lead / DC Fed Lead per the DM Updates SOP)
   - **QA** — owns environment-level signoff (Valentina Epishina or designated tester)
   - **TPM** — owns end-to-end coordination, multi-repo sequencing, and the Testing Signoff record (Gina Kuffel)

10. `### ✅ **Testing Signoff**` — The completion record. Tester fills in date and initials per environment as work progresses. The DC sign-off row records the formal Data Commons approval gating `prod` promotion. **Prod signoff is the trigger to transition the ticket to Closed.**

    | Milestone / Environment | Completion Date | Initials |
    |---|---|---|
    | DC formal sign-off (gates `prod`) | | |
    | Dev | | |
    | QA | | |
    | Stage | | |
    | Prod | | |

11. `### 🔍 **Open Questions / Risks**` — Bullet list of decisions or unknowns to resolve. Each bullet is one question. Resolve in ticket comments so the audit trail lives with the change. If none at ticket creation, state "None at this time" — items added later become comments, not edits to this section. Common entries on model updates:
    - Where do any new property *values* come from? Adding a property defines the field; if no study submission carries the value, the curation source (a curation TSV/manifest the dataloader ingests, a workbook tab, etc.) must be decided. This drives whether a follow-on Data Loading Task is queued.
    - Is existing data backward-compatible under the new schema, or does it need a re-load? (Drives whether a Data Loading Task is queued behind this ticket.)
    - Are caDSR CDE codes still outstanding (model carrying `TBD`)? If so, note the ServiceNow ticket and the timing risk to `prod` promotion.
    - Does the change require a corresponding update to the dataloader's validation rules? (If yes, the dataloader row in Section 5 must be checked.)
    - Will the frontend need new component work to render the change, or is it transparent? (Determines whether frontend engineering is on the critical path.)
    - Is the SemVer classification (MAJOR/MINOR/PATCH) accurate per the SOP's rules?
    - Does adding any *node* (not just properties or PVs) trigger a future model-mapping need? (Per the DM Updates SOP, node additions should be flagged for model mapping; PV/property-only changes do not. The mapping infrastructure is forward-looking — flag the need even if it can't yet be actioned.)

12. `### 📝 **Notes**` — Bullet list. Optional content: links to design discussions or model-owner meetings, prior model-change retrospectives, references to caDSR Common Data Elements being adopted, terminology translations (Bento "Case" → CTDC "Participant"). If there's no meaningful note, write "None at this time."

**Standing emoji set (12 entries)**

| Section | Emoji |
|---|---|
| Model Change Summary | 🎯 |
| CDE Request Workbook | 📚 *(shared with study-submission modeling template — same source-of-truth role)* |
| Linked Work | 🔗 |
| Model Change Details | 🧬 *(unique to data model update task)* |
| Affected Code & Loaders | 🏗️ *(unique to data model update task)* |
| Promotion Workflow | 🚦 *(shared with data loading task — same operational shape)* |
| Verification Surfaces | 🧪 *(shared with data loading task — same operational shape)* |
| Per-Environment Verification | 📊 *(shared with data loading task — same operational shape)* |
| Collaboration & Handoffs | 🤝 |
| Testing Signoff | ✅ |
| Open Questions / Risks | 🔍 |
| Notes | 📝 |

**Required content rules (Data Model Update Task specific — universal Jira rules in 7b-shared also apply)**

- **Scope is internally / CTDC-driven model changes, across the full weight range.** Lightweight additive changes (new optional properties/enums from the application roadmap) *and* heavyweight infrastructure changes (breaking schema changes, framework upgrades, multi-repo refactors). The driver — the CTDC project itself, not an incoming study — is what selects this template. **Study-driven** model changes use the **Data Modeling for Study Submission** template (Section 7g). Loading new study data or adding data to an existing study uses the Data Loading Task template (Section 7e). Software development that does not touch the schema uses the software development template family.
- **Every change records in a CDE Request Workbook (Section 2).** For this template that is the internal CTDC workbook, owned by the project (no individual owner). There is no "no workbook" path. The workbook is the term-level source of truth; the ticket does not duplicate per-term inventory or status.
- **caDSR / ServiceNow + DC sign-off are not optional.** New or updated CDEs/PVs go through a ServiceNow request to caDSR (record the SN ID in Section 2). DC model changes require a formal DC sign-off recorded in Section 2 and Section 10, gating `prod` promotion (Section 6). This holds even for additive internal changes.
- **No Acceptance Criteria section.** Model updates are operational SOP work; the completion bar is the Testing Signoff table plus the Verification Surfaces checklist. AC belongs on user stories that consume the new schema, not on the model change itself.
- **One Task per end-to-end model update** — develop branch through Prod, not one ticket per repo. The Affected Code & Loaders table (Section 5) and Testing Signoff table (Section 10) together are the single source of truth for what's been touched and where the change is in the pipeline.
- **Issue type is Task** on this tracker. Do not use Story or Subtask.
- **Parent Epic field set on the ticket itself** via `customfield_12350` when a release-level epic exists.
- **YAML `Version:` field and git tag must match exactly.** Surface this in Section 4 as two distinct rows (Target model version + Target git tag) so the values can be eyeballed before tagging. A mismatch silently fails the downstream sync.
- **Three schema artifacts updated together.** The checklist in Section 4 enforces the trio: `ctdc_model_file.yaml`, `ctdc_model_properties_file.yaml`, `version-history.md`. Update one without the others and the Navigator becomes inconsistent.
- **SemVer impact is a required field.** MAJOR / MINOR / PATCH must be classified at ticket creation per the SOP's rules. Downstream consumers plan migration around this classification.
- **Affected Code & Loaders table is mandatory and complete.** All five canonical rows present; the always-required rows checked; the conditional rows (dataloader, frontend) checked or explicitly crossed off. Don't silently omit a row. For a lightweight additive change, only the always-required rows may be checked — that is expected; do not reroute to 7g on weight grounds.
- **The model SOP is authoritative for procedure.** This template tracks the change; the SOP at `https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md` describes how to do it, and the DM Updates Kanban SOP is authoritative for the caDSR/ServiceNow and DC-sign-off steps. If a SOP changes, this template's references should be re-checked against it.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as every other Jira description on this tracker.

**Writing-and-publishing workflow**

1. Confirm the work is internally/CTDC-driven (this template), not study-driven (Section 7g) and not a pure data load (Section 7e). If it is study-driven — requested by an incoming study submission with that study's CDE Request Workbook — use 7g instead.
2. Confirm the internal CDE Request Workbook is recording this change (Section 2). If new/updated CDEs or PVs are involved, line up the ServiceNow request to caDSR and capture the SN ID.
3. Confirm the SemVer classification per the SOP — MAJOR / MINOR / PATCH. Additive optional properties/enums are MINOR; breaking changes are MAJOR; doc-only is PATCH. All are valid under this template when internally driven.
4. Verify the target YAML `Version:` and target git tag values are identical character-for-character. Write them as two separate rows in Section 4's table so the values are easy to compare.
5. Determine which Section 5 rows are affected. The model repo and Navigator are always checked; the dataloader and frontend rows are checked per the actual scope of the change.
6. Create the data model update task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields`. Assign to the TPM (Gina Kuffel) initially — the TPM coordinates the multi-repo work and reassigns per-environment as work progresses.
7. Push the description in two steps: create with the placeholder, then `jira_update_issue` with the full Markdown body. `jira_create_issue` renders inconsistently on long Markdown; `jira_update_issue` performs clean Markdown-to-Jira-wiki conversion.
8. Add a "Relates" issue link between the data model update task and: any downstream Data Loading Task (Section 3), any consuming feature ticket, and any related model updates.
9. Verify the rendered description with a UI screenshot — wiki source is unreliable as a render preview.
10. As each environment completes, the assigned tester adds their initials and date to Section 10 (Testing Signoff) and notes results in Section 8 (Per-Environment Verification). The DC sign-off row in Section 10 is filled when the formal DC approval lands.
11. **Prod signoff + downstream sync verification together are the close trigger.** Once Section 10's Prod row is filled in *and* `crdc-datahub-models` `cache/content.json` on `prod` shows the new version, transition the ticket to Closed.

**When to expand vs trim**

- **MINOR additive change, internally driven** (new optional property, new optional node, added enum value, new optional relationship — e.g., CTDC-2068's four Program curation properties) → use this template, trimmed. Section 5 may show only the always-required rows plus dataloader if values need populating; verification is light. Section 2 (internal workbook) and the caDSR/ServiceNow + DC-sign-off steps still apply if any CDE/PV is added or changed.
- **PATCH change** (description fix, examples, metadata text) → use the template but expect Sections 5 (Affected Code & Loaders) and 7 (Verification Surfaces) to be light — only the model repo and Navigator are touched; verification focuses on the Navigator's display. Usually no caDSR/SN request.
- **MAJOR change** (removed required property, type change, removed node, removed enum, tightened constraint) → use the template and expand: Section 4 should explicitly describe what existing data does under the new schema; Section 11 (Open Questions / Risks) must include the backward-compatibility plan; expect a Data Loading Task to follow with re-load of affected studies.
- **Multi-property batched change** (several properties / nodes changing together for one release) → one ticket per coherent change-set. If the changes are independent and could ship at different times, use one ticket each. If they must ship together as one version bump, one ticket with expanded Section 4 detail.
- **Pure documentation update to `ctdc-model`** (README, owner guide, SOP edits) → this template is overkill. A free-form Task with the doc change description and a single PR link is enough.

**When NOT to use this template**

This template covers internally/CTDC-driven model changes across the full weight range. The following kinds of work are different:

- **Study-driven model changes** — properties, enums, or permissible values requested by an incoming study submission, anchored on that study's CDE Request Workbook. Use the **Data Modeling for Study Submission** template (Section 7g). The driver is the study, and the term-level truth lives in the study's workbook (owned by that study's Data Concierge), not the internal workbook.
- **Loading data into the existing schema** — new studies, new data added to existing studies, megazip loads. Use the **Data Loading Task** template (Section 7e). That's the sibling template in the data management lane for the loading sub-function.
- **Software development that does not change the schema** — frontend features, backend refactors, microservice work, bug fixes, library upgrades. Use the software development family: User Story, Features Epic, Application Pages Epic, Microservices Epic, Design Task, Bug.
- **CRDC platform changes** — Fence, IndexD, Submission Portal upgrades owned by CRDC platform teams. Out of CTDC scope.
- **Bento Core MDF framework changes** — changes to the underlying MDF tooling itself (`bento-mdf` submodule). Upstream concern; file with the Bento team.

If a CRDC submission requires schema changes before it can be loaded, that's two pieces of data management work: a modeling ticket and a Data Loading Task. They depend on each other but live in separate tickets, with the load blocked until the model update has landed and stabilized in each environment. If the change is requested by the study submission, the model work is a 7g ticket (study workbook). If the change is initiated by the CTDC project itself, the model work is a 7f ticket (internal workbook) regardless of whether it is additive or breaking.

**Changelog**

- **v3 (2026-06-01)** — Reframed the 7f/7g split on the **driver** (internally/CTDC-driven vs. study-driven) rather than on weight (heavyweight infrastructure vs. lightweight additive). Made the **CDE Request Workbook universal**: every model change records in a workbook — the internal CTDC project workbook for 7f, a study workbook for 7g — with no "no workbook" path. Added Section 2 (CDE Request Workbook) with the internal workbook, caDSR/ServiceNow coordination, and the DC sign-off gate; renumbered subsequent sections (12 total). Added the ServiceNow-to-caDSR and formal DC-sign-off steps to the Promotion Workflow and a DC-sign-off row to Testing Signoff. Rewrote scope, antipatterns, and when-to-expand/trim so internally-driven additive work (canonical pilot: CTDC-2068, Program curation properties) is squarely in scope rather than bounced to 7g. README decision tree and SKILL.md routing rule to be updated to match in a follow-on edit.
- **v2 (2026-05-20, superseded)** — Scoped to infrastructure-level changes only (breaking, framework upgrades, multi-repo refactors); routed additive work to 7g and had no CDE Request Workbook concept. Superseded by v3's driver-based, universal-workbook model.
