### 7f. 🧬 Data Model Update Task Template (Drafted)

> **Use this template for every CTDC data model update — any change to the schema in `CBIIT/ctdc-model`** (new node types, new properties, renamed relationships, changed cardinalities, enum value additions, model version upgrades). Like the Data Loading Task, this template covers **data management** work and promotes through Dev → QA → Stage → Prod. Schema changes touch loader code in `CBIIT/crdc-ctdc-dataloader` and frontend rendering — those code updates are children of this data management ticket, not separate software development tickets.

**Why this template**

The CTDC team's data management function has two sub-functions, and both promote data through the same Dev → QA → Stage → Prod ladder:

- **Loading data** — taking a CRDC submission's *contents* (study metadata, files, IndexD entries) and getting them into CTDC's databases. Tracked with the Data Loading Task template (Section 7e).
- **Modeling data** — changing the *shape* of what CTDC's databases can hold (new node types, new properties, renamed relationships, version bumps). Tracked with **this** template.

A model update is a single end-to-end change: edit `model-desc/` YAML files, update `version-history.md`, bump the YAML `Version:` field, update any dataloader / frontend code that consumes the new schema, run the lower-tiers Jenkins pipeline against Dev/QA, promote `develop` → `prod` in `ctdc-model`, tag the release, verify downstream sync, run upper-tiers pipeline against Stage/Prod. The work spans repos and pipelines, but it is one piece of work and lives in one ticket.

