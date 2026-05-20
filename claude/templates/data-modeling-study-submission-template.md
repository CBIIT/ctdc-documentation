### 7g. 🧬 Data Modeling for Study Submission Template (Drafted)

> **Use this template for every CTDC data modeling task driven by a study submission** — whether the study is brand-new to CTDC or an existing study requesting new properties, permissible values, or enums. Canonical example: **[CTDC-1799](https://tracker.nci.nih.gov/browse/CTDC-1799)** (Update data model for CIDC-CIMAC study submission). This template covers the **modeling-data** sub-function of data management when the work is anchored on a study's **CDE Request Workbook**. It is **not** for infrastructure-level model changes (breaking changes, model framework upgrades, coordinated multi-repo refactors) — those use the Data Model Update Task template (Section 7f). It is **not** for loading the study's data after the model is in place — that's a Data Loading Task (Section 7e).

**Why this template**

The CTDC team's data management function has two sub-functions. Modeling data has **two distinct work patterns**, and each has its own template:

- **Study-driven model additions** — a study submits its data needs via a CDE Request Workbook; CTDC adds the requested properties, enums, and (occasionally) nodes to the model. Almost always **MINOR**-version additions: backward-compatible, no code changes outside `ctdc-model`, no application regression. Tracked with **this** template (Section 7g).
- **Infrastructure-level model changes** — breaking changes, framework upgrades, cross-cutting refactors initiated by the data team itself. Touches loader code in `crdc-ctdc-dataloader` and/or frontend rendering. Rare. Tracked with the Data Model Update Task template (Section 7f).

The vast majority of CTDC modeling work is the first pattern: a study comes to us with their data, we look at their CDE Request Workbook, we agree what to bring into the model, we add it, we tag and release, the CRDC Submission Portal picks up the new model, the study can submit. CTDC-1799 is the canonical shape: 76 terms in the workbook, additions to `ctdc-model`, iterative resolution of missing CDE codes with the SI team, eventually promoted to `prod` and verified in the Submission Portal.

This template owns that work pattern end-to-end. The verification points, signoff cadence, and external dependencies below are tuned for **study-driven** model additions specifically.

The most common antipatterns we've seen on study-modeling tickets:

1. **No anchor on the CDE Request Workbook.** Without a single named workbook URL, the ticket becomes a free-form discussion of "what did we agree to add?" and the conversation lives across Slack threads, working-meeting notes, and email. The workbook is the single source of truth for *what* terms are in scope, *which* CDE codes are bound to them, and *which* status each term is in. Surface it at the top of the ticket and refer to it for every term-level question.
2. **Replicating the workbook's term inventory into Jira.** Some workbooks have 70+ terms. Pasting them into the ticket description creates a stale snapshot that the team has to maintain in two places. Don't. Reference the workbook and capture *summary counts* (total terms, count with CDE codes, count blocked on SI) at the ticket level.
3. **Treating SI / caDSR dependencies as internal blockers.** Missing CDE codes wait on the SI team — an external dependency. The ticket should make this visible: which terms are blocked on SI? when was the request sent? what's the SLA? Hiding external blockers as "in progress" gives a false sense of velocity.
4. **Conflating modeling with loading.** Adding the property `assay_subtype` to the model is *not* the same as loading the CIMAC-CIDC study's actual `assay_subtype` data into CTDC. The first is a modeling ticket (this template); the second is a data loading ticket (Section 7e) that runs *after* the modeling work lands. Keep them separate so the close criteria for each is unambiguous.
5. **No per-stage signoff record.** The promotion ladder for a modeling ticket is short — `develop` PR merged, `prod` PR merged, tag cut, Submission Portal verified — but it still benefits from a one-row-per-stage signoff table for an auditable trail.

The template below resolves all five with three commitments:

1. **One Task per study's modeling work.** Single source of truth from initial workbook review through Submission Portal verification. One study, one ticket, one workbook. Future additions to the same study get their own ticket.
2. **CDE Request Workbook is the term-level source of truth.** The ticket references it; the workbook owns the inventory and status of every term. The ticket captures summary counts and stage-level signoff.
3. **Branch-and-release ladder, not environment ladder.** Study modeling promotes through `develop` → `prod` in `ctdc-model`, then tag → release → Submission Portal sync. There is no Dev → QA → Stage → Prod ladder for the modeling work itself (that comes later when the *data* gets loaded). Don't bolt a four-environment table onto a two-stage promotion.

**Workbook, branch, & store anatomy (read this once before drafting)**

A study-modeling ticket touches a smaller set of systems than a full Data Model Update Task. Knowing the chain helps every section land:

- **CDE Request Workbook** — a SharePoint-hosted Excel workbook authored by the CTDC team with input from the study team. Each study has its own workbook (CIDC-CIMAC has [`CTDC-CIMAC-CIDC_CDE_Request_Workbook.xlsx`](https://nih.sharepoint.com/:x:/r/sites/NCI-CBIIT-FNL-CTDC-CTDCProjectManagement/Shared%20Documents/CTDC%20Data/CTDC%20Studies/IODH-CIMAC-CIDC/Data/CTDC-CIMAC-CIDC_CDE_Request_Workbook.xlsx) for example). The workbook lists every property, node, and permissible value the study needs, with caDSR Common Data Element bindings and a Status column tracking propagation.
- **Upstream artifacts from the study team** — typically a Gap Analysis spreadsheet and an Appendix A enumerating the study's data shape (CIDC-CIMAC's are linked from the ticket). These inform the workbook but are not its substitute.
- **Source-of-truth repo** — **`CBIIT/ctdc-model`**. Two branches matter: `develop` (where the work lands first) and `prod` (where it's tagged and released from). The branching is the same as 7f; the operational ladder is shorter because no application environments are involved at this stage.
- **Schema artifacts** — the same three files in `ctdc-model` that any model change touches:
  - **`model-desc/ctdc_model_file.yaml`** — node definitions, relationships, and the top-level `Version:` field
  - **`model-desc/ctdc_model_properties_file.yaml`** — property definitions including caDSR CDE bindings
  - **`model-navigator-resources/version-history.md`** — release notes for the Data Model Navigator
- **External dependency** — the **SI / caDSR team** owns Common Data Element creation. Terms without CDE codes wait on this team; their work is outside CTDC's direct control and needs explicit tracking.
- **Downstream consumer** — the **CRDC Submission Portal**. After the new model version is tagged on the `prod` branch, GitHub Actions sync the model into the Submission Portal (via `CBIIT/crdc-datahub-models` `cache/content.json`). The Submission Portal then exposes the new properties to the study team for their submission. **This is the close trigger for the modeling ticket.**
- **Data Model Navigator** — the user-facing model view. Reflects the new version once the tag is cut; useful for verification but not the close trigger.

The CTDC application itself is **not** in the verification surface for this ticket. The application picks up new properties / nodes when the study's data gets loaded into Memgraph and reindexed into OpenSearch — and the load is a separate ticket (7e) that comes after this one lands.

The template walks all of this in order: study identity → workbook & term counts → modeling workflow → external dependency tracking → branch-and-release signoff.

**Section order (10 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header — same rule as every other CTDC template.

1. `### 🎯 **Modeling Summary**` — Two to three sentences. Which study is driving this modeling work, whether it's a new study coming to CTDC or an addition to an existing study, and what the modeling work enables. Example: *"Update the CTDC data model to support the upcoming IODH-CIMAC-CIDC study submission. This is a new-study onboarding with 76 terms requested via the CDE Request Workbook — adding new properties to existing nodes, with no new node types. Once the model is in Prod and the Submission Portal picks up the new version, the CIMAC-CIDC team can submit their data through the standard CRDC submission flow."*

2. `### 🔗 **Linked Work**` — Bullet list of every related ticket. Required entries:
   - **Parent Epic / Release:** CTDC-XXXX — *Study onboarding epic or release name* (the release-level ticket if one exists)
   - **Downstream data load tickets:** CTDC-XXXX — *(the Data Loading Task that will follow once the model lands — see Section 7e)*
   - **Related modeling tickets:** CTDC-XXXX — *(if this study has had prior modeling tickets, or if multiple studies are being onboarded in parallel and sharing terms)*
   - **GitHub PR(s):** `CBIIT/ctdc-model#PR_NUM` — *(the PR into `develop`, and the later PR from `develop` → `prod`; add as comments on the ticket as they're opened)*

3. `### 🧬 **Study & CDE Workbook**` — Required field. The single source of truth pointer. The workbook itself lives in SharePoint and owns the term inventory; this section anchors the ticket to it.

   | Field | Value | Notes |
   |---|---|---|
   | Study (acronym + sponsor) | *(e.g., IODH-CIMAC-CIDC)* | The study driving this modeling work |
   | New study or existing? | *(New study OR addition to existing study)* | New studies typically have larger workbooks; additions are usually smaller targeted changes |
   | CDE Request Workbook URL | *(SharePoint URL)* | **The authoritative source for what's being added.** Term inventory, CDE codes, and Status column live here — not in this ticket |
   | Study Gap Analysis URL | *(SharePoint URL)* | The study team's analysis identifying what they need that CTDC doesn't yet have |
   | Study Appendix A / data dictionary URL | *(SharePoint URL — if applicable)* | The study's own data dictionary, if it has one; otherwise N/A |
   | Total terms in workbook | *(Count — updated as workbook evolves)* | Headline number; drives Section 4 progress tracking |
   | Target model version | *(e.g., `1.21.0`)* | What this modeling work will produce. SemVer typically **MINOR** for property/enum additions; **PATCH** for description-only edits |
   | Current model version | *(e.g., `1.20.3`)* | From `model-desc/ctdc_model_file.yaml` `Version:` field on `prod` today |

   Reference: the canonical procedure for editing, branching, tagging, and downstream-syncing model changes lives at **[`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md)** on the `prod` branch of `ctdc-model`. This ticket tracks the change; the SOP describes the procedure.

4. `### 📋 **Term Status Summary**` — Required field. Headline counts only — the workbook owns the detailed inventory. Update this section as work progresses; the values change over the life of the ticket.

   | Term Status | Count | Notes |
   |---|---|---|
   | Total terms in workbook | *(N)* | Sum of all terms requested by the study, whether resolved or pending |
   | Terms added to `ctdc-model` (`develop` branch) | *(N)* | Terms that have been written into the YAML files on the feature branch / `develop` |
   | Terms with CDE codes bound | *(N)* | Terms whose caDSR Common Data Element bindings are complete in the workbook and YAML |
   | Terms missing CDE codes (blocked on SI / caDSR) | *(N)* | Terms waiting on the SI team to create a CDE; tracked in Section 7 as external dependency |
   | Terms with ENUM values but no CDE | *(N)* | A specific subset of the missing-CDE bucket worth tracking — the enum values are defined, the CDE binding is missing |
   | Terms confirmed not needed | *(N)* | Terms originally in the workbook that the team agreed to drop (CIDC-CIMAC dropped `study_status`, for example) |
   | Terms promoted to `prod` | *(N)* | Terms in the merged-to-prod branch; populates as the prod PR lands |

   **Maintain the workbook's Status column** as the per-term source of truth; surface the counts here for ticket-level visibility. Common workflow during the ticket: data team adds terms to `develop`, updates the workbook's Status column per term, refreshes these counts in the ticket.

5. `### 🚦 **Modeling & Promotion Workflow**` — Numbered list of the end-to-end workflow. The promotion ladder is `develop` → `prod` in `ctdc-model`, then tag and release, then Submission Portal verification. Two stages, not four environments.

   **Pre-work**
   1. Receive the study's Gap Analysis and Appendix A (or equivalent data dictionary).
   2. Author the CDE Request Workbook for the study in SharePoint — list every term the study needs, with caDSR CDE bindings where known and a Status column for tracking.
   3. Working meeting with the study team and CTDC data team to agree on what comes into the CTDC model. Update the workbook accordingly (add terms; mark dropped terms; flag terms that need SI / caDSR action).

   **`develop` branch — model additions**
   4. Create feature branch `CTDC-XXXX` off `develop` in `ctdc-model`. Edit the three schema artifacts together: add property definitions to `ctdc_model_properties_file.yaml`, add property references to the relevant node's `Props:` list in `ctdc_model_file.yaml`, add a release notes entry to `version-history.md`. Bump the `Version:` field per SemVer.
   5. Commit with message `CTDC-XXXX: <change summary>`. Push and open a PR into `develop`. CI must pass; assign reviewer.
   6. Update the workbook's Status column per term as each lands on the branch.
   7. After review and merge to `develop`, refresh the counts in Section 4 above.

   **Resolve external dependencies**
   8. For terms still missing CDE codes, send the request to the SI / caDSR team. Track the request in Section 7 (Collaboration & External Dependencies). The terms can land on `develop` without CDE bindings; the bindings get added when SI responds. Do not block the promotion to `prod` on completion of every CDE binding unless the study team has explicitly required it.

   **`develop` → `prod` promotion**
   9. Open a PR from `develop` → `prod` in `ctdc-model`. CI must pass; assign reviewer. After merge, create a git tag matching the YAML `Version:` field exactly (e.g., `1.21.0`), and publish a GitHub Release with release notes summarizing what was added for this study.
   10. **Verify downstream sync**: GitHub Actions update `CBIIT/crdc-datahub-models`. Inspect `cache/content.json` on the `prod` branch of that repo — the CTDC entry should reflect the new version number.
   11. **Verify the CRDC Submission Portal picks up the new model**: navigate to the Submission Portal, confirm the new properties and enums appear in the data model view, and confirm the study team can target the new properties in a draft submission. **This is the close trigger.**

6. `### 🧪 **Verification Surfaces**` — Bullet list naming every surface that must be checked after the modeling work lands. Focus is on **the Submission Portal**, not the CTDC application — the application isn't affected until data is loaded (which is a separate ticket).
   - **`ctdc-model` `prod` branch** — confirm the merge landed; the YAML `Version:` field matches the git tag
   - **GitHub Release** — confirm the release was published with release notes; tag matches the YAML version exactly (character-for-character)
   - **Downstream `crdc-datahub-models`** — confirm `cache/content.json` on `prod` reflects the new CTDC version (this is the GitHub Actions sync target)
   - **CRDC Submission Portal** — confirm the new properties / enums are available in the Submission Portal's data model view; this is what the study team uses to prepare their submission
   - **Data Model Navigator** — confirm the new version appears in the Version tab and any new properties / enums are visible
   - **CDE Request Workbook** — confirm the Status column is fully updated per term: every term either marked complete, dropped, or explicitly waiting on SI

7. `### 🤝 **Collaboration & External Dependencies**` — Bullet list of the people and external teams required for this work. The standard CTDC set, plus the external dependencies that are first-class for study modeling.

   **Internal CTDC**
   - **Data engineering / model owner** — owns the schema edits to `ctdc-model` (Patrick Breads, Stephanie Singleton)
   - **Reviewer** — reviews the `develop` and `prod` PRs; typically Gina Kuffel or Stephanie Singleton (not the author of the PR)
   - **TPM** — owns end-to-end coordination, workbook hygiene, external dependency tracking, and ticket-level status (Gina Kuffel)

   **External**
   - **Study team** — owns the Gap Analysis and Appendix A; agrees on what comes into the CTDC model in the working meetings; eventually uses the Submission Portal to submit data (CIDC-CIMAC contact: *(add per study)*)
   - **SI / caDSR team** — owns Common Data Element creation for terms missing CDE codes. Track outstanding requests in this section as bullets: *"Requested CDE for `assay_subtype` on YYYY-MM-DD; SLA: N business days; status: pending."* When SI responds, update the workbook and refresh Section 4 counts.

   **External dependency log** (add a line per outstanding request):
   - *(Term name)* — *(date requested)* — *(status: pending / received / N/A)*

8. `### ✅ **Branch & Release Signoff**` — The completion record. This replaces the four-row Dev/QA/Stage/Prod signoff used by 7e and 7f because the modeling ticket's operational ladder is a branch-and-release ladder, not an environment ladder. Tester / reviewer fills in date and initials per stage as work progresses.

   | Stage | Completion Date | Reviewer Initials | Notes |
   |---|---|---|---|
   | Feature branch PR opened into `develop` | | | Link the PR in this row's notes |
   | `develop` PR merged | | | Author can self-fill this row on merge |
   | `develop` → `prod` PR opened | | | Link the second PR |
   | `prod` PR merged | | | |
   | Git tag created (matches YAML `Version:`) | | | Tag value here for the audit trail |
   | GitHub Release published | | | |
   | Downstream `crdc-datahub-models` `cache/content.json` shows new version | | | |
   | **CRDC Submission Portal shows new properties / enums** | | | **This is the close trigger** |

9. `### 🔍 **Open Questions / Risks**` — Bullet list of decisions or unknowns to resolve. Each bullet is one question. Resolve in ticket comments so the audit trail lives with the modeling work. If none at ticket creation, state "None at this time." Common entries on study-modeling tickets:
   - Are all required CDE codes available, or are some blocked on SI / caDSR? (Drives whether `prod` promotion happens before or after SI's work lands.)
   - Are any of the requested terms already in the CTDC model under a different name? (Sometimes the study team requests a term that exists; alignment with the existing term is preferable to creating a duplicate.)
   - Does the workbook have an Appendix A entry for every term, or are some terms ambiguous? (Drives whether a second working meeting is needed.)
   - Will this modeling work require a data re-load for any existing study? (Almost always no for additive MINOR changes; flag if any term has a different shape than expected.)
   - Is the target model version coordinated with any other in-flight modeling tickets? (If two studies are being onboarded in parallel, version bumps need to be sequenced.)

10. `### 📝 **Notes**` — Bullet list. Optional content: links to working-meeting notes, prior modeling tickets for the same study, terminology translations between the study team's vocabulary and CTDC's (Bento "Case" → CTDC "Participant" is the classic), known constraints, lessons learned. If there's no meaningful note, write "None at this time."

**Standing emoji set (10 entries)**

| Section | Emoji |
|---|---|
| Modeling Summary | 🎯 |
| Linked Work | 🔗 |
| Study & CDE Workbook | 🧬 *(unique to study-modeling task)* |
| Term Status Summary | 📋 *(unique to study-modeling task)* |
| Modeling & Promotion Workflow | 🚦 *(shared with data loading and data model update tasks — same operational shape)* |
| Verification Surfaces | 🧪 *(shared shape; content focused on Submission Portal)* |
| Collaboration & External Dependencies | 🤝 |
| Branch & Release Signoff | ✅ *(unique signoff shape — not four environments; eight stages of branch-and-release)* |
| Open Questions / Risks | 🔍 |
| Notes | 📝 |

**Required content rules (Data Modeling for Study Submission specific — universal Jira rules in 7b-shared also apply)**

- **Scope is study-driven model additions only.** A study submission's CDE Request Workbook drives the work. Infrastructure-level model changes (breaking, framework upgrades, cross-cutting refactors) use the Data Model Update Task template (Section 7f). Data loading for the study after the model lands uses the Data Loading Task template (Section 7e).
- **No Acceptance Criteria section.** Study modeling is operational SOP work; the completion bar is the Branch & Release Signoff table and the Submission Portal verification. The study team's AC for their submission is on their submission ticket, not on the modeling ticket.
- **One Task per study's modeling work.** New study onboarding gets one ticket. Subsequent additions for the same study get their own ticket. Don't roll forward into a single open ticket.
- **Issue type is Task** on this tracker, matching CTDC-1799's convention.
- **Parent Epic field set on the ticket itself** via `customfield_12350` when a study onboarding or release epic exists.
- **The CDE Request Workbook is the term-level source of truth, not this ticket.** Section 3 anchors the URL; Section 4 captures summary counts only. Do not replicate the workbook's term inventory in this ticket — the workbook can have 70+ rows and maintaining two copies leads to drift.
- **YAML `Version:` field and git tag must match exactly.** Surface this in Section 8 (Branch & Release Signoff) as a recorded value, and verify before tagging. The downstream Data Hub reads the version from the YAML, not the tag — a mismatch silently fails the Submission Portal sync.
- **External dependencies named explicitly.** Terms blocked on SI / caDSR are tracked in Section 7 (Collaboration & External Dependencies). Don't bury "waiting on CDE codes" inside ticket comments — surface it in the structured section so it's visible at a glance.
- **The Submission Portal is the close trigger.** Not `prod` merge, not the GitHub Release, not the `crdc-datahub-models` sync — the actual verification that the Submission Portal displays the new model is what closes the ticket. The earlier stages are necessary but not sufficient.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as every other Jira description on this tracker.

**Writing-and-publishing workflow**

1. Confirm a CDE Request Workbook exists or is in active authoring for the study. If it doesn't exist yet, the modeling ticket is premature — author the workbook first (it's a SharePoint Excel file, owned by the data team, populated from the study's Gap Analysis).
2. Confirm this work is study-driven (anchored on a CDE Request Workbook) and not an infrastructure-level model change. If the scope is breaking schema changes, framework upgrades, or coordinated multi-repo refactors, use the Data Model Update Task template (Section 7f) instead.
3. Confirm this work is *modeling*, not *loading*. Adding the property `assay_subtype` to the model is modeling (this ticket). Loading the CIMAC-CIDC `assay_subtype` data into CTDC is a separate Data Loading Task (Section 7e) that comes later.
4. Create the data modeling task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields`. Assign to the TPM (Gina Kuffel) initially — the TPM coordinates the working meetings and external dependencies and reassigns to the data engineer for the schema edits.
5. Push the description in two steps: create with the placeholder, then `jira_update_issue` with the full Markdown body. Same two-step pattern as every other CTDC template.
6. Add a "Relates" issue link between the modeling task and: the future Data Loading Task for this study (or a placeholder note if it hasn't been created yet), any prior modeling tickets for the same study, and any parallel modeling tickets for studies being onboarded at the same time.
7. Verify the rendered description with a UI screenshot — wiki source is unreliable as a render preview (per 7b-shared).
8. As work progresses, the data engineer adds GitHub PR links as Jira comments and updates Section 4 (Term Status Summary) counts and Section 8 (Branch & Release Signoff) rows directly in the description, or via comments that the TPM rolls up at stage boundaries.
9. **Submission Portal verification + tag-and-release together are the close trigger.** Once Section 8's last row is filled in (Submission Portal shows new properties / enums) and the workbook's Status column is fully reconciled, transition the ticket to Closed.

**When to expand vs trim**

- **New study onboarding with a large workbook** (50+ terms) → use the template as written. Section 4's term counts make scale legible; Section 7's external dependency log accommodates multiple SI requests.
- **Existing study, small addition** (a handful of new properties or enums) → use the template; Section 4 will be small. The Branch & Release Signoff in Section 8 still applies — the promotion path is the same regardless of scope.
- **New study with no missing CDE codes** (rare but possible — all bindings ready) → use the template; Section 4's "Terms missing CDE codes" row is 0, Section 7's external dependency log is "None at this time."
- **Workbook still in early authoring** (study team and CTDC haven't agreed on scope yet) → file the ticket as a draft with Section 4 counts as PLACEHOLDER. The workbook reaches stability in the working meetings; the ticket is the operational tracker once scope is set.
- **A single property addition driven internally by the data team, not a study** → this template is the wrong fit. That's a Data Model Update Task (Section 7f) — internal-driven, smaller scope, no CDE Workbook anchor.

**When NOT to use this template**

This template covers study-driven model additions only. The following kinds of work are different:

- **Infrastructure-level model changes** — breaking changes, framework upgrades, coordinated multi-repo refactors initiated by the CTDC data team (not a study). Use the **Data Model Update Task** template (Section 7f). That template has the heavier machinery for code coordination across `crdc-ctdc-dataloader` and frontend repos.
- **Loading study data after the model lands** — once this modeling ticket closes, the study can submit, and the load of their data into CTDC's databases is a **Data Loading Task** (Section 7e). Different verification surface (CTDC application, not Submission Portal), different ladder (Dev → QA → Stage → Prod application environments).
- **Software development that does not touch the schema** — frontend features, backend refactors, microservices, bug fixes. Use the software development template family (User Story, Features Epic, Application Pages Epic, Microservices Epic, Design Task, Bug).
- **CDE Workbook authoring as standalone work** — the workbook itself is a SharePoint artifact owned by the data team. If a study needs a workbook authored before this ticket can be created, that authoring is data-adjacent operational work and can be a free-form Task or a checklist item on the study onboarding epic — not its own structured ticket.
- **Bento Core MDF framework changes** — changes to the underlying MDF tooling itself (`bento-mdf` submodule). Upstream concern; file with the Bento team.

If a study submission requires schema changes that go beyond MINOR additions (e.g., the study needs a fundamentally new node type with breaking implications for existing data), that's two pieces of data management work: a Data Model Update Task (Section 7f) for the breaking schema work, followed by a Data Modeling for Study Submission task (this template) for the study-specific additions on top. They depend on each other but are tracked separately, with the study modeling work blocked until the infrastructure-level model change has landed and stabilized.
