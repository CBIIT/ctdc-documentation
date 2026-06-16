### 7i. 📥 Data Submission User Story Template (Drafted v1)

> **Use this template for the parent user story of every CTDC study data submission** — the artifact the **CTDC Data Concierge** uses to shepherd a study's data into CTDC end to end. Canonical example: **[CTDC-1666](https://tracker.nci.nih.gov/browse/CTDC-1666)** (NCI-MATCH Arm Z1D). Working instance: **[CTDC-2110](https://tracker.nci.nih.gov/browse/CTDC-2110)** (Data Submission: Cancer Moonshot Biobank v6). This story is the **coordinating outline** for one study submission; it is **not** where any activity is executed. Data modeling (7g/7f), IndexD registration (7h), and data loading (7e) each get their own linked task, and term-level modeling detail lives in the study's CDE Request Workbook. This is the parent that every downstream data-management task for the submission links back to.

**Why this template**

A study data submission touches many distinct activities — Submission Portal work, submitter communications, validation, modeling, indexing, loading, verification. The Data Concierge needs one place that encapsulates and coordinates them without duplicating their detail. This story is that place: it holds study identity, the lifecycle outline, and aggregate scope. The linked tasks and the CDE Request Workbook hold the specifics.

**Point of view: the CTDC Data Concierge.** The narrative and the lifecycle are written from the Concierge's perspective — the person guiding the submitter, supporting validation, coordinating modeling, obtaining the Release Package, and verifying the study in Production. This is a deliberate departure from the Submitter-POV phrasing on older stories.

**A submission and a model change are related but distinct activities.** The submission delivers new study data values; any data model change is a separate, linked activity that the submission may or may not require. The story names modeling in its lifecycle outline at summary altitude only — it never asserts that a given submission requires a model change, and it never carries modeling detail.

**Three commitments**

1. **One user story per study submission, per version.** A new submission — or a new version of an existing study's submission — gets its own story. Don't roll a new version into a closed/On-Hold prior story; link the prior version with `Relates` instead.
2. **The story coordinates; the linked tasks execute.** Modeling, indexing, and loading appear in the lifecycle outline at summary altitude only. Their steps, specifics, and state live on their own tasks.
3. **The CDE Request Workbook is the term-level source of truth.** Node-, property-, permissible-value-, and count-level detail never appears on the story; the Data Details section carries aggregate study-level counts only.

