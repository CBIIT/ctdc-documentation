<!-- Extracted from claude/SKILL.md on 2026-11-02 so CTDC packages its templates the same way ICDC does. This is the canonical home for this content. -->

## 7b. 🏛️ Epic Template (Lean v2)

> **Use this template for every CTDC epic**, whatever its grouping (application page, microservice, feature, product, infrastructure, security), except the two standing data modeling epics, which use the data variant in 7b-D, and data submission epics, which use the Data Submission Epic template (DO-EPIC). The canonical example is **CTDC-1802 (CTDC Export to the Cancer Genomics Cloud)**, rewritten to this shape and Closed on 2026-09-14.
>
> **v2.1 (2026-10-13): section 4 is the Definition of Done, not Acceptance Criteria.** Acceptance Criteria are testable conditions on a user story; an epic is not tested, it closes when its children are done. Section 4 now states three to five closure conditions and no longer carries a quality-bar item (that bar lives on every child story). Applies to 7b-D and the Data Submission Epic (DO-EPIC) as well. ICDC adopted the identical shape the same day.
>
> **v2 (2026-09-14): one five-section template replaces the per-grouping templates.** The v1 templates (7b-1 Application Pages at 15 sections, 7b-2 Microservices at 20, 7b-3 Features at 18, and the 7b-4 through 7b-7 stubs) produced 3,000-word epic bodies that nobody read in Jira. Per Gina's direction the standard is now what the Agile Alliance actually prescribes: an epic is "a large user story that cannot be delivered as defined within a single iteration," and "there is no standard form to represent epics." CTDC's form is a goal-and-objectives statement, not the As a / I want / So that story form, which is reserved for the child stories. Everything the v1 templates carried beyond that (Upstream Provenance, Surface Area, Stakeholders, Key Definitions, Composition, Dependencies, Assumptions, Constraints, Risks, User Impact, Components Breakdown, Documentation & Compliance) now lives in one of three places: a child story, a Jira comment, or the leadership `.docx` epic summary (Section 5). The seven groupings survive only as a classification aid for deciding what to verify before drafting (see 7b-shared, "Verification & ground truth"); they no longer select a template.

**Why this template**

An epic is a container over its child stories, not a design document. The body needs to do four things: state the goal, objectives, and why, describe the mechanism in one breath, draw the scope line, and state how we know it is done. Anything longer either duplicates the children or goes stale. The target is **under 600 words**; if content does not fit, it belongs in a child story, a comment, or the epic summary `.docx`.

**Card line + section order (card line, then 5 sections, exactly this sequence)**

Each section header is an `h3.` Jira wiki heading using the emoji + bold title format shown (`h3. 🎯 *Epic Statement*`; see "Authoring format" in 7b-shared). Do not omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header.

