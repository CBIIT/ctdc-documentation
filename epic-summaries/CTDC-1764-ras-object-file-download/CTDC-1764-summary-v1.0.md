# Epic Summary: Bento Core — RAS-Enabled Object File Download

**Epic:** CTDC-1764  
**Version:** v1.0  
**Date Prepared:** April 2, 2026  
**Status:** In Progress — Tasks On Hold Pending Architecture Decisions  
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

The Cancer Research Data Commons (CRDC) is building a capability that will allow researchers to download controlled-access data files directly to their own computers — securely, and without requiring them to manually navigate complex authentication systems. Today, accessing protected genomics and clinical data files requires manual credential steps that can be a barrier to research. This epic addresses that gap.

The solution is built around NIH's Researcher Auth Service (RAS) — a centralized NIH system that verifies who a researcher is and what data they are permitted to access, based on their approved study agreements (called consent groups). Think of RAS as a secure digital passport: it confirms your identity and lists exactly which datasets you are authorized to see or download. The CTDC application will use these passports to automatically verify authorization before allowing a file download to proceed.

This capability is being developed at the Bento Framework level — meaning it will benefit not just CTDC, but also the Cancer Cohort Data Commons (C3DC) and the Population Sciences Data Commons (PopSci). The expected outcome is a streamlined, standards-compliant download experience for researchers, underpinned by robust access controls that protect participant privacy and satisfy federal data governance requirements.

As of this writing, architecture decisions are pending and most implementation tasks are currently On Hold. Design-phase tasks (architecture diagrams) have been completed by the backend team.

---

## 2. Scope & Objectives

### In Scope

- Develop a new backend microservice within the Bento Framework to orchestrate RAS passport acquisition and validation
- Implement GA4GH DRS (Data Repository Service) integration to resolve file download URLs using DCF (Data Commons Framework)
- Integrate NIH RAS for authentication and authorization — verifying researcher identity and consent-group eligibility before permitting downloads
- Enforce consent-group-level access controls at both the data display and file download layers
- Create architecture diagrams documenting the RAS passport flow, the DRS download flow, and the consent-group enforcement model
- Design must be reusable across CTDC, C3DC, and PopSci (Bento Framework shared capability)

### Out of Scope

- Command-line bulk download tooling (under separate consideration at the GDC level; CRDC will likely adopt that solution when available)
- Anonymous / open-access file download (this epic covers controlled-access data only)
- Changes to dbGaP consent group assignment processes (managed by dbGaP, not CTDC)
- ICDC-specific implementation — ICDC open-access data handling is a separate workstream

---

## 3. Technical Context (Plain-English Glossary)

| Term | Plain-English Explanation |
|---|---|
| **RAS** | Researcher Auth Service — NIH's centralized system for verifying researcher identity and data access permissions. Issues a digital 'passport' that lists what datasets a researcher may access. |
| **Passport / Visa** | A digitally signed token (like a secure ID badge) issued by RAS. The 'passport' is the outer container; 'visas' inside it list specific dataset permissions. Both expire after a short time for security. |
| **DCF** | Data Commons Framework — the system (run by the University of Chicago) that validates passports and generates temporary signed download URLs for protected files. |
| **DRS** | Data Repository Service — a GA4GH (Global Alliance for Genomics and Health) standard API that resolves a file identifier (GUID) into an actual download URL. |
| **Consent Group** | A classification assigned by dbGaP when a study is registered. Each participant belongs to one consent group. These groups define what a researcher can do with the data (e.g., General Research Use). Access control in this epic enforces these boundaries. |
| **Bento Framework** | The shared software foundation underlying CTDC, ICDC, C3DC, and other CRDC data commons. Building this capability at the Bento level means all commons benefit from a single implementation. |

---

## 4. Work Breakdown

All work items are tracked as Tasks in Jira under epic CTDC-1764. Story points have not been estimated for this epic at this time.