The canonical procedural reference for *how* model changes are designed, branched, committed, reviewed, tagged, and synced downstream is the SOP in the model repo itself: **[`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md)**. This Jira template does **not** duplicate that SOP — it tracks the model change as a ticket and points at the SOP for procedural detail. If anything in this template appears to conflict with the SOP, the SOP is authoritative.

The most common antipatterns we've seen on model update tickets:

1. **One ticket per affected repo.** Splits a single model change across the `ctdc-model` ticket, the `crdc-ctdc-dataloader` ticket, and the frontend ticket, with manual cross-linking. A model change is one end-to-end unit; the multi-repo touch is implementation detail, not separate work. Use the Affected Code & Loaders table in Section 4 to track multi-repo updates within one ticket.
2. **Treating schema changes as software development.** Schema changes generate code work (loader updates, frontend renderers), but the *concern* is data shape — what nodes exist, what properties they carry, what relationships connect them. That is data management. The cascading code work is owned by the data management ticket, not split out as standalone software development tickets that lose the connection to the model change.
3. **YAML `Version:` field doesn't match the git tag.** The downstream Data Hub reads the version from `model-desc/ctdc_model_file.yaml`, not from the tag name. A mismatch silently fails to advance the displayed version downstream. The Section 3 Model Change Details table makes both values explicit so they can be verified together before tagging.
4. **Three required-together artifacts updated independently.** Adding a property to `ctdc_model_properties_file.yaml` without referencing it in the relevant node's `Props:` list in `ctdc_model_file.yaml` will pass validation but the property will not appear in the Data Model Navigator. Likewise, forgetting to update `version-history.md` leaves the Navigator's Version tab stale. Section 3's checklist enforces the trio.
5. **No per-environment signoff record.** Same antipattern as Data Loading. When QA, Stage, and Prod testers initial their work in Slack threads or comments, there's no consolidated record. The Testing Signoff table in Section 9 is the single source of truth.
6. **Missing SemVer classification.** "Update the model" doesn't communicate whether the change is MAJOR (breaking), MINOR (backward-compatible addition), or PATCH (bug fix). Downstream consumers need to know whether to plan a migration. Section 3 makes SemVer impact a required field.

The template below resolves all six with three commitments:

1. **One Task per end-to-end model update.** Single source of truth from initial schema edit through Prod signoff and downstream verification. The Testing Signoff section is the artifact that justifies the one-ticket approach.
2. **Branching mirrors Jenkins tiering.** The `ctdc-model` repo's `develop` branch maps to Dev + QA (lower tiers); the `prod` branch maps to Stage + Prod (upper tiers). The promotion workflow is the same two-tier ladder as Data Loading, plus the tag-and-release step between tiers.
3. **Explicit schema, explicit code, explicit downstream.** Section 3 names the schema artifacts being changed (YAML files, version-history entry, SemVer impact). Section 4 names the code and downstream artifacts being touched (the five canonical affected surfaces). Verification confirms both the schema landed *and* the downstream consumers picked it up.

**Pipeline, branch, & store anatomy (read this once before drafting)**

A CTDC model update touches multiple systems in sequence. Knowing the chain helps every section of the template land:

- **Source-of-truth repo** — **`CBIIT/ctdc-model`** holds the canonical model definition. Two branches matter: **`develop`** (pre-release; what Dev and QA serve) and **`prod`** (release; what Stage and Prod serve, and what the downstream Data Hub tracks via tag).
- **Schema artifacts** — three files in `ctdc-model` must update together for a property/node change to be complete:
  - **`model-desc/ctdc_model_file.yaml`** — node and relationship definitions. The `Version:` field at the top of this file is the canonical model version.
  - **`model-desc/ctdc_model_properties_file.yaml`** — property definitions (description, type, enum values, caDSR CDE binding, required vs preferred vs optional).
  - **`model-navigator-resources/version-history.md`** — what end users see in the Data Model Navigator's Version tab.
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

The template walks all of this in order: schema change definition → affected code surfaces → promotion workflow → verification per environment → signoff per environment.

**Section order (11 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header — same rule as every other CTDC template.

1. `### 🎯 **Model Change Summary**` — Two to three sentences. What's changing in the model, why, and the SemVer impact in plain English. Example: *"Add a `consent_group` property to the `study` node to capture the consent classification for each Cancer Moonshot Biobank cohort. This is a MINOR change — a new optional property — so existing data remains valid under the new schema and no data migration is required. The Data Model Navigator will reflect the new property in its next version refresh."*

2. `### 🔗 **Linked Work**` — Bullet list of every related ticket. Required entries:
   - **Parent Epic / Release:** CTDC-XXXX — *Release or feature name* (the release-level ticket if one exists, or the parent epic this model update enables)
   - **Downstream data load tickets:** CTDC-XXXX — *(if this model change unblocks a pending Data Loading Task; the load is the consumer of the schema change)*
   - **Upstream consumer tickets:** CTDC-XXXX — *(if a frontend feature or backend feature depends on the new schema being in place)*
   - **Related model updates:** CTDC-XXXX — *(if this update supersedes, follows, or is bundled with another model change)*

3. `### 🧬 **Model Change Details**` — Required field. The single source of truth for *what* is changing in the model. Fill in every row at ticket creation; leave PLACEHOLDER values only for items genuinely pending design decisions, and update them as the change firms up.

   | Field | Value | Notes |
   |---|---|---|
   | SemVer impact | *(MAJOR / MINOR / PATCH)* | MAJOR = breaking schema change (removed required prop, type change, removed node, removed enum value, tighter constraint). MINOR = backward-compatible addition (new optional prop/node, added enum value, new optional relationship). PATCH = doc-only or bug fix. |
   | Current model version | *(e.g., `1.20.3`)* | From `model-desc/ctdc_model_file.yaml` `Version:` field on `prod` branch today. |
   | Target model version | *(e.g., `1.21.0`)* | Must follow SemVer from the current version. This value goes into the YAML `Version:` field and the git tag — they must match exactly. |
   | Target git tag | *(e.g., `1.21.0`)* | Must match the YAML `Version:` value above, character-for-character. The downstream Data Hub reads from the YAML, not the tag — a mismatch silently fails. |
   | Schema artifacts updated | *(Checklist below)* | All three must update together for a property/node change. Adding to one without the others passes validation but leaves the Navigator inconsistent. |
   | Backward compatibility | *(Yes / No / Partial — explain)* | If No or Partial, describe what breaks and what existing data needs to do. Drives whether existing studies need re-loading. |
   | Affected node types | *(Bullet list of node names — e.g., `study`, `participant`)* | The nodes touched by this change. Drives the Section 6 verification scope. |
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

4. `### 🏗️ **Affected Code & Loaders**` — Required field. Every model update touches the model repo itself; some also touch loader code, frontend rendering, and Navigator resources. Mark each row affected; cross off rows that this specific change does not impact.

   | Artifact | Affected? | Owner | Notes |
   |---|---|---|---|
   | **`CBIIT/ctdc-model`** (branches `develop` → `prod`) | ☑️ Always | Data engineering | The model change itself. Feature branch named `CTDC-XXXX` off `develop`. |
   | **`CBIIT/crdc-ctdc-dataloader`** (branch `master`) | ☐ | Backend / data engineering | Loader code that consumes the schema. Required for new node types, new required properties, type changes, or anything affecting how the loader parses or validates incoming data. Modules: `data_loader.py`, `loader.py`, `file_loader.py`, `es_loader.py`, `model-converter.py`. |
   | **CTDC frontend repo(s)** (e.g., `CBIIT/crdc-ctdc-frontend`) | ☐ | Frontend engineering | Rendering changes when new fields, columns, filters, or facets need to surface in the UI. May also include the GraphQL schema regenerated by `model-converter.py`. |
   | **Data Model Navigator** (`model-navigator-resources/` in `ctdc-model`) | ☑️ Always | Data engineering | The Navigator UI reads from `version-history.md` and the YAML files; updates flow automatically once the new version is tagged. |
   | **`CBIIT/crdc-datahub-models`** (downstream sync, branch `prod`) | ☑️ Always | Automated | GitHub Actions sync from `ctdc-model` `prod` tag. Verification only — no manual edits. Check `cache/content.json` on `prod` to confirm. |

   Pending decisions: PLACEHOLDER. Confirm with **Yizhen Chen** (tech lead — scope of dataloader and frontend touches), **Patrick Breads** or **Stephanie Singleton** (data engineering — model file edits), and **Charles Ngu** (DevOps — Jenkins pipeline if any pipeline change is needed for the new schema).

5. `### 🚦 **Promotion Workflow**` — Numbered list of the end-to-end promotion steps. The branching in `ctdc-model` mirrors the Jenkins two-tier pipeline: `develop` is for lower tiers (Dev + QA); `prod` is for upper tiers (Stage + Prod), with a tag-and-release step between the two. Standard CTDC sequence:

   **Pre-change (one-time)**
   1. Confirm the target SemVer in Section 3 is consistent with what's actually changing (MAJOR / MINOR / PATCH per the SOP's classification rules).
   2. Confirm the target YAML `Version:` and target git tag match exactly. The Data Hub reads from YAML; a mismatched tag will silently fail downstream sync.
   3. If the change affects loader code or frontend rendering (Section 4 rows checked beyond the always-required ones), confirm those repos have feature branches lined up to land alongside the model change.

   **Lower tiers — `develop` branch**
   4. Create feature branch `CTDC-XXXX` off `develop` in `ctdc-model`. Edit the three schema artifacts together. Commit with message `CTDC-XXXX: <change summary>`. Push and open PR into `develop`. CI must pass; assign reviewer.
   5. After merge to `develop`, run the Jenkins **lower-tiers** data loading pipeline targeting Dev (this exercises the updated loader against the new schema if the dataloader was also updated).
   6. Verify in Dev: Memgraph reflects the new schema (new node types / properties / relationships present), OpenSearch reindex completed, application pages affected by the change render correctly, Data Model Navigator shows the new version once propagated. Record results in Section 7 (Per-Environment Verification).
   7. Run the Jenkins **lower-tiers** pipeline targeting QA. Assign for QA testing. Tester verifies the schema and any sample loads against the new model. Tester initials Section 9 on completion.

   **Tag and release**
   8. Open a PR from `develop` → `prod` in `ctdc-model`. CI must pass; assign reviewer. After merge, create a git tag matching the YAML `Version:` field exactly (e.g., `1.21.0`), and publish a GitHub Release with release notes.
   9. Verify downstream sync: GitHub Actions update `CBIIT/crdc-datahub-models`. Inspect `cache/content.json` on the `prod` branch of that repo — the CTDC entry should reflect the new version. If sync fails, do not proceed to upper tiers.

   **Upper tiers — `prod` branch**
   10. Run the Jenkins **upper-tiers** data loading pipeline targeting Stage. Assign for Stage testing and signoff. Tester verifies the schema landed and the application surfaces still render correctly. Tester initials Section 9 on completion.
   11. Run the Jenkins **upper-tiers** pipeline targeting Prod. Assign for Prod verification and signoff. Verify with the live application URL and the live Data Model Navigator. Tester initials Section 9 on completion. **This is the trigger to close the ticket.**