0. **Card line** (added 2026-09-24): the first line of the description, with no header. One plain sentence, 140 characters or fewer, saying what the epic delivers in terms Federal Leadership would use. No markup, emoji, ticket keys, or heading; follow it with one blank line, then the 🎯 heading. It is what the Federal Leadership epic Kanban board shows on each card (Board settings, Card layout: Description shows the first line). Example (CTDC-1926): *"Lets users download all files of one type for a study as a single zip, built on request instead of stored in the cloud."* When the admins add the existing Business Value text field (`customfield_10502`) to the CTDC and ICDC epic screens, the same sentence moves into that field and the card layout switches to it.
1. `h3. 🎯 *Epic Statement*`: One paragraph stating the **goal**, the **objectives**, and **why** the work is being done. Not a user story: the As a / I want / So that form belongs to the child stories, where one user and one behavior are in view. The epic statement is the umbrella over those stories: the outcome the team is delivering, the two to four objectives that define it, and the problem or value that justifies it. Example (CTDC-1802): *"Give CTDC researchers a one-click path from the files they have gathered in the Cart to a project on the Seven Bridges Cancer Genomics Cloud, so that CTDC data can be analyzed in the cloud without being downloaded or manually re-uploaded. Objectives: an export that needs no manual manifest handling; a manifest CGC can import as-is with CTDC clinical metadata attached; file access still governed by the researcher's own dbGaP authorization; identical behavior on every tier. Why: finding files is only half of FAIR. Without a direct hand-off to a CRDC Cloud Resource, researchers had to download a manifest, log in to CGC separately, and upload it by hand."*
2. `h3. 🧬 *Description*`: Two short paragraphs. (a) How it works: the surface(s) it lives on with the route, the services and data stores involved (Memgraph, never Neo4j; OpenSearch when relevant), and where authorization is enforced. (b) What this epic's phase delivers (design, frontend, backend, DevOps, defect fixes) and the release it shipped in or targets. Ground (a) in the canonical repo and the live UI, not in ticket titles.
3. `h3. 🗺️ *Scope*`: Two sub-blocks, **In Scope** and **Out of Scope**, about five bullets each. Out of Scope names adjacent or excluded work in prose (never by ticket key) and ends with the bullet that future enhancements will be scoped as a new phase epic.
4. `h3. ✅ *Definition of Done*`: A numbered list of three to five closure conditions: what must be true for this epic to close. Write them as outcomes, not test steps. The epic itself is not tested: verification happens in its child stories and tasks, whose Acceptance Criteria carry the testable detail. No quality-bar item: The quality bar (Section 508 and WCAG 2.1 AA accessibility, CTDC design system conformance, page performance; contract stability, observability, secrets handling, and cross-environment parity for backend work) is a team-wide standard that QA checks on every story and release. It is not written into user stories, tasks, or epics (TPM decision, 2026-10-13).
5. `h3. 📝 *Notes*`: Two or three bullets at most: the phase statement (complete and the release it shipped in, or what closes it), and the canonical repos with branch. An upstream Bento package goes here when one is involved. Nothing else.

**Standing emoji set (5 entries)**

| Section | Emoji |
|---|---|
| Epic Statement | 🎯 |
| Description | 🧬 |
| Scope | 🗺️ |
| Definition of Done | ✅ |
| Notes | 📝 |

**Required content rules (universal rules in 7b-shared also apply)**

