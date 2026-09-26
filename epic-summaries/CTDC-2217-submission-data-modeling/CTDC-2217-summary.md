# Epic Summary: CTDC Submission Data Modeling

**Epic:** CTDC-2217  
**Version:** v1.0  
**Date Prepared:** September 15, 2026  
**Status:** Open (evergreen epic)  
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

Every incoming submission to the Clinical and Translational Data Commons, whether a new study or an update to an existing one, brings its own fields, vocabularies, and data types. Before that data can be validated and loaded, the CTDC data model (the blueprint that defines what the commons can store) often has to be extended to represent it. This epic covers that work: working alongside the submitter as they move through the CRDC Submission Portal, identifying what the model cannot yet hold, requesting standardized Common Data Elements from NCI's Semantic Infrastructure team, and releasing an updated model so the submission can proceed.

The reason this matters is twofold. First, a submission cannot move forward until the model can represent it, so this work is frequently the gating step for onboarding new data. Second, doing it through governed NCI standards rather than one-off fixes is what allows data from the Cancer Moonshot Biobank, NCI-MATCH, and the CIMAC-CIDC immuno-oncology network to be queried side by side, and what keeps CTDC data Findable, Accessible, Interoperable, and Reusable (FAIR) across the broader CRDC.

Until September 2026 this work was tracked inside the Data Integration epic alongside loading and testing tasks, or occasionally under Internal Data Modeling, which made it hard to see how much modeling effort each submission actually required. This epic was created to separate it out. It is evergreen: it stays open for the life of the project, with one long-lived task per submission underneath it.

---

## 2. Scope & Objectives

### In Scope

- Gap analysis of an incoming submission against the current CTDC data model, worked with the submitter in the CRDC Submission Portal
- Node, relationship, property, CDE, and permissible value changes required by a specific submission
- Creation and maintenance of a CDE Request Workbook for each submission, and the associated caDSR help desk requests to the NCI Semantic Infrastructure team
- Model releases whose purpose is to unblock a submission, including diagram regeneration and coordination of downstream impact
- Model updates for each new version of a study that submits repeatedly

### Out of Scope

- Internally motivated model changes such as governance, SOP, repository structure, CDE hygiene, and application-driven properties (tracked under the Internal Data Modeling epic)
- Transformation, validation, ingestion, and testing of the submission itself (tracked under each study's submission epic; cross-study integration work sits under the Data Integration epic)
- Mock data generation, backend and OpenSearch implementation, application UI changes, and the ICDC data model, each owned elsewhere

---

## 3. Technical Context (Plain-English Glossary)

| Term | Plain-English Explanation |
|---|---|
| **Submission** | A new study, or an update to an existing study, delivered to CTDC through the CRDC Submission Portal. The submission is what triggers a model change under this epic. |
| **Submission Request Form (SRF)** | The form a submitter files in the CRDC Submission Portal to begin a submission. Every submission-driven model change starts here. |
| **Gap Analysis** | A field-by-field comparison of what the submitter is providing against the CTDC model to find what can be stored as-is, what needs a new property or value, and what needs a new node. |
| **CDE (Common Data Element)** | A standardized, reusable definition of a data field, registered in NCI's caDSR system. Binding a CTDC property to a CDE means it shares a definition with other NCI systems, which is what makes data comparable across the CRDC. |
| **CDE Request Workbook** | The per-submission spreadsheet that records every requested model change: the source field, the target CTDC element, the CDE (existing or requested), the allowed values, and the approval and release status. It is the system of record for what was modeled and why; the Jira ticket tracks only milestones. |
| **Semantic Infrastructure (SI) Team** | The NCI team that curates caDSR. They create new CDEs and add permissible values in response to CTDC's help desk requests. |
| **Data Concierge** | The CTDC team member who works directly with the submitter and owns the gap analysis, the workbook, the caDSR requests, and the model pull request for a given submission. |
| **Data Model Navigator** | The page on both the CRDC Submission Portal and CTDC where submitters and researchers can browse the current model. A release is verified by confirming the new version appears there. |
| **Memgraph** | The graph database that stores CTDC data in the shape the model defines. It replaced the historical Neo4j database. |

---

## 4. Current Status

This is an evergreen epic, so this document does not carry a point-in-time work breakdown or progress figures; they would be out of date within a sprint. Current child tasks, their status, and story points are always available in Jira under epic CTDC-2217 (one task per submission, named `Submission Data Modeling: <submission>`), and at the summary level on the CTDC engineering dashboard.

---

## 5. Diagrams & Visuals

The CIMAC-CIDC assay modeling work produced entity relationship diagrams that are stored with that program's documentation on SharePoint. The visual model diagram (`model-desc/ctdc-model.svg`) is regenerated with every model release and lives in the `CBIIT/ctdc-model` repository on the `prod` branch. No other diagrams or mockups are attached to this epic at the time of publication.

---

## 6. Document History

| Version | Date | Author | Summary of Changes |
|---|---|---|---|
| v1.0 | September 15, 2026 | Gina Kuffel (FNL) | Initial publication |

---

*Prepared by Frederick National Laboratory for Cancer Research (FNL) | Contractor to NCI/CBIIT*