6. `### 🧪 **Verification Surfaces**` — Bullet list naming every surface that must be checked after each environment promotion. The standard CTDC set, expanded based on the affected node types and properties named in Section 3:
   - **Memgraph schema** — confirm new node types, properties, and relationships are present; renamed entities reflect the new name; deprecated entities are gone (for MAJOR changes)
   - **OpenSearch index mapping** — confirm new searchable fields are indexed; reindex completed without error
   - **Application pages affected by the change** — for each node type touched in Section 3, confirm the relevant page renders correctly (Study Details for `study` changes, Participant Details for `participant` changes, etc.)
   - **Data Model Navigator** — confirm the new version appears in the Version tab and the schema diagram reflects the change
   - **GraphQL schema** — if regenerated via `model-converter.py`, confirm the new schema is in place in the backend service
   - **Loader behavior** — if the dataloader was updated, run a sample load and confirm new properties are populated, validation rules fire correctly, and no regressions on existing studies
   - **Frontend rendering** — confirm new fields/columns/filters appear; existing pages still render without errors
   - **Application error metrics** — confirm no spike in 4xx / 5xx after the model promotion
   - **Logs (CloudWatch or equivalent)** — confirm no schema-related exceptions during reindex or loader runs
   - **Downstream `crdc-datahub-models`** — confirm `cache/content.json` on `prod` reflects the new CTDC version