| Ticket | Summary | Type | Status | Assignee |
|---|---|---|---|---|
| CTDC-1765 | Create a diagram for obtaining and processing the RAS passport | Task | **Closed** | Adam Davenport |
| CTDC-1766 | Create a diagram for using the RAS passport to download a file from DCF | Task | **Closed** | Adam Davenport |
| CTDC-1767 | Create a diagram for a Bento installation using the RAS passport to regulate what data is displayed | Task | On Hold | Yizhen Chen |
| CTDC-1768 | Integrate authn and authz with RAS and RAS test bed | Task | Open | Yizhen Chen |
| CTDC-1769 | Support authz through consent groups at the study and participant level to control data displayed | Task | On Hold | Yizhen Chen |
| CTDC-1770 | Support authz through consent groups to enable file download | Task | On Hold | Yizhen Chen |

---

## 5. Progress Summary

| Metric | Value |
|---|---|
| **Total Tickets** | 6 |
| **Closed (Done)** | 2 (CTDC-1765, CTDC-1766) |
| **On Hold** | 3 (CTDC-1767, CTDC-1769, CTDC-1770) |
| **Open / Not Started** | 1 (CTDC-1768) |
| **Blocked** | 0 |
| **Completion Rate** | 33% (2 of 6 tickets closed) |
| **Story Points** | Not estimated |
| **Sprint Association** | Not currently assigned to an active sprint — implementation pending architecture decisions |

---

## 6. Risks & Blockers

| Ticket | Issue | Impact | Mitigation / Owner |
|---|---|---|---|
| CTDC-1767, CTDC-1769, CTDC-1770 | Three implementation tasks are On Hold pending architectural decisions about how RAS passports will be processed and how consent-group enforcement will be wired into the Bento Framework. | No implementation progress can be made on the data display or file download layers until the architecture is finalized. Risk of scope creep if decisions are delayed. | Architecture design document (CTDC-2006) is assigned to Yizhen Chen (Tech Lead). TPM (Gina Kuffel, FNL) to track and escalate if timeline slips. |
| CTDC-1768 | RAS and RAS test bed integration not yet started. Dependency on availability of a functioning RAS staging environment and test accounts. | Downstream tasks cannot begin until authentication is in place. | DCF/Gen3 team is the external dependency. Contact: support@gen3.org. Yizhen Chen (Tech Lead) to coordinate. |
| Epic | No shared Slack channel or active monitoring exists between CTDC and the Gen3/DCF team for auth service outages. | Silent auth failures in non-prod environments could go undetected during development. | Establish a monitoring contact or shared channel with Gen3 before integration testing begins. |

---

## 7. Diagrams & Visuals

Two architecture diagrams were produced as deliverables of the design phase (CTDC-1765 and CTDC-1766) and are attached to those Jira tickets. A third diagram (CTDC-1767 — consent-group-driven data display) is currently On Hold.

- **Diagram 1 (CTDC-1765):** Flow for obtaining and processing the RAS passport — illustrates how the application requests a researcher's passport from NIH RAS and validates it.
- **Diagram 2 (CTDC-1766):** Flow for using the RAS passport to download a file from DCF — illustrates how a validated passport is presented to DCF in exchange for a signed, time-limited download URL.
- **Diagram 3 (CTDC-1767):** On Hold — will document how the RAS passport governs what data is displayed to a researcher based on their consent group memberships.

Diagrams are stored in Jira. Contact the CTDC Technical Lead (Yizhen Chen) or TPM (Gina Kuffel, FNL) to obtain copies.

---

## 8. Next Steps & Open Questions

### Immediate Next Steps

- Finalize the Bento Framework architecture design document (CTDC-2006, assigned to Yizhen Chen) — this is the gate for all On Hold implementation tasks
- Resume CTDC-1767 (consent-group data display diagram) once architecture decisions are captured
- Establish a coordination channel with the DCF/Gen3 team before integration testing for CTDC-1768 begins
- Confirm with the DCF team whether RAS will accept empty passports for open-access scenarios (relevant to ICDC; noted for awareness)

### Open Questions

- Will command-line bulk download (being evaluated at the GDC) be adopted by CRDC? If so, does this epic's scope need to expand?
- Mutual TLS (a way two systems prove their identity to each other using certificates) is being considered as an additional security layer — has a decision been made on whether CTDC will implement this?
- What is the expected timeline for architecture sign-off on CTDC-2006?

---

## 9. Document History

| Version | Date | Author | Summary of Changes |
|---|---|---|---|
| v1.0 | April 2, 2026 | Gina Kuffel (FNL) | Initial publication |

---

*Prepared by Frederick National Laboratory for Cancer Research (FNL) | Contractor to NCI/CBIIT*