- **Card line first.** Line 1 is the card line (item 0), exactly one sentence of 140 characters or fewer, then a blank line.
- **Under 600 words** for the five sections (the card line is not counted). Count before pushing. CTDC-1802 is 597.
- **No ticket keys in the body** (7b-shared, "Cross-ticket references"). Related work is named in prose; Jira links are for parent/child only.
- **Grounded before drafting.** Verify the live UI in Claude in Chrome for anything user-facing, and read the canonical repo (confirmed via `CBIIT/crdc-ctdc-starter-kit/.gitmodules`, Section 17) for the mechanism in the Description. The child tickets' summaries are not a substitute.
- **Memgraph, never Neo4j; OpenSearch named when relevant; FAIR stated where it falls naturally** (usually in the Epic Statement's "why").
- **Underscored identifiers in `{{...}}` monospace** (`{{drs_uri}}`, `{{REACT_APP_INTEROP_SERVICE_URL}}`) so Jira does not open italic spans.
- **Curly braces escaped as `\{...\}`** anywhere they appear.
- **Not evergreen** (feature, page, service, and infrastructure epics; the standing data epics in 7b-D are the exception). The Notes phase statement says what this epic delivered; the epic is Closed (no resolution) when its children are done (7b-shared, "Epic posture defaults").

**Writing-and-publishing workflow**

1. **Verify** the hosting surface (Claude in Chrome) and the canonical repo before drafting.
2. **Draft** the card line and all 5 sections in Jira wiki markup, applying the rules above. Check the word count.
3. **Push** via `PUT /rest/api/2/issue/<KEY>` with the description in `fields` (`{"description": "..."}`, raw Jira wiki; see "Authoring format" in 7b-shared) (for a new epic, `POST /rest/api/2/issue` with a one-line placeholder first, then update). Set `priority` to Major in the same call; never touch `labels` unless deliberately changing them.
4. **Verify the render with a UI screenshot** from the user; wiki source is not a render preview.
5. **Close the epic** (transition `Closed`, no resolution) with a short comment naming the release once every child ticket is Closed.

**Migration of v1 epics**

*Data-related epics use the data variant in 7b-D* (Gina, 2026-09-25). Submission Data Modeling and Internal Data Modeling are standing epics that stay Open and carry a Risks table; they are migrated with 7b-D, not this template. Each study submission is its own closing epic on the Data Submission Epic template (DO-EPIC, 2026-09-25), piloted on CTDC-2110 (CMB v6). The Data Integration epic (CTDC-1664) stays Open as a standing operational epic on this template (7b): it holds integration work no single submission owns (cross-study loading and indexing, engineering and data modeling support, migrations and corrections of existing data, cross-study plans and logs). Model changes live in the modeling epics, and deployments of data-driven code live in Software Release Deployments (Gina, 2026-09-25). Mock Data Generation was reclassified the same day as a feature epic (its outputs serve data submitters, but it closes when delivered) and is migrated with this template.

Superseded 2026-09-24: Gina directed a sweep of every active CTDC epic to this template (the earlier "condense when next edited, no sweep" rule is retired). Order: In Progress epics first, then Ready for Review, then On Hold, with drafts reviewed by the TPM before each batch is written. Drafts condense the existing description only (no new facts); stale items are flagged to the TPM rather than guessed. Cut detail is not copied into comments: Jira's History tab keeps every prior description, and a JSON backup of the pre-sweep descriptions was taken on 2026-09-24. Final Design QA epics keep their own container template (cloned from the Final Design QA template epic) and only gain the card line.

### 7b-D. Data Epic Variant (Lean v2-D)

> **Use this variant for the two standing data modeling epics only**: Submission Data Modeling and Internal Data Modeling (Gina, 2026-09-25). Data submissions no longer have a standing epic: each study submission is its own closing epic on the Data Submission Epic template (DO-EPIC, 2026-09-25). Everything in 7b and 7b-shared applies except where this subsection says otherwise. Mock Data Generation is a feature epic and uses 7b; Bento Data Retriever is a microservice epic and uses 7b.

**Why a variant**

A feature epic delivers something and closes. A data epic is a standing pipeline: submissions, model releases, and data loads keep arriving, and each one is a child ticket that opens and closes on its own. The body therefore describes how work flows through the epic, not what one phase delivers. The two modeling epics also feed a chain that no feature epic has:

```
Submission Data Modeling ─┐
                          ├─> model release ─┬─> Data submission epics (validate, index, load, test)
Internal Data Modeling ───┘                  └─> Mock Data Generation and other consumers
```

**Card line + section order (card line, then 7 sections, exactly this sequence)**

0. **Card line**: same rule as 7b item 0.
1. `h3. 🎯 *Epic Statement*`: same as 7b (goal, objectives, why; not a user story).
2. `h3. 🧬 *Description*`: Two short paragraphs. (a) How the work runs: the model itself, meaning the repo, files, and branches the work changes (`CBIIT/ctdc-model`, `model-desc/ctdc_model_file.yaml` and `model-desc/ctdc_model_properties_file.yaml`, `develop` to `prod`, and the version history the Data Model Navigator displays), plus the CRDC Submission Portal where a submission drives the change. The act of loading study data (running the loader, promoting a study through the tiers) is data management and does not belong in a modeling epic; the stores and services built on the model schema still appear as downstream consumers (Gina, 2026-10-13). (b) How child work is tracked: the child naming pattern (for example `{{Submission Data Modeling: <submission>}}`) and the event that closes each child. Part (b) replaces 7b's "what this phase delivers." No list of active studies or releases in the body: the children carry that, and a list in the body goes stale.
3. `h3. 🗺️ *Scope*`: **In Scope** and **Out of Scope**, about five bullets each. Out of Scope names the sister data epics in prose. Omit 7b's closing "new phase epic" bullet.
4. `h3. 🔗 *Upstream & Downstream*`: Two labeled sub-lists, *Upstream* (what must be true before this work starts) and *Downstream* (who consumes what it produces), two to four bullets each, named in prose.
5. `h3. ✅ *Definition of Done*`: Three to five **standing** conditions, each phrased "Every <child, release, or load> ...": the bar each child must meet to close, since the standing epic itself does not close. A data promotion constraint (for example, no direct loads to Prod) is one item here.
6. `h3. ⚠️ *Risks & Mitigations*`: Required. A Jira wiki table `||Risk||Impact||Mitigation||` with three to six rows. Only standing risks that apply to the epic as a whole; a risk tied to one study or release goes on that child ticket. PHI handling is a row when the epic touches patient-level data.
7. `h3. 📝 *Notes*`: Two or three bullets: the Standing line ("Standing: stays Open for the life of the CTDC project; each child closes when <event>.") and the canonical repos with branch. Never the word "evergreen."

**Standing emoji set (7 entries)**

| Section | Emoji |
|---|---|
| Epic Statement | 🎯 |
| Description | 🧬 |
| Scope | 🗺️ |
| Upstream & Downstream | 🔗 |
| Definition of Done | ✅ |
| Risks & Mitigations | ⚠️ |
| Notes | 📝 |

**Content rules that differ from 7b**

- **Under 800 words** for the seven sections, including the Risks table text (the card line is not counted).
- **The model, not the data loading.** A modeling epic covers the shape of the model, not the act of loading study data. Upstream & Downstream names everything built on the model schema: Memgraph, OpenSearch, and the backend (a breaking change can require updated backend queries and index mappings), plus the Data Model Navigator, mock data generation, and submission templates and validation. The downstream impact list in the Definition of Done covers those same consumers. The Definition of Done holds no data-loading condition: running the loader and promoting a study's data through the tiers is verified on the data submission and loading tasks.
- **Standing, not closed.** These epics stay Open for the life of the CTDC project. The wording is "Standing"; the sweep check for "evergreen" still applies.
- **Not restored from the v1 data templates**: Stakeholders, Key Definitions, Assumptions, Constraints, User Impact, Components Breakdown, Documentation & Compliance. A constraint worth keeping becomes one acceptance criterion or one risk row.
- **Migration moves, never invents.** Drafts condense existing text only. A risk that belongs to a sister data epic moves to that epic (for example, "the model lags the incoming dataset" belongs to the modeling epics, not to a data submission epic).

---

### 7b-shared. Universal Epic Conventions

These rules apply to every CTDC epic. The lean template in 7b-lean inherits them without restating them.

**Authorship & affiliation**
- Preparer: Gina Kuffel, Senior Technical Project Manager
- Organization: Frederick National Laboratory for Cancer Research (FNL/BACS)
- Federal sponsor: NCI / Center for Biomedical Informatics and Technology (CBIIT)
- Program: Cancer Research Data Commons (CRDC)

**Domain content rules**
- **Memgraph, never Neo4j.** Always reference Memgraph as the graph database with the parenthetical *"replaces the historical Neo4j references"* when first introduced. The public `crdc-ctdc-starter-kit` README is outdated and must not be mirrored.
- **OpenSearch named explicitly** when the epic surfaces aggregations, counts, or facet-driven queries.
- **FAIR mission stated.** Connect the epic back to making CTDC data Findable, Accessible, Interoperable, and Reusable somewhere in the description (typically in Context & Background and User Impact).
- **CTDC ↔ ICDC translations.** When mirroring an ICDC pattern, translate node names and relationship names — `participant`/`case`, `specimen`/`sample`, `associated_with`/`of_*`, `data_file_uuid`/`file_uuid`, `study_accession`/`clinical_study_designation`. See Section 16.

**Epic posture defaults**
- **Priority:** Major (set in the create or update request, not the description).
- **Status and lifecycle:** Open while any child work is in flight; **Closed** once every child ticket is delivered. Epics are NOT evergreen containers. When a delivered feature, page, or service needs further work later, open a **new phase epic** for that work rather than reopening or extending the finished one. Close via the `Closed` transition (id 31 on the epic workflow both projects share since 2026-10-01); **no resolution is set or required on epics**. An epic is either Open or Closed: the children's resolutions record how the work ended, and the closing comment states whether the epic was **delivered** (name the release) or **retired** (why). To find open epics, query `statusCategory != Done`, never `resolution = Unresolved` (closed epics carry no resolution, so resolution-based filters would count them as open). TPM decision, 2026-10-13.
- **Assignee:** Unassigned, unless directed otherwise. Individual child tickets carry the actual ownership.
- **Labels:** Preserve any existing label (e.g., `Task-1.3.8.X`). Do not add or remove labels in epic-normalization passes unless deliberately changing them.

**Cross-ticket references (applies to epics, user stories, and tasks)**
- **Never write another Jira ticket key in the body of an epic, user story, or task.** Related, adjacent, or excluded work is named in prose by what it is (e.g., "the Cart page", "the RAS-enabled object file download initiative", "the per-submission IndexD registration work"), never by key.
- **Jira issue links are used only for parent/child relationships** (Epic Link and sub-task parentage). Do not add "Relates" links between epics to stand in for prose references.
- Ticket keys are still fine in Jira **comments**, in this SKILL.md, in the templates repo, and in sprint or stakeholder artifacts; the rule is about the description body of the ticket itself.
- This supersedes the earlier "cross-epic linkage" rule that asked Out of Scope, Dependencies, and Notes to cite sibling epic keys. Epics written before 2026-09-14 still carry inline keys; strip them the next time each one is edited rather than in a sweep.

**Quality bar**
- The quality bar (Section 508 and WCAG 2.1 AA accessibility, CTDC design system conformance, page performance; contract stability, observability, secrets handling, and cross-environment parity for backend work) is a team-wide standard that QA checks on every story and release. It is not written into user stories, tasks, or epics (TPM decision, 2026-10-13).

**Verification & ground truth**
- **For Application Pages and Features:** verify the live UI in Claude in Chrome (open the page, then read it with `get_page_text` or `read_page`) before drafting. Ground In Scope claims in what the page actually renders.
- **For Microservices, Infrastructure, Security, Data:** verify against the source repo, deployed config, or live system before drafting. Do not infer scope from imagination.
- **Verify the canonical repo before reading code.** When an epic touches CTDC code, **the first verification step is confirming you are in the project-canonical repo**, not just any repo whose name matches. CTDC has historical/abandoned repos that share name fragments with the active canonical repo (e.g., `bento-ctdc-frontend` is the abandoned 2021–2023 frontend; `crdc-ctdc-ui` is the active starter-kit-canonical frontend). The single source of truth for which repos are canonical is the `.gitmodules` file in `CBIIT/crdc-ctdc-starter-kit`. See Section 17 for the full list.
- **For all groupings:** the rendered Jira UI is the only ground truth for description rendering. Wiki source returned by `GET /rest/api/2/issue/<KEY>` does not reveal rendering state and looks identical for working and broken tickets: only a UI screenshot tells the truth.

**Authoring format (Jira wiki, since 2026-09-24)**
- **The `description` field takes Jira wiki markup, stored exactly as sent.** Jira is written through the REST API v2 from a signed-in `tracker.nci.nih.gov` tab in Claude in Chrome (the Atlassian MCP and its Docker container were retired on 2026-11-02). Nothing converts Markdown: raw `###` and `**` appear on the page if sent. Write requests (`POST`/`PUT`) need the headers `X-Atlassian-Token: no-check` and `Content-Type: application/json`, and descriptions go in `fields` as `{"description": "..."}`.

  | Want | Write (Jira wiki) |
  |---|---|
  | h3 heading | `h3. 🎯 *Title*` |
  | Bold | `*bold*` |
  | Italic | `_italic_` |
  | Bullet | `* item` |
  | Numbered | `# item` |
  | Monospace | `{{code_name}}` |
  | Link | `[text|url]` |
  | Table | `||h||` header row, `|c|` data rows |

- Bullets and numbered items are flush-left at column 0. Blank line after every heading. After pushing, read the description back and confirm it starts with `h3.`, not `###`.
- **Underscored identifiers** go in `{{...}}` monospace in plain text and are percent-encoded (`%5F`) inside link syntax, or Jira opens stray italic spans.
- **Curly-brace rule (universal).** Anywhere curly braces appear in description text (URLs with path parameters, scope items naming a variable, acceptance criteria, notes), escape them: `\{program_short_name\}`, not `{program_short_name}`. The only exception is the `{{monospace}}` and `[text|url]` wiki syntax itself.
- Markdown-era conventions (authored while the old connector converted Markdown, verified on CTDC-1960 and CTDC-1922) are retired; Jira's History tab keeps any description written under them.

**Write notes (why the curly-brace rule exists)**

The Jira wiki renderer treats `{...}` as **macro syntax** (`{code}`, `{noformat}`, `{panel}`, `{color}`). An unknown macro name like `{program_short_name}` corrupts the parser state for the surrounding block, and the damage propagates forward: headers render as literal `h3.` text, bullets lose their list context, some headers lose their `h3.` prefix entirely, and paragraphs fracture at the `{...}`. This was the verified root cause of the CTDC-2040 (Program Details Page) render failure on 2026-04-30; escaping the braces fixed it on the next push.

**Workflow:**

1. Author Jira wiki markup with `\{...\}` escaping anywhere curly braces appear in body text.
2. Push the description with `PUT /rest/api/2/issue/<KEY>` (`{"fields": {"description": "..."}}`).
3. **Verify the render with a UI screenshot from the user**, not the wiki source. Wiki source returned by `GET /rest/api/2/issue/<KEY>` is unreliable as a render preview.
4. **If rendering is broken**, the most likely cause is unescaped curly braces somewhere in the description. Re-check the source for any `{...}` not preceded by a backslash.
5. **Last-resort fallback:** if the source is clean and the render is still broken, ask the user to re-paste the description through the Jira web UI's Description field. This is rarely needed once the curly-brace rule is followed.

**Other fields are safe.** Priority, summary, custom fields, status transitions, and labels write reliably through `PUT /rest/api/2/issue/<KEY>` because they don't go through the wiki-text renderer.

**Comment body wiki-rendering quirks.** Comment bodies (added with `POST /rest/api/2/issue/<KEY>/comment`, or with a `comment` in the `update` block of `POST /rest/api/2/issue/<KEY>/transitions`) also pass through the Jira wiki renderer and have their own quirks separate from descriptions:

- **Plus signs (`+`) get silently mangled.** The wiki renderer interprets `+` as inserted-text markup. A comment that says "Memgraph + OpenSearch" will render as "Memgraph  OpenSearch" with the `+` stripped. **Fix:** use plain English connectives like "and" or "plus" in comment bodies, or escape with a backslash if the literal `+` is essential. Verified on CTDC-1658 closure comment, 2026-05-06.
- **Curly braces have the same macro-syntax issue as descriptions.** Apply the same `\{...\}` escaping rule.

**Risk format (when a risk table is needed at all)**

The lean epic template (7b-lean) has no Risks section: risks belong in the `.docx` epic summary (Section 5) or in a comment. The data variant (7b-D) requires one. When a table is needed anywhere in a Jira body, it renders as a three-column **table**, not a bullet list:

```
h3. ⚠️ *Risks & Mitigations*
||Risk||Impact||Mitigation||
|Schema changes break generation scripts|Mock files become outdated|Implement automated generation triggered by schema version updates|
|Mock data does not reflect real-world complexity|Submitters misinterpret requirements|Include edge-case examples and relational integrity scenarios|
```
