# Epic Summary: CTDC Submission Data Modeling

**Epic:** CTDC-2217  
**Version:** v1.0  
**Date Prepared:** September 15, 2026  
**Status:** In Progress (evergreen epic)  
**Epic Priority:** Major  

**Prepared By**  
Gina Kuffel  
Senior Technical Project Manager  
Frederick National Laboratory for Cancer Research (FNL)  
Contractor to the National Cancer Institute (NCI)  
Center for Biomedical Informatics and Technology (CBIIT)  
Cancer Research Data Commons (CRDC)

---

## 1. Executive Summary

Every new study or data release that arrives at the Clinical and Translational Data Commons brings its own spreadsheet columns, vocabularies, and data types. Before that data can be validated and loaded, the CTDC data model (the blueprint that defines what the commons can store) often has to be extended to represent it. This epic covers that work: reading the submitter's data dictionary, identifying what the model cannot yet hold, requesting standardized Common Data Elements from NCI's Semantic Infrastructure team, and releasing an updated model so the submission can proceed.

The reason this matters is twofold. First, submissions cannot move forward until the model can represent them, so this work is frequently the gating step for onboarding a new program. Second, doing it through governed NCI standards rather than one-off fixes is what allows data from the Cancer Moonshot Biobank, NCI-MATCH, and the CIMAC-CIDC immuno-oncology network to be queried side by side. That cross-program comparability is the core of the FAIR promise (Findable, Accessible, Interoperable, Reusable).

Until September 2026 this work was tracked inside the Data Integration epic alongside loading and testing tasks, or occasionally under Internal Data Modeling, which made it hard to see how much modeling effort each incoming program actually required. This epic was created to separate it out. It is evergreen: it stays open for the life of the project, with one long-lived task per study or data release underneath it.

---

## 2. Scope & Objectives

### In Scope

- Gap analysis of a submitter's data dictionary against the current CTDC data model
- Node, relationship, property, CDE, and permissible value changes required by a specific submission
- Creation and maintenance of a CDE Request Workbook for each submission, and the associated caDSR help desk requests to the NCI Semantic Infrastructure team
- Model releases whose purpose is to unblock a submission, including diagram regeneration and coordination of downstream impact
- Recurring model updates for programs that submit in numbered data releases (for example, each Cancer Moonshot Biobank version)

### Out of Scope

- Internally motivated model changes such as governance, SOP, repository structure, CDE hygiene, and application-driven properties (tracked under the Internal Data Modeling epic)
- Transformation, validation, ingestion, and testing of the submission itself (tracked under the Data Integration epic)
- Mock data generation, backend and OpenSearch implementation, application UI changes, and the ICDC data model, each owned elsewhere

---

## 3. Technical Context (Plain-English Glossary)

| Term | Plain-English Explanation |
|---|---|
| **Data Dictionary** | The submitter's own description of the columns in their data files: what each field means and what values it can hold. The starting point for every gap analysis. |
| **Gap Analysis** | A field-by-field comparison of the submitter's data dictionary against the CTDC model to find what can be stored as-is, what needs a new property or value, and what needs a new node. |
| **CDE (Common Data Element)** | A standardized, reusable definition of a data field, registered in NCI's caDSR system. Binding a CTDC property to a CDE means it shares a definition with other NCI systems, which is what makes data comparable across programs. |
| **CDE Request Workbook** | The per-submission spreadsheet that records every requested model change: the source field, the target CTDC element, the CDE (existing or requested), the allowed values, and the approval and release status. It is the system of record for what was modeled and why; the Jira ticket tracks only milestones. |
| **Semantic Infrastructure (SI) Team** | The NCI team that curates caDSR. They create new CDEs and add permissible values in response to CTDC's help desk requests. |
| **Data Concierge** | The CTDC team member who owns the gap analysis, the workbook, the caDSR requests, and the model pull request for a given submission. |
| **Data Model Navigator** | The page on both the CRDC Submission Portal and CTDC where submitters and researchers can browse the current model. A release is verified by confirming the new version appears there. |
| **Memgraph** | The graph database that stores CTDC data in the shape the model defines. It replaced the historical Neo4j database. |

---

## 4. Work Breakdown

All work items are tracked as Tasks in Jira under epic CTDC-2217. Each task represents one study or data release and stays open until that submission completes in the CRDC Submission Portal.

