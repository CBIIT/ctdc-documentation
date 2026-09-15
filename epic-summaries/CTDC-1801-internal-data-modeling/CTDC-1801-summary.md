# Epic Summary: CTDC Internal Data Modeling

**Epic:** CTDC-1801  
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

The CTDC data model is the blueprint that defines what kinds of information the Clinical and Translational Data Commons can hold (studies, participants, specimens, diagnoses, therapies, files) and how those pieces connect to one another. Every screen in the CTDC application, every search filter, and every incoming data submission depends on that blueprint being accurate and consistent. This epic covers the ongoing work of maintaining the blueprint for reasons that come from inside the platform: keeping vocabulary aligned with NCI's Common Data Element (CDE) standards, adding fields the application needs, restructuring how the model repository is organized and governed, and fixing issues discovered by testing tools.

The reason this matters is trust. When the model drifts out of alignment with NCI standards, submitters run into unexpected validation errors, searches return inconsistent results, and comparing data across studies becomes unreliable. Disciplined model maintenance is what keeps CTDC data Findable, Accessible, Interoperable, and Reusable (FAIR).

This is an evergreen epic. It stays open for the life of the CTDC project, and individual modeling efforts open and close underneath it as tasks. Model changes that are triggered by a specific incoming study or data release are tracked separately under the companion Submission Data Modeling epic.

---

## 2. Scope & Objectives

### In Scope

- Additions, changes, and retirements of nodes, relationships, and properties whose trigger is a platform, application, governance, or tooling need
- Alignment of model properties with caDSR Common Data Elements and cleanup of locally defined value lists that duplicate a governed CDE
- Maintenance of the model repository structure, continuous integration checks, the Data Model Contribution SOP, the Data Model Owner Guide, and the version history
- Regeneration of the visual model diagram and coordination of downstream impact (backend, search index, mock data, ingestion, submission templates) with each release
- Schema fixes surfaced by mock data generation, ingestion validation, or the application

### Out of Scope

- Model changes driven by an incoming study or data release (tracked under the Submission Data Modeling epic)
- Transformation, validation, ingestion, and testing of submitted data (tracked under the Data Integration epic)
- Mock data generation, backend and OpenSearch implementation, application UI changes, and the ICDC data model, each owned elsewhere

---

## 3. Technical Context (Plain-English Glossary)

| Term | Plain-English Explanation |
|---|---|
| **Data Model** | The structured definition of what CTDC can store and how the pieces relate. Written as two YAML text files in the `CBIIT/ctdc-model` GitHub repository. Think of it as the floor plan for the data. |
| **Node / Property / Relationship** | A node is a kind of thing (a participant, a specimen). A property is a fact about that thing (age, tissue type). A relationship is how two things connect (this specimen came from this participant). |
| **CDE (Common Data Element)** | A standardized, reusable definition of a data field, registered in NCI's caDSR system. Binding a CTDC property to a CDE means it uses the same definition and allowed values as other NCI systems, which is what makes data comparable across programs. |
| **Permissible Values** | The list of allowed answers for a field (for example, the accepted names of tumor grades). Governed by caDSR when a CDE is bound; defined locally otherwise. |
| **Semantic Versioning** | The numbering scheme for model releases (MAJOR.MINOR.PATCH). A major bump signals a change that could break something downstream; minor and patch bumps are backward compatible. |
| **Memgraph** | The graph database that stores CTDC data in the shape the model defines. It replaced the historical Neo4j database. |
| **OpenSearch** | The search and aggregation index that powers CTDC filters and counts. Which fields are searchable is determined by the model. |
| **Contribution SOP** | The standard operating procedure every model change must follow: branch from `develop`, pass automated checks, match the release tag to the version line in the model file, regenerate the diagram, and record the change in the version history. |

---

## 4. Work Breakdown

All work items are tracked as Tasks in Jira under epic CTDC-1801.