7. `### 📊 **Per-Environment Verification**` — Table to record observed schema state, loader behavior, and any anomalies, per environment. Same row shape across all four environments so anomalies stand out by visual comparison.

   | Environment | Model version (post-promotion) | Schema artifacts verified in Memgraph | Application surfaces verified | Anomalies / notes |
   |---|---|---|---|---|
   | Dev | | | | |
   | QA | | | | |
   | Stage | | | | |
   | Prod | | | | |

8. `### 🤝 **Collaboration & Handoffs**` — Bullet list of the people and touchpoints required for this update. The standard CTDC set:
   - **Data engineering / model owner** — owns the schema change itself (Patrick Breads, Stephanie Singleton)
   - **Backend engineering** — owns dataloader updates when Section 4's dataloader row is checked (Adam Davenport, Eric Miller)
   - **Frontend engineering** — owns rendering updates when Section 4's frontend row is checked (Nahom Tesfatsion, with Yizhen Chen as tech lead consult)
   - **DevOps** — owns Jenkins pipeline execution and any pipeline configuration needed for the new schema (Charles Ngu)
   - **QA** — owns environment-level signoff (Valentina Epishina or designated tester)
   - **TPM** — owns end-to-end coordination, multi-repo sequencing, and the Testing Signoff record (Gina Kuffel)

9. `### ✅ **Testing Signoff**` — The completion record. Tester fills in date and initials per environment as work progresses. **Prod signoff is the trigger to transition the ticket to Closed.**

   | Environment | Testing Completion Date | Tester Initials |
   |---|---|---|
   | Dev | | |
   | QA | | |
   | Stage | | |
   | Prod | | |

10. `### 🔍 **Open Questions / Risks**` — Bullet list of decisions or unknowns to resolve. Each bullet is one question. Resolve in ticket comments so the audit trail lives with the change. If none at ticket creation, state "None at this time" — items added later become comments, not edits to this section. Common entries on model updates:
    - Is existing data backward-compatible under the new schema, or does it need a re-load? (Drives whether a Data Loading Task is queued behind this ticket.)
    - Does the change require a corresponding update to the dataloader's validation rules? (If yes, the dataloader row in Section 4 must be checked.)
    - Will the frontend need new component work to render the change, or is it transparent? (Determines whether frontend engineering is on the critical path.)
    - Is the SemVer classification (MAJOR/MINOR/PATCH) accurate per the SOP's rules?
    - Is the timing for tag-and-release coordinated with any downstream consumers that may need to plan around the version bump?

11. `### 📝 **Notes**` — Bullet list. Optional content: links to design discussions or model-owner meetings, prior model-change retrospectives, references to caDSR Common Data Elements being adopted, terminology translations (Bento "Case" → CTDC "Participant"). If there's no meaningful note, write "None at this time."

**Standing emoji set (11 entries)**

| Section | Emoji |
|---|---|
| Model Change Summary | 🎯 |
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