| Ticket | Summary | Type | Status | Assignee | Story Points |
|---|---|---|---|---|---|
| CTDC-1753 | Update data model for CMB version 5 study submission | Task | **Closed** | Valentina Epishina | 5 |
| CTDC-1784 | Update data model for NCTN-NCORP study submissions | Task | **Closed** | Valentina Epishina | 2 |
| CTDC-1799 | Submission Data Modeling: IODH-CIDC-CIMAC | Task | In Progress | Patrick Breads | 5 |
| CTDC-1893 | Additional updates to data model for CIDC-CIMAC study submission | Task | **Closed** (Duplicate) | Gina Kuffel | 5 |
| CTDC-1903 | Add Assay Nodes to the CTDC Data Model to support CIMAC-CIDC | Task | **Closed** | Patrick Breads | 8 |
| CTDC-2051 | Submission Data Modeling: NCI-MATCH Arm Z1D IHC | Task | On Hold | Patrick Breads | Not estimated |
| CTDC-2111 | Submission Data Modeling: Cancer Moonshot Biobank v6 | Task | Open | Patrick Breads | Not estimated |

---

## 5. Progress Summary

| Metric | Value |
|---|---|
| **Total Tickets** | 7 |
| **Closed (Done)** | 4 (one closed as a duplicate of CTDC-1799) |
| **In Progress** | 1 (CTDC-1799) |
| **On Hold** | 1 (CTDC-2051) |
| **Open / Not Started** | 1 (CTDC-2111) |
| **Blocked** | 0 |
| **Completion Rate** | 57% (4 of 7 tickets closed) |
| **Story Points Burned / Total** | 20 / 25 (2 tickets not estimated) |
| **Sprint Association** | Evergreen epic; child tasks are pulled into sprints individually |

---

## 6. Risks & Blockers

| Ticket | Issue | Impact | Mitigation / Owner |
|---|---|---|---|
| CTDC-2111 | Cancer Moonshot Biobank v6 requires new CDEs and permissible values (therapy properties, specimen preservation, program naming) that are still being issued by the Semantic Infrastructure team, and the submitter has outstanding validation errors tied to the clinical metadata. | The v6 load, and the breaking-change model release for the consolidated therapy node that is gated on it, cannot be scheduled until both the modeling and the submitter's corrections are complete. | Patrick Breads (Data Concierge) is tracking the open caDSR requests; TPM (Gina Kuffel, FNL) coordinates with the CMB program team on the validation errors. |
| CTDC-1799 | IODH-CIMAC-CIDC modeling remains open as the network continues to surface additional assay properties and timepoints after the initial model release. | Each new request restarts the CDE request cycle, extending the time before the program's data can be fully loaded. | Task is intentionally long-lived; the CDE Request Workbook captures each iteration so nothing is lost between rounds. |
| CTDC-2051 | NCI-MATCH Arm Z1D IHC is On Hold pending program name and disease code corrections in caDSR and submitter-side file fixes. | Arm Z1D cannot pass Submission Portal validation until the CDE updates land. | Stephanie Singleton reviewed and approved the caDSR request; Patrick Breads is filing it. |
| Epic | Submitter data dictionaries sometimes arrive late or incomplete. | Gap analysis slips, which slips the model release and the dependent integration. | Require a data dictionary at intake as a precondition for scheduling an integration. |

---

## 7. Diagrams & Visuals

The CIMAC-CIDC assay modeling work (CTDC-1903) produced entity relationship diagrams that are stored with the program's documentation on SharePoint. The visual model diagram (`model-desc/ctdc-model.svg`) is regenerated with every model release and lives in the `CBIIT/ctdc-model` repository on the `prod` branch. No other diagrams or mockups are attached to this epic at the time of publication.

---

## 8. Next Steps & Open Questions

### Immediate Next Steps

- Close out the remaining caDSR requests for Cancer Moonshot Biobank v6 and release the model so the v6 load can be scheduled
- File the approved caDSR request for NCI-MATCH Arm Z1D IHC and take CTDC-2051 off hold once the CDE updates are issued
- Continue iterating the IODH-CIMAC-CIDC workbook as the network supplies additional assay properties
- Adopt the `Submission Data Modeling: <study or release>` naming convention for every new child task so effort per program is reportable from Jira

### Open Questions

- Should older submission modeling tasks be renamed to the current naming convention for consistency in reporting?
- What is the expected timeline from the Semantic Infrastructure team for the remaining CMB v6 CDE requests?

---

## 9. Document History

| Version | Date | Author | Summary of Changes |
|---|---|---|---|
| v1.0 | September 15, 2026 | Gina Kuffel (FNL) | Initial publication |

---

*Prepared by Frederick National Laboratory for Cancer Research (FNL) | Contractor to NCI/CBIIT*