| Ticket | Summary | Type | Status | Assignee | Story Points |
|---|---|---|---|---|---|
| CTDC-1742 | Revise the structure of the ctdc-model GitHub repo | Task | **Closed** | Gina Kuffel | Not estimated |
| CTDC-1780 | Add new BE queries for the consent group and study nodes | Task | **Closed** | Eric Miller | 2 |
| CTDC-1800 | Add study_version to the CTDC Data Model | Task | **Closed** | Valentina Epishina | 1 |
| CTDC-2020 | Data Modeling: Add useNullCDE flags to CTDC model | Task | **Closed** | Patrick Breads | 3 |
| CTDC-1936 | Internal Data Modeling: Remove Enum blocks from properties bound to a CDE | Task | **Closed** | Valentina Epishina | 2 |
| CTDC-1937 | CTDC Data Model updates to resolve mock data issues | Task | **Closed** | Patrick Breads | Not estimated |
| CTDC-2041 | SOP follow-up cleanup: Section 8, Section 10, header path | Task | Ready for QA Testing | Valentina Epishina | Not estimated |
| CTDC-2046 | Disable data file type CDE to support megazip downloads | Task | **Closed** | Valentina Epishina | 0.05 |
| CTDC-2112 | Internal Data Modeling: Add a new therapy node | Task | **Closed** | Valentina Epishina | 2 |
| CTDC-2114 | Research the MDF Python Library | Task | On Hold | Patrick Breads | 3 |
| CTDC-2121 | Internal Data Modeling: Add Acceptable Values to the new Program & Program Short Name CDE | Task | **Closed** | Stephanie Singleton | 2 |
| CTDC-2124 | Internal Data Modeling: Add useNullCDE flags to CTDC model | Task | **Closed** | Stephanie Singleton | 2 |
| CTDC-2201 | Internal Data Modeling: Disable CDE for program_name & program_short_name and add custom Enum blocks | Task | **Closed** | Valentina Epishina | Not estimated |
| CTDC-2202 | Internal Data Modeling: Disable CDEs with by-reference permissible values | Task | **Closed** | Valentina Epishina | Not estimated |
| CTDC-2204 | Internal Data Modeling: Update CDE version values | Task | In Progress | Patrick Breads | 1 |
| CTDC-2214 | Internal Data Modeling: Correct Yaml Format to make EDP compliant | Task | On Hold | Patrick Breads | 5 |

---

## 5. Progress Summary

| Metric | Value |
|---|---|
| **Total Tickets** | 16 |
| **Closed (Done)** | 12 |
| **Ready for QA Testing** | 1 (CTDC-2041) |
| **In Progress** | 1 (CTDC-2204) |
| **On Hold** | 2 (CTDC-2114, CTDC-2214) |
| **Blocked** | 0 |
| **Completion Rate** | 75% (12 of 16 tickets closed) |
| **Story Points Burned / Total** | 14.05 / 23.05 (5 tickets not estimated) |
| **Sprint Association** | Evergreen epic; child tasks are pulled into sprints individually |

---

## 6. Risks & Blockers

| Ticket | Issue | Impact | Mitigation / Owner |
|---|---|---|---|
| CTDC-2214 | Correcting the YAML format for EDP (Enterprise Data Platform) compliance is On Hold. Until it lands, the model files may not validate against the enterprise tooling that consumes them. | Downstream enterprise integration is delayed; the longer the fix waits, the more releases accumulate that will need the same correction. | Patrick Breads (Data Concierge) owns the task; TPM (Gina Kuffel, FNL) to confirm the EDP requirement with CBIIT and schedule the work. |
| CTDC-2204 | CDE version values in the model lag the versions currently registered in caDSR. | Submitters validating against caDSR may see mismatches between the CTDC template and the CDE's current definition. | In Progress with Patrick Breads; coordinated with the NCI Semantic Infrastructure team. |
| Epic | Internal and submission-driven model changes can collide in the same release window, producing conflicting pull requests and unclear version attribution. | Downstream consumers cannot tell which change caused which impact. | TPM sequences releases between this epic and the Submission Data Modeling epic; one release per change set where practical. |

---

## 7. Diagrams & Visuals

The visual model diagram (`model-desc/ctdc-model.svg`) is regenerated with every model release and lives in the `CBIIT/ctdc-model` repository on the `prod` branch. No additional architecture diagrams or mockups are attached to this epic at the time of publication.

---

## 8. Next Steps & Open Questions

### Immediate Next Steps

- Complete QA on the SOP follow-up cleanup (CTDC-2041) and close it
- Finish the CDE version value update (CTDC-2204) in coordination with the Semantic Infrastructure team
- Confirm the EDP compliance requirement and timeline so CTDC-2214 can come off hold
- Decide whether the MDF Python Library research (CTDC-2114) should resume or be closed

### Open Questions

- What is the required delivery date for EDP-compliant YAML, and who on the CBIIT side owns that requirement?
- Should the model version history entry name the driver of each change (internal vs submission) so effort can be reported from GitHub as well as Jira?

---

## 9. Document History

| Version | Date | Author | Summary of Changes |
|---|---|---|---|
| v1.0 | September 15, 2026 | Gina Kuffel (FNL) | Initial publication |

---

*Prepared by Frederick National Laboratory for Cancer Research (FNL) | Contractor to NCI/CBIIT*
