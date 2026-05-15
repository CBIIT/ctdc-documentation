### 7e. 📦 Data Loading Task Template (Drafted)

> **Use this template for every CTDC data management task that loads a CRDC submission into CTDC — either a brand-new study or new data added to an existing study — and promotes it through Dev → QA → Stage → Prod.** Canonical example: the CMB v5 megazip metadata load (see CTDC-1753 lineage). This template covers the **loading-data** sub-function of the team's data management work. It is **not** for changes to the data model itself — schema changes (new node types, new properties, renamed relationships, model version upgrades) use the **Data Model Update Task** template (Section 7f), which is the sibling template in the data management lane. See "When NOT to use this template" at the end.

**Why this template**

The CTDC team has two primary functions, and the two have completely separate operational lanes:

- **Software development** — designing, coding, testing, releasing the React frontend, the Java backend, the microservices, and the infrastructure. Verified against application *behavior*. Tracked with the User Story, Features Epic, Application Pages Epic, Microservices Epic, Design Task, and Bug templates.
- **Data management** — managing CRDC submissions, modeling the shape of CTDC's data, and loading data into CTDC's databases. Verified against application *contents* (row counts, page renders, downloadable artifacts) and against schema state (node types, properties, relationships, version numbers).

Data management itself has two sub-functions, and each has its own template:

- **Loading data** — taking a CRDC submission's *contents* (study metadata, files, IndexD entries) and getting them into CTDC's databases. Tracked with **this** template (Section 7e).
- **Modeling data** — changing the *shape* of what CTDC's databases can hold (new node types, new properties, renamed relationships, version bumps). Tracked with the Data Model Update Task template (Section 7f).

Application pages do get updated when new data is loaded — the Programs page, the Study List, the Study Details page, the Explore Dashboard. That is not a sign that the load involves software work or a model change. It's the application working as designed: rendering the new data with unchanged behavior and unchanged schema. The model is stable, the code is unchanged; only the contents shift.

This template owns only the loading-data sub-function. The verification points, signoff cadence, and pipeline choices below are tuned for loading data into a stable schema with unchanged application code.

The most common antipatterns we've seen on data loading tickets:

1. **One ticket per environment.** Splits the audit trail across four tickets and forces a manual reconstruction of "where is this release in the pipeline?" at every standup. A data load is a single end-to-end promotion, not four independent jobs.
2. **No per-environment signoff record.** When QA, Stage, and Prod testers initial their work in Slack threads or comments, there's no consolidated record for compliance review or for the post-load retrospective if something goes wrong in Prod.
3. **Mixed metaphor with software development tickets or model update tickets.** Writing the description as if it's a code deployment ("merge to main, tag the release") or a frontend change ("update the component to render the new field") obscures that this is a *data* promotion — a different operational pathway with different verification points (Memgraph contents, OpenSearch reindex status, Study Details page rendering, megazip download links). If code is changing, this is the wrong template — file software development work in its own ticket family. If the schema is changing, use the Data Model Update Task template (Section 7f) instead.
4. **Ambiguous data payload.** "Load the new metadata" without naming the Submission ID, the SharePoint folder, the AWS bucket, the metadata loading file location, and the IndexD manifest location leaves the data engineer guessing — and a guess on which `file.tsv` to load is exactly the kind of mistake that goes undetected until Prod verification. The Submission & Artifacts table in Section 3 exists specifically to eliminate this ambiguity.

The template below resolves all four with three commitments:

1. **One Task per end-to-end load.** Single source of truth from Dev kickoff through Prod signoff. The Testing Signoff section is the artifact that justifies the one-ticket approach — it consolidates four environment-level handoffs into one auditable table.
2. **Two-tier pipeline grouping.** Dev and QA run on the **Jenkins lower-tiers pipeline**; Stage and Prod run on the **Jenkins upper-tiers pipeline**. That's a real architectural boundary in our deployment infrastructure, not a cosmetic grouping. Surfacing it in the template helps a new data engineer pick the right Jenkins job at each step instead of running the lower-tiers pipeline against Stage.
3. **Explicit payload, explicit verification.** The Submission & Artifacts table in Section 3 names every artifact going into the load — CRDC Submission ID, SharePoint folder, AWS bucket, metadata loading file, IndexD manifest. The Verification Surfaces section names every application page to check after each environment load.