**Section order (narrative + 6 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + **bold** title format shown. The narrative paragraph comes first, with no header. Don't omit, reorder, or merge sections. If a section genuinely has no content yet, fill it with `TBD` rather than dropping the header.

- *(narrative)* — One paragraph, Data Concierge POV. Example: *"As the CTDC Data Concierge, I shepherd a study's data submission from end to end — guiding the submitter through the CRDC Submission Portal, supporting data validation, coordinating any required data modeling, obtaining the Release Package, and ensuring the data is indexed, loaded, and verified in CTDC — so that the study is correctly integrated and its files are downloadable in Production."*

1. `### 🧬 **Study Identity**` — A two-column table; the at-a-glance identity plus all working links (folders, DH ticket, CDE Workbook) **consolidated here** (no separate references section). Rows, in order: Program, Program Short Name, Study Name, Study Status in CTDC, dbGaP ID, dbGaP Link, Associated Publications, CRDC Data Submission Request Date, Submission Request Form (SRF), Submission ID, Submitter / Submission Team, CTDC Data Concierge, Data Hub Data Modeling Ticket (DHDM), SharePoint Folder, CDE Request Workbook. **No AWS bucket row** — that is loading-only and lives on the Data Loading task. Notes: **Submission ID** is ascribed by the Submission Portal (`TBD` until submitted); **SharePoint Folder** is the single folder for the submission; **Data Hub Data Modeling Ticket (DHDM)** is the same ticket referenced in the modeling task's DM Federal Lead & SME Review section. Use `NA` where a field genuinely doesn't apply (e.g., Program for a study with no parent program) and `TBD` where it is pending.

2. `### 📋 **POC Requirements**` — One line pointing to the CTDC Active Data Submission SOP.

3. `### 🚦 **Submission Lifecycle**` — The Data Concierge's end-to-end outline. A short framing line, then a numbered list at **summary altitude only**. The story is the umbrella over the whole effort; each activity that has its own task says so ("a separate, linked … activity"). Standard outline:

   *This story is the Data Concierge's end-to-end outline for the submission. Each activity below is executed and tracked on its own linked task; this story encapsulates and coordinates them.*

   1. Submitter submits the Data Submission Request through the CRDC Submission Portal; the Submission Review Committee approves it.
   2. Provide Data Concierge services and maintain communications with the submitter throughout.
   3. Support data validation efforts in the CRDC Submission Portal.
   4. Coordinate any required CTDC data modeling — a separate, linked Data Modeling activity, related but distinct from this submission.
   5. Submit and release the data from the CRDC Submission Portal.
   6. Obtain the Release Package — placed programmatically by the Submission Portal into its AWS bucket on release — and hand it off to the CTDC engineering team.
   7. Coordinate data indexing — a separate, linked Data Indexing (IndexD registration) activity.
   8. Coordinate data loading into Dev → QA → Stage → Production — separate, linked Data Loading activities.
   9. Confirm the study looks correct in Production and its files can be downloaded.

   No execution detail belongs here — that lives on the linked tasks.

4. `### 📅 **Submission Chronology**` — A "Current as of \{date\}" line followed by a dated event log. Maintained by the Data Concierge. `TBD` until events exist.

5. `### 🔬 **Study Description**` — Prose describing the study/protocol. Study-level context; `TBD` until authored.

6. `### 📊 **Data Details**` — A short boundary line, then an aggregate metric table. Aggregate, study-level submission scope only — term-, property-, permissible-value-, and node-level detail lives in the CDE Request Workbook. Metric rows: Participants, Diagnoses, Therapies, Biospecimens, Participant Files, Study-Level Files, Data Volume.

**Standing emoji set (6 entries)**

| Section | Emoji |
|---|---|
| Study Identity | 🧬 |
| POC Requirements | 📋 |
| Submission Lifecycle | 🚦 |
| Submission Chronology | 📅 |
| Study Description | 🔬 |
| Data Details | 📊 |

**Title convention**

- The user story title is `Data Submission: <Study Name vN>` (full study name, plus version where the study recurs across submissions). The user story title **defines the canonical `<Study Name vN>` token**; every downstream task reuses that exact string verbatim (`Data Modeling: <Study Name vN>`, `Data Indexing: <Study Name vN>`, `Data Loading: <Study Name vN>`). This keeps the whole family parallel, groupable on the board, and findable in one `summary ~ "<Study Name vN>"` query, with no drift between the full name, an acronym, and a version string.

**Required content rules**

- **Data Concierge POV throughout** — narrative and lifecycle.
- **The Submission Lifecycle is an outline, not a procedure** — summary altitude, with linked-task pointers for modeling, indexing, and loading.
- **No node/property/PV/term/count specifics anywhere** — Data Details is aggregate only; everything finer lives in the CDE Request Workbook.
- **No AWS bucket / Release Package location on the story** — loading-only, lives on the Data Loading task.
- **Links consolidated into Study Identity** — no separate Document & Folder References section.
- **`NA` for genuinely-inapplicable fields, `TBD` for pending ones.**
- **Rendering-safe authoring**: section headers use `### **Title**` Markdown (round-trips to `h3.`); author in Markdown and let the converter produce Jira-wiki; use Jira-wiki `||header||` table syntax. Escape every curly brace as `\{...\}`. In SharePoint/GitHub URLs, percent-encode `(` / `)` as `%28` / `%29` and `_` as `%5F` so the wiki renderer cannot break the link.

**Linking conventions**

- **Epic:** `customfield_12350` → the study-integration epic (**CTDC-1664**, CTDC Data Integration).
- **Data Modeling task (7g/7f)** → `Supports` this story.
- **IndexD Registration (7h) / Data Loading (7e) tasks** → `Supports` this story.
- **Prior version's story** → `Relates` (version lineage).
- **Data Hub Data Modeling (DHDM) tracking ticket** → `Related To`.

**Metadata**

- Issue type **User Story**; priority **Major**; component **Data**; labels **`Data-Concierge`** plus the WBS line (`Task-1.2.1.x`).
- Leave **Unassigned** at creation unless directed.

**Writing-and-publishing workflow**

1. Confirm the study-integration epic (CTDC-1664) exists for the epic link.
2. Two-step create: `jira_create_issue` with a one-line placeholder, then `jira_update_issue` with the full Markdown body. Same pattern as every other CTDC template.
3. **Do not hand-edit the description in the Jira UI afterward** — re-push via `jira_update_issue`; the wiki editor eats underscores and mangles monospace.
4. Render-verify in the Jira UI. The MCP read-tool's Markdown reconversion mangles underscores into asterisks — trust the rendered ticket, not the API echo.
5. Add the native links above as the modeling/indexing/loading tasks and the DHDM ticket come into being.

**Changelog**

- **v1 (2026-06-16)** — Initial draft. Promotes the CTDC-1666 gold-standard shape into a reusable template. Establishes: Data Concierge POV; the 🚦 Submission Lifecycle outline that coordinates linked modeling/indexing/loading at summary altitude; link-consolidated Study Identity (no separate references section; SRF, Submission ID, DHDM rows added; the two SharePoint folder rows consolidated to one; AWS bucket row removed as loading-only); aggregate-only Data Details; the submission-vs-model-change "related but distinct activities" framing; and the `Data Submission: <Study Name vN>` title convention that defines the canonical study token reused verbatim by all downstream tasks. Canonical example CTDC-1666; working instance CTDC-2110 (Data Submission: Cancer Moonshot Biobank v6).