- **Scope is data model updates only.** Schema changes to `CBIIT/ctdc-model`. Loading new study data or adding data to an existing study uses the Data Loading Task template (Section 7e). Software development that does not touch the schema (frontend features, backend refactors, microservice changes, bug fixes that don't involve the model) uses the software development template family (User Story, Features Epic, Application Pages Epic, Microservices Epic, Design Task, Bug).
- **No Acceptance Criteria section.** Model updates are operational SOP work; the completion bar is the Testing Signoff table plus the Verification Surfaces checklist. AC belongs on user stories that consume the new schema, not on the model change itself.
- **One Task per end-to-end model update** — develop branch through Prod, not one ticket per repo. The Affected Code & Loaders table (Section 4) and Testing Signoff table (Section 9) together are the single source of truth for what's been touched and where the change is in the pipeline.
- **Issue type is Task** on this tracker. Do not use Story or Subtask.
- **Parent Epic field set on the ticket itself** via `customfield_12350` when a release-level epic exists.
- **YAML `Version:` field and git tag must match exactly.** Surface this in Section 3 as two distinct rows (Target model version + Target git tag) so the values can be eyeballed before tagging. A mismatch silently fails the downstream sync.
- **Three schema artifacts updated together.** The checklist in Section 3 (Schema artifact checklist) enforces the trio: `ctdc_model_file.yaml`, `ctdc_model_properties_file.yaml`, `version-history.md`. Update one without the others and the Navigator becomes inconsistent.
- **SemVer impact is a required field.** MAJOR / MINOR / PATCH must be classified at ticket creation per the SOP's rules. Downstream consumers plan migration around this classification.
- **Affected Code & Loaders table is mandatory and complete.** All five canonical rows present; the always-required rows checked; the conditional rows (dataloader, frontend) checked or explicitly crossed off. Don't silently omit a row.
- **The model SOP is authoritative for procedure.** This template tracks the change; the SOP at `https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md` describes how to do it. If the SOP changes, this template's references should be re-checked against it.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as every other Jira description on this tracker.

**Writing-and-publishing workflow**

1. Confirm this work is a data model update (a schema change), not a data load or a software-only feature. If only data contents are changing (new study, new files), use the Data Loading Task template (7e). If only application behavior is changing without touching the schema, use the software development template family.
2. Confirm the SemVer classification per the SOP — MAJOR / MINOR / PATCH. This drives whether backward-compatibility planning is needed.
3. Verify the target YAML `Version:` and target git tag values are identical character-for-character. Write them as two separate rows in Section 3's table so the values are easy to compare.
4. Determine which Section 4 rows are affected. The model repo and Navigator are always checked; the dataloader and frontend rows are checked per the actual scope of the change.
5. Create the data model update task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields`. Assign to the TPM (Gina Kuffel) initially — the TPM coordinates the multi-repo work and reassigns per-environment as work progresses.
6. Push the description in two steps: create with the placeholder, then `jira_update_issue` with the full Markdown body. This is the same two-step pattern as the Features, Design Task, and Data Loading templates — `jira_create_issue` renders inconsistently on long Markdown; `jira_update_issue` performs clean Markdown-to-Jira-wiki conversion.
7. Add a "Relates" issue link between the data model update task and: any downstream Data Loading Task (Section 2), any consuming feature ticket, and any related model updates.
8. Verify the rendered description with a UI screenshot — wiki source is unreliable as a render preview.
9. As each environment completes, the assigned tester adds their initials and date to Section 9 (Testing Signoff) and notes results in Section 7 (Per-Environment Verification) directly in the description, or via a comment that the TPM rolls up into the description at end-of-environment.
10. **Prod signoff + downstream sync verification together are the close trigger.** Once Section 9's Prod row is filled in *and* `crdc-datahub-models` `cache/content.json` on `prod` shows the new version, transition the ticket to Closed.

**When to expand vs trim**

- **MINOR change** (new optional property, new optional node, added enum value, new optional relationship) → use the template as written. Backward-compatible; existing data remains valid; usually no migration required.
- **PATCH change** (description fix, examples, metadata text) → use the template but expect Sections 4 (Affected Code & Loaders) and 6 (Verification Surfaces) to be light — only the model repo and Navigator are touched; no loader or frontend changes; verification focuses on the Navigator's display.
- **MAJOR change** (removed required property, type change, removed node, removed enum, tightened constraint) → use the template and expand: Section 3 should explicitly describe what existing data does under the new schema; Section 10 (Open Questions / Risks) must include the backward-compatibility plan; expect a Data Loading Task to follow with re-load of affected studies.
- **Multi-property batched change** (several properties / nodes changing together for one release) → use one ticket per coherent change-set. If the changes are independent and could ship at different times, use one ticket each. If they must ship together as one version bump, one ticket with expanded Section 3 detail.
- **Pure documentation update to `ctdc-model`** (README, owner guide, SOP edits) → this template is overkill. A free-form Task with the doc change description and a single PR link is enough.

**When NOT to use this template**

This template covers data model updates only — schema changes in `CBIIT/ctdc-model`. The following kinds of work are different:

- **Loading data into the existing schema** — new studies, new data added to existing studies, megazip loads. Use the **Data Loading Task** template (Section 7e). That's the sibling template in the data management lane.
- **Software development that does not change the schema** — frontend features, backend refactors, microservice work, bug fixes, library upgrades. Use the software development family: User Story, Features Epic, Application Pages Epic, Microservices Epic, Design Task, Bug.
- **CRDC platform changes** — Fence, IndexD, Submission Portal upgrades owned by CRDC platform teams. Out of CTDC scope.
- **Bento Core MDF framework changes** — changes to the underlying MDF tooling itself (`bento-mdf` submodule). Upstream concern; file with the Bento team.

If a CRDC submission requires schema changes before it can be loaded, that's two pieces of data management work: this template tracks the model update; the Data Loading Task template tracks the load. They depend on each other but live in separate tickets, with the load blocked until the model update has landed and stabilized in each environment.