**Pipeline & store anatomy (read this once before drafting)**

A CTDC data load touches multiple systems in sequence. Knowing the chain helps every section of the template land:

- **Source artifacts** — Release package + metadata loading file (`file.tsv` or similar) live in **SharePoint** under the release folder. Object files (BAMs, VCFs, DICOMs, megazips) live in **AWS S3** buckets owned by CRDC. The IndexD manifest (mapping each file's GUID to its bucket location) also lives in SharePoint until it's submitted to CRDC IndexD.
- **Loader repo** — **`CBIIT/crdc-ctdc-dataloader`** (branch `master`). The Python loader code that the Jenkins pipeline runs. Modules include `data_loader.py`, `file_loader.py`, `es_loader.py` (OpenSearch loader), and `loader.py`. For a routine data load, no changes are needed in this repo — the loader code is stable. If loader code *needs* to change (new node type to parse, new validation rule), that's a Data Model Update Task (Section 7f), not this template.
- **Loading pipeline** — **Jenkins**. Two pipelines exist: *lower tiers* (targets Dev and QA) and *upper tiers* (targets Stage and Prod). The pipeline parses the metadata loading file, applies it to the graph, and triggers the reindex.
- **Graph database** — **Memgraph**. This is the canonical metadata store. (Historical tickets and the `crdc-ctdc-starter-kit` README still reference Neo4j — those are outdated; current architecture is Memgraph.) The loading pipeline writes nodes and relationships here. **The model is assumed stable for a data load — if the model is changing, that's a Data Model Update Task and uses the Section 7f template.**
- **Search index** — **OpenSearch**. The frontend's Explore Dashboard, autocomplete, and filters read from OpenSearch, not from Memgraph directly. After every metadata load, OpenSearch must be reindexed from Memgraph before the frontend reflects the change. The Jenkins pipeline handles this — but verification needs to confirm both the Memgraph write **and** the OpenSearch reindex landed.
- **Application surfaces** — Study Details page, Explore Dashboard, Cart, Participant Details, etc. These are the pages a tester loads in their browser to verify the data actually shows up correctly. For megazip loads specifically, the Study Details page download link is the critical verification point.

The template walks all of this in order: payload → pipeline run per environment → verification per environment → signoff per environment.

**Section order (10 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header — same rule as every other CTDC template.

1. `### 🎯 **Load Summary**` — Two to three sentences. What's being loaded, which study or release it belongs to, whether this is a new study or an addition to an existing study, and what application surfaces it lights up. Example: *"Load the Cancer Moonshot Biobank (CMB) v5 release — new data added to the existing CMB study, including the megazipped VCF file (CTDC-1983) — through all four CTDC environments. After load and reindex, the CMB Study Details page should expose the VCF megazip download link and the Explore Dashboard should reflect the updated participant and file counts."*

2. `### 🔗 **Linked Work**` — Bullet list of every related ticket. Required entries:
   - **Parent Epic / Release:** CTDC-XXXX — *Release or feature name* (the release-level ticket if one exists, or the parent epic this data load enables)
   - **Upstream artifact tickets:** CTDC-XXXX — *Megazip / metadata creation tickets* (e.g., the CTDC-1983–1986 family that produced the megazip files this load consumes)
   - **Upstream model update tickets:** CTDC-XXXX — *(if this load depends on a Data Model Update Task that must land first — see Section 7f)*
   - **Downstream verification tickets:** CTDC-XXXX — *(if QA verification or Stage signoff is tracked on its own ticket)*
   - **Related data loads:** CTDC-XXXX — *(if this load supersedes or follows a prior load that needs to be referenced)*

3. `### 📦 **Submission & Artifacts**` — Required field. The single source of truth for *where* every artifact this load consumes lives. Fill in every row at ticket creation; leave PLACEHOLDER values only for items genuinely pending upstream work, and update them as soon as the data engineer or DevOps confirms them. **A reader of this ticket should be able to find every artifact without asking anyone.**

   | Field | Value | Notes |
   |---|---|---|
   | CRDC Submission ID | *(Submission Portal ID — one per submission included in this load)* | Use the ID issued by the CRDC Submission Portal. Multiple IDs allowed if this load consolidates more than one submission. |
   | SharePoint folder | *(SharePoint URL)* | The release folder containing the loading file, IndexD manifest, and any release notes. |
   | AWS bucket name | *(e.g., `nci-crdc-data-bucket-prod`)* | Where the object files (BAMs, VCFs, DICOMs, megazips) live. Include the bucket path prefix if files are scoped to a sub-folder. |
   | Metadata loading file location | *(Full SharePoint path or filename, e.g., `file.tsv`)* | The TSV the Jenkins pipeline consumes. This is the artifact that drives the load. |
   | IndexD manifest location | *(Full SharePoint path or filename, e.g., `indexd.tsv`)* | Maps each file's GUID (e.g., `dg.4DFC/...`) to its bucket location. |
   | Study (acronym + version) | *(e.g., CMB v5)* | Drives the Study Details page verification in Section 5. |
   | New study or addition? | *(New study OR addition to existing study)* | Affects the verification scope — new studies require additional Programs / Study List page checks. |
   | Target model version | *(e.g., `1.21.0`)* | The CTDC model version that this load runs against. Should match the `Version:` field in `model-desc/ctdc_model_file.yaml` on the `prod` branch of `CBIIT/ctdc-model` at the time of load. If the load requires a *newer* model version, that's a Data Model Update Task (Section 7f) that must land first. |

   Pending values: PLACEHOLDER. Confirm with **Charles Ngu** (AWS bucket details), **Patrick Breads** or **Stephanie Singleton** (data engineering — metadata loading file and IndexD manifest), and the CRDC Submission Portal team for Submission IDs.

4. `### 🚦 **Loading Workflow**` — Numbered list of the end-to-end promotion steps. **Two-tier pipeline grouping** is enforced here: Dev + QA on lower tiers, Stage + Prod on upper tiers. Standard CTDC sequence:

   **Pre-load (one-time)**
   1. Locate the release package, metadata loading file, and IndexD manifest in the SharePoint folder named in Section 3. Open the loading file and confirm contents — row count, file references, and GUIDs match the IndexD manifest and the upstream megazip/file creation tickets named in Section 2.
   2. If the load includes a megazip or new object files, confirm those files exist in the AWS bucket named in Section 3 and that each IndexD manifest entry resolves to a real bucket location.
   3. Confirm the target model version named in Section 3 is the version currently deployed in each environment. If a model update is required first, that work belongs in a Data Model Update Task (Section 7f) which must land before this load proceeds.

   **Lower tiers — Dev**
   4. Run the Jenkins **lower-tiers** data loading pipeline targeting Dev with the confirmed metadata loading file.
   5. After pipeline completion, verify in Dev: Memgraph node counts updated, OpenSearch reindex completed, Study Details page renders the new data, no errors in application metrics or page loads. Record results in Section 6 (Per-Environment Verification).

   **Lower tiers — QA**
   6. Run the Jenkins **lower-tiers** data loading pipeline targeting QA with the same metadata loading file.
   7. Assign for QA testing. Tester verifies: data renders on expected pages, megazip download link (if applicable) initiates a successful download of the correct file, no regressions on existing studies. Tester initials Section 8 (Testing Signoff) on completion.

   **Upper tiers — Stage**
   8. Run the Jenkins **upper-tiers** data loading pipeline targeting Stage with the same metadata loading file.
   9. Assign for Stage testing and signoff. Same verification points as QA, plus production-parity sanity checks (Stage typically mirrors Prod configuration). Tester initials Section 8 on completion.

   **Upper tiers — Prod**
   10. Run the Jenkins **upper-tiers** data loading pipeline targeting Prod with the same metadata loading file.
   11. Assign for Prod verification and signoff. Verify in production with the live application URL. Tester initials Section 8 on completion. **This is the trigger to close the ticket.**

5. `### 🧪 **Verification Surfaces**` — Bullet list naming every application surface that must be checked after each environment load. The standard CTDC set; expand based on whether this load is a new study or an addition to an existing study (the "New study or addition?" field in Section 3 drives this):
   - **Memgraph node counts** — confirm expected node and relationship counts post-load (drives the integrity check)
   - **OpenSearch reindex** — confirm reindex completed without error and counts match Memgraph
   - **Study Details page** — for the affected study, confirm new data renders correctly and any megazip download link initiates the right file
   - **Explore Dashboard** — confirm participant, study, and file counts reflect the load
   - **Programs page / Study List** — *(only for new study loads; confirm the new study appears in the global study list and Programs page)*
   - **Participant Details page** — *(only when participant-level metadata is part of the load)*
   - **Cart and File Download** — *(only when new files are part of the load; confirm cart selection and IndexD signed-URL flow still works)*
   - **Application error metrics** — confirm no spike in 4xx / 5xx after load
   - **Logs (CloudWatch or equivalent)** — confirm no exceptions during reindex

6. `### 📊 **Per-Environment Verification**` — Table to record observed Memgraph counts, OpenSearch counts, and any anomalies, per environment. Same row shape across all four environments so anomalies stand out by visual comparison.

   | Environment | Memgraph node count (post-load) | OpenSearch doc count (post-reindex) | Application surfaces verified | Anomalies / notes |
   |---|---|---|---|---|
   | Dev | | | | |
   | QA | | | | |
   | Stage | | | | |
   | Prod | | | | |

7. `### 🤝 **Collaboration & Handoffs**` — Bullet list of the people and touchpoints required for this load. The standard CTDC set:
   - **Data engineering** — owns the load itself (Patrick Breads, Stephanie Singleton)
   - **DevOps** — owns Jenkins pipeline execution and AWS bucket access (Charles Ngu)
   - **QA** — owns environment-level signoff (Valentina Epishina or designated tester)
   - **Backend engineering** — on-call consult if Memgraph / OpenSearch / reindex anomalies appear (Adam Davenport, Eric Miller, Yizhen Chen as tech lead)
   - **TPM** — owns end-to-end coordination and the Testing Signoff record (Gina Kuffel)

8. `### ✅ **Testing Signoff**` — The completion record. Tester fills in date and initials per environment as work progresses. **Prod signoff is the trigger to transition the ticket to Closed.**

   | Environment | Testing Completion Date | Tester Initials |
   |---|---|---|
   | Dev | | |
   | QA | | |
   | Stage | | |
   | Prod | | |

9. `### 🔍 **Open Questions / Risks**` — Bullet list of decisions or unknowns to resolve. Each bullet is one question. Resolve in ticket comments so the audit trail lives with the load. If none at ticket creation, state "None at this time" — items added later become comments, not edits to this section. Common entries on data loads:
   - Are the IndexD entries already created for every file in this load? (If not, blocked until the upstream IndexD ticket lands.)
   - Has the Cloud One bucket policy been refreshed for this submission? (DevOps owns; Charles Ngu confirms.)
   - Does the target model version (Section 3) match what's deployed in each environment? (If not, this load is blocked behind a Data Model Update Task.)
   - Is there a known-good "rollback" plan if Prod load surfaces an issue? (Data loads are append-friendly but version updates can require coordinated re-loads.)

10. `### 📝 **Notes**` — Bullet list. Optional content: prior load lessons learned, links to retrospective notes if a prior load surfaced an issue worth remembering, terminology translations (Bento "Case" → CTDC "Participant"), known constraints. If there's no meaningful note, write "None at this time."

**Standing emoji set (10 entries)**

| Section | Emoji |
|---|---|
| Load Summary | 🎯 |
| Linked Work | 🔗 |
| Submission & Artifacts | 📦 *(unique to data loading task)* |
| Loading Workflow | 🚦 *(shared with data model update task — same operational shape)* |
| Verification Surfaces | 🧪 *(shared with data model update task — same operational shape)* |
| Per-Environment Verification | 📊 *(shared with data model update task — same operational shape)* |
| Collaboration & Handoffs | 🤝 |
| Testing Signoff | ✅ |
| Open Questions / Risks | 🔍 |
| Notes | 📝 |

**Required content rules (Data Loading Task specific — universal Jira rules in 7b-shared also apply)**

- **Scope is loading-data work only.** Loading a CRDC submission into CTDC — either a new study or new data added to an existing study. **Schema or model changes (new node types, new properties, renamed relationships, model version upgrades) do not use this template** — those use the **Data Model Update Task** template (Section 7f), which is the sibling template in the data management lane. **Software development work (frontend, backend, microservices, infrastructure) that does not change the schema or the data being loaded uses the software development template family** — User Story, Features Epic, Application Pages Epic, Microservices Epic, Design Task, or Bug. See "When NOT to use this template" at the end.
- **No Acceptance Criteria section.** Data loading is operational SOP work; the completion bar is the Testing Signoff table plus the Verification Surfaces checklist. AC belongs on user stories in the software development lane, not on the load itself.
- **One Task per end-to-end load** — Dev through Prod, not one ticket per environment. The Testing Signoff table is the single source of truth for where the load is in the pipeline.
- **Issue type is Task** on this tracker, matching the convention used on the megazip family (CTDC-1983 etc.). Do not use Story or Subtask.
- **Parent Epic field set on the ticket itself** via `customfield_12350` when a release-level epic exists. This makes the data load discoverable from the epic's child issues panel.
- **Two-tier Jenkins pipeline distinction must be explicit** in the Loading Workflow section. Dev + QA → lower tiers; Stage + Prod → upper tiers. This is not stylistic — running the wrong pipeline at the wrong tier is a real failure mode.
- **Memgraph and OpenSearch both named in Verification Surfaces.** A successful Memgraph write without an OpenSearch reindex means the frontend won't show the new data; a successful reindex against an incomplete Memgraph write means stale or partial results. Both surfaces are required checks.
- **Submission & Artifacts table is mandatory and complete.** All required anchor fields (CRDC Submission ID, SharePoint folder, AWS bucket name, metadata loading file location, IndexD manifest location, target model version) must be present. Use PLACEHOLDER explicitly when a value is pending upstream — never silently omit a row.
- **Charles Ngu is named in Collaboration & Handoffs** for DevOps / Jenkins / AWS bucket questions. Same for the data engineering pair (Patrick Breads, Stephanie Singleton).
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as every other Jira description on this tracker.

**Writing-and-publishing workflow**

1. Confirm the upstream data artifacts exist before drafting the load ticket. If the megazip file hasn't been created yet, or the metadata loading file isn't in SharePoint yet, or the IndexD entries aren't registered, the load ticket is premature — those upstream tickets should land first and be linked from Section 2 (Linked Work).
2. Confirm this work is a data load, not a data model update or software development. If the schema is changing (new node types, new properties, renamed relationships, version bump), use the Data Model Update Task template (Section 7f) instead. If code is changing in the frontend, backend, or microservices without a schema change, use the software development ticket family (User Story, Features Epic, Application Pages Epic, etc.) — this template does not cover those cases.
3. Confirm the target model version (Section 3) matches what's deployed in each environment. If the load needs a newer model version, file a Data Model Update Task first and link it from Section 2.
4. Create the data loading task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields`. Assign to the TPM (Gina Kuffel) initially — the TPM coordinates the load and reassigns per-environment as work progresses.
5. Push the description in two steps: create with the placeholder, then `jira_update_issue` with the full Markdown body. This is the same two-step pattern as the Features and Design Task templates — `jira_create_issue` renders inconsistently on long Markdown; `jira_update_issue` performs clean Markdown-to-Jira-wiki conversion.
6. Add a "Relates" issue link between the data loading task and every upstream artifact ticket (megazip creation, IndexD registration, etc.) named in Section 2. If a Data Model Update Task is a prerequisite, add a "Relates" link to that too.
7. Verify the rendered description with a UI screenshot — wiki source is unreliable as a render preview (per 7b-shared).
8. As each environment completes, the assigned tester adds their initials and date to Section 8 (Testing Signoff) and notes counts in Section 6 (Per-Environment Verification) directly in the description, or via a comment that the TPM rolls up into the description at end-of-environment.
9. **Prod signoff is the close trigger.** Once Section 8's Prod row is filled in, transition the ticket to Closed. The completed ticket is now the canonical record of this data promotion.

**When to expand vs trim**

- **New study load** → use the template as written. Section 3 captures every artifact; Section 5 includes the Programs / Study List page check.
- **Addition to existing study** (e.g., CMB v5 megazip pattern) → use the template as written. This is the canonical case it was designed for. Skip the Programs / Study List bullet in Section 5 unless the study itself wasn't previously visible there.
- **Multi-study omnibus load** (multiple submissions consolidated into one Jenkins run) → expand Section 3 (Submission & Artifacts) to a table per submission, or add submission-ID rows; expand Section 6 (Per-Environment Verification) to a row per environment-per-study or use a per-study sub-table. Keep one ticket — the Jenkins run is one unit of work.
- **Re-load after data correction** (same submission, corrected file) → use the template as written, and use Section 2 (Linked Work) → "Related data loads" to point at the original (now-superseded) load ticket. Section 10 (Notes) should explain *why* the re-load is needed.
- **Single-file hot-patch** (one corrected file, no metadata change) → this template is overkill. A free-form Task with a one-line description and a single-row signoff table is enough. But if the patch crosses environments, still use the four-row Testing Signoff table to keep the audit trail consistent.

**When NOT to use this template**

The CTDC team has two primary functions: software development and data management. Data management has two sub-functions: loading data (this template) and modeling data (the Data Model Update Task template). This template covers the loading-data sub-function only.

**Data model updates** — does NOT use this template. Use the **Data Model Update Task** template (Section 7f) instead. That sibling template is the home for:

- **Schema / data model changes** — adding new node types, new properties, renamed relationships, changed cardinalities, model version upgrades. Tracked in `CBIIT/ctdc-model`. Touches the loader code in `CBIIT/crdc-ctdc-dataloader` and frontend rendering when new fields need to be displayed.

**Software development work** — does NOT use this template. Use the appropriate template from the software development family instead:

- **Frontend, backend, or microservices code changes** — new features, refactors, bug fixes, library upgrades that do not change the schema. Use **User Story** (for a single user-facing change), **Features Epic** (for a multi-story feature like Local Find), **Application Pages Epic** (for a whole-page redesign like Participant Details), **Microservices Epic**, **Design Task** (for visual design work), or **Bug** (for defects).
- **Infrastructure / DevOps changes** — Jenkins pipeline updates, deployment configuration, environment provisioning. Use the relevant Infrastructure or DevOps ticket pattern.

**Other work that is none of the above:**

- **CRDC platform changes** — Fence, IndexD, Submission Portal upgrades owned by CRDC platform teams. Out of CTDC scope entirely; CTDC files dependency tickets if affected, but does not own the work.
- **Pure file-creation tickets** — making a megazip, generating an IndexD manifest, registering GUIDs in IndexD. These are the *upstream artifact creation* tickets (e.g., CTDC-1983–1986) that feed *this* template, not the load itself. They're data-adjacent operational tasks tracked separately.

If a CRDC submission requires schema changes before it can be loaded, that's two pieces of data management work in two sub-functions: the schema change is a Data Model Update Task (Section 7f), and the load is a Data Loading Task that uses this template. They depend on each other but are tracked separately, with the load blocked until the model update has landed and stabilized in each environment.
