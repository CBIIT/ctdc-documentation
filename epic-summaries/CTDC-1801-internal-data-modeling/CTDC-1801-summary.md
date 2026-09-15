# Epic Summary: CTDC Internal Data Modeling

**Epic:** CTDC-1801  
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

The CTDC data model is the blueprint that defines what kinds of information the Clinical and Translational Data Commons can hold (studies, participants, specimens, diagnoses, therapies, files) and how those pieces connect to one another. Every screen in the CTDC application, every search filter, and every incoming submission depends on that blueprint being accurate and consistent. This epic covers the ongoing work of maintaining the blueprint for reasons that come from inside the platform: keeping vocabulary aligned with NCI's Common Data Element (CDE) standards, adding fields the application needs, restructuring how the model repository is organized and governed, and fixing issues discovered by testing tools.

The reason this matters is trust. When the model drifts out of alignment with NCI standards, submitters run into unexpected validation errors, searches return inconsistent results, and comparing data across studies becomes unreliable. Disciplined model maintenance is what keeps CTDC data Findable, Accessible, Interoperable, and Reusable (FAIR) across the broader CRDC.

This is an evergreen epic. It stays open for the life of the CTDC project, and individual modeling efforts open and close underneath it as tasks. Model changes that are triggered by an incoming submission are tracked separately under the companion Submission Data Modeling epic.

---

## 2. Scope & Objectives

### In Scope

- Additions, changes, and retirements of nodes, relationships, and properties whose trigger is a platform, application, governance, or tooling need
- Alignment of model properties with caDSR Common Data Elements and cleanup of locally defined value lists that duplicate a governed CDE
- Maintenance of the model repository structure, continuous integration checks, the Data Model Contribution SOP, the Data Model Owner Guide, and the version history
- Regeneration of the visual model diagram and coordination of downstream impact (backend, search index, mock data, ingestion, submission templates) with each release
- Schema fixes surfaced by mock data generation, ingestion validation, or the application

### Out of Scope

- Model changes driven by an incoming submission (tracked under the Submission Data Modeling epic)
- Transformation, validation, ingestion, and testing of submitted data (tracked under the Data Integration epic)
- Mock data generation, backend and OpenSearch implementation, application UI changes, and the ICDC data model, each owned elsewhere

---

## 3. Technical Context (Plain-English Glossary)

| Term | Plain-English Explanation |
|---|---|
| **Data Model** | The structured definition of what CTDC can store and how the pieces relate. Written as two YAML text files in the `CBIIT/ctdc-model` GitHub repository. Think of it as the floor plan for the data. |
| **Node / Property / Relationship** | A node is a kind of thing (a participant, a specimen). A property is a fact about that thing (age, tissue type). A relationship is how two things connect (this specimen came from this participant). |
| **CDE (Common Data Element)** | A standardized, reusable definition of a data field, registered in NCI's caDSR system. Binding a CTDC property to a CDE means it uses the same definition and allowed values as other NCI systems, which is what makes data comparable across the CRDC. |
| **Permissible Values** | The list of allowed answers for a field (for example, the accepted names of tumor grades). Governed by caDSR when a CDE is bound; defined locally otherwise. |
| **Semantic Versioning** | The numbering scheme for model releases (MAJOR.MINOR.PATCH). A major bump signals a change that could break something downstream; minor and patch bumps are backward compatible. |
| **Memgraph** | The graph database that stores CTDC data in the shape the model defines. It replaced the historical Neo4j database. |
| **OpenSearch** | The search and aggregation index that powers CTDC filters and counts. Which fields are searchable is determined by the model. |
| **Contribution SOP** | The standard operating procedure every model change must follow: branch from `develop`, pass automated checks, match the release tag to the version line in the model file, regenerate the diagram, and record the change in the version history. |

---

## 4. Current Status

This is an evergreen epic, so this document does not carry a point-in-time work breakdown or progress figures; they would be out of date within a sprint. Current child tasks, their status, and story points are always available in Jira under epic CTDC-1801, and at the summary level on the CTDC engineering dashboard.

---

## 5. Diagrams & Visuals

The visual model diagram (`model-desc/ctdc-model.svg`) is regenerated with every model release and lives in the `CBIIT/ctdc-model` repository on the `prod` branch. No additional architecture diagrams or mockups are attached to this epic at the time of publication.

---

## 6. Document History

| Version | Date | Author | Summary of Changes |
|---|---|---|---|
| v1.0 | September 15, 2026 | Gina Kuffel (FNL) | Initial publication |

---

*Prepared by Frederick National Laboratory for Cancer Research (FNL) | Contractor to NCI/CBIIT*
