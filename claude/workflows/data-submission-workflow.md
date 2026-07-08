# CTDC Data Submission — Process SOP

*Internal operational reference for Claude (Sprint Command Center). Owner: CTDC Data Concierge. Covers active data submissions end to end — the order the work happens in, and which Jira artifact + template to create at each step. This is the internal/technical companion to the separately-maintained, submitter-facing Active Data Submission SOP.*

## The issue family (all under epic CTDC-1664, CTDC Data Integration)

| Artifact | Template | Count |
|---|---|---|
| Data Submission **User Story** — the umbrella | **7e** | 1 per study submission / version |
| **Data Modeling** task | **7f** | 1 |
| **IndexD Registration** task | **7g** | 1 |
| **Data Loading** task | **7h** | 1 per load event |
| **Megazip** task — only if study files should download as one bundle | **7i** | as needed |

The User Story title sets the canonical `<Study Name vN>` token; every task reuses it verbatim (`Data Modeling: <Study Name vN>`, etc.). New tickets stay **unassigned** unless directed; data-management tasks don't need the Developer field.

## The flow (▶ = create a Jira artifact)

1. **SRF submitted** — the CRDC Data Submission Request Date is now known.
2. **SRF approved** — ▶ **Create the User Story (7e).** Assign the Data Concierge and create the study's single SharePoint folder. The story coordinates the whole effort; it never executes work.
3. **Data modeling starts** (hand-in-hand with the submitter — learning their data and what the model must accommodate) — ▶ **Create the Data Modeling task (7f).** Link `Supports` the story. Build out the CDE Request Workbook — the term-level source of truth.
4. **CDE Workbook complete** — submit the **DHDM ticket** to Data Hub; link it `Related To` the story and reference it from the modeling task. The Workbook must be finished before the DHDM ticket goes in.
5. **dbGaP IDs in hand** — required gate before the DataHub portal submission can begin.
6. **Submission created in the Portal** — the Submission ID is assigned (can be months after SRF approval). Submit and release the data.
7. **Released** — the Release Package lands in the Submission Portal's AWS bucket; hand it off to engineering.
8. **Indexing** — ▶ **Create the IndexD Registration task (7g).** Carries the `Data-Concierge` label; links `Supports` the story. Runs **in parallel** with loading — link the two with `Relates`, never `Blocks`.
9. **Loading** — ▶ **Create a Data Loading task (7h).** No label; links `Supports` the story. One Jenkins job per tier: Dev → QA → Stage → Prod. Every later reload is its own new loading task.
10. **Verify** — confirm the study renders in Production and its files download.
11. **Megazip** (only if the study's object files should download as one bundle) — once **all** loading is complete, ▶ **create the Megazip task (7i).**

## Standing rules

- **Tasks execute; the story deliberates.** Open questions, risks, and handoffs live on the User Story, never on child tasks.
- **Indexing never blocks loading** — they run in parallel (`Relates`).
- **Reloads are always a new Data Loading task** — never folded into a modeling ticket.
- **Scope is active data submissions only.** Model changes the CTDC project itself initiates (Data Model Update, 7j) are a different workflow, out of scope here.
- Data-management tasks don't require the Developer field; new tickets stay unassigned unless directed.
