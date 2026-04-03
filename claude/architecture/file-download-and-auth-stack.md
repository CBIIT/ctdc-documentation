# CTDC File Download & Authorization Stack

**Document Type:** Leadership Overview
**Prepared by:** Gina Kuffel, Senior TPM, BACS/FNL
**Date:** April 3, 2026
**Version:** v1.0
**Project:** Clinical and Translational Data Commons (CTDC)
**Ecosystem:** Cancer Research Data Commons (CRDC) | NCI/CBIIT

---

## 1. Purpose

This document provides a concise, non-technical overview of how the Clinical and Translational Data Commons (CTDC) enables authorized users to download research files directly from the application. It describes the flow from the moment a user requests a file through to receipt of that file, and explains where CTDC connects to NIH's identity and authorization infrastructure — specifically the Data Commons Framework (DCF) Fence service.

This document is intended for leadership and program stakeholders. A companion technical architecture document covering the full engineering implementation is maintained separately in the CTDC engineering knowledge base.

---

## 2. Background

CTDC stores two categories of files. The majority are large scientific data files — BAM and FASTQ files from genomic sequencing, often several gigabytes in size — which require cloud-based compute environments to access and analyze. A second, smaller category consists of document-type files: study protocols, pathology reports, and data supplement files, typically under 12 MB.

| File Category | Access Method |
|---|---|
| **Large Scientific Files** (e.g., BAM, FASTQ) | Accessed via the My Files workflow. Users assemble a file cart, export a manifest, and submit it to a cloud compute environment such as Seven Bridges. Direct download is not available for these files due to size. |
| **Document Files** (PDFs, Word, Excel) | Files under 12 MB can be downloaded directly from the CTDC application with a single click. The download icon appears in the Access column of any applicable file listing. |

Direct file download was first introduced in the Bento platform ecosystem in release 3.2.0 (June 2021). The 12 MB threshold is enforced at the application level — files above that size do not display a download icon and show a tooltip directing users to the My Files workflow instead.

---

## 3. Integration Points: DCF Fence and NIH Login

CTDC's file download capability depends on two external NIH services for identity and access control. Understanding these integration points is important context for any discussions with NIH IT or DCF program staff.

### What Is DCF Fence?

The Data Commons Framework (DCF) Fence service is NIH's centralized OAuth2 identity and authorization broker for data commons applications. It manages user identity, login via NIH credentials or Login.gov, and issues access tokens that vouch for who a user is.

Think of Fence as the front door guard at NIH: it checks your badge before letting you into any data commons application.

### CTDC's Two Integration Points

**Integration Point 1 — User Login**

When a user logs into CTDC, the application redirects them to NIH's login page (managed by Fence). After the user authenticates — via NIH eRA Commons credentials or Login.gov — Fence issues an access token. CTDC uses this token to retrieve the user's profile and data access permissions, then stores them in a secure server-side session. Fence is only involved at this login step.

**Integration Point 2 — Email-Based IDP Detection**

CTDC distinguishes between two NIH sub-identity providers — NIH (`@nih.gov`) and Login.gov — based on the email address associated with the user's account. This classification is recorded in the user's session and audit log entries.

> ⚠️ **Known constraint:** Users who do not have an `@nih.gov` or `@login.gov` email in their NIH-linked profile will be unable to log in. This is a known limitation of the current identity integration.

After login is complete, Fence plays no further role in individual file download requests. All subsequent access decisions are made using the session data already established — a deliberate design choice that reduces dependency on Fence availability for every user action.

---

## 4. How a File Download Works

The following steps describe the complete flow when an authorized CTDC user clicks the download icon on a document-type file.

| Step | What Happens |
|---|---|
| **1** | The user clicks the download icon next to a file in CTDC. The application sends a request to the CTDC file delivery service, including the user's session cookie. |
| **2** | The file delivery service looks up the user's session — stored in a secure database — to confirm the user is logged in and to retrieve their access permissions (ACL). If no valid session is found, the request is rejected immediately. |
| **3** | The service checks whether the user's approved permissions cover the requested file. If the file is publicly accessible (marked as "open"), any logged-in user may proceed. If the file requires specific access approval, the user's permission list is checked. |
| **4** | Once access is confirmed, the service queries the CTDC database to retrieve the file's storage location — an internal reference to its location in AWS cloud storage. |
| **5** | The service generates a time-limited pre-signed URL — a secure, one-time access link — directly to the file in AWS S3 cloud storage. This link is valid for 24 hours. |
| **6** | The pre-signed URL is returned to the user's browser. The file transfers directly from AWS to the user's machine. The CTDC application server is not involved in the actual file transfer. |
| **7** | A download event record is written to the CTDC database capturing the user ID, email, identity provider, file name, and timestamp. This provides a full audit trail of all file download activity. |

---

## 5. Access Control Summary

CTDC enforces two levels of access control on file downloads:

| Access Level | Description |
|---|---|
| **Open Access Files** | Study protocols, supplemental documents, and other non-restricted files are tagged as "open" in the system. Any user who has logged in may download these files — no specific data access approval is required. Login alone is sufficient. |
| **Controlled Access Files** | Files associated with specific research arms or datasets may require formal data access approval. The system checks the user's approval status at the time of download. Only approvals with a status of "Approved" are honored — pending or expired requests do not grant access. |

Access permissions are retrieved from the CTDC backend at login time and stored in the session. Administrators have unconditional access to all files regardless of access level.

---

## 6. Audit and Compliance

Every file download generates an audit record in the CTDC database to support compliance reporting and data access monitoring. Each record captures:

| Field | Detail |
|---|---|
| **User Identity** | User ID and email address from the session |
| **Identity Provider** | NIH or Login.gov — how the user authenticated |
| **File Details** | File name, format, and size |
| **Timestamp** | Date and time of the download event |
| **Anonymous Indicator** | If no session is present at time of download, the event is recorded as "Anonymous User" |

---

## 7. Key Notes for Leadership

- **CTDC connects to DCF Fence only at login time.** After a user's session is established, Fence plays no role in individual file download requests. Download performance is not dependent on Fence availability after login.
- **Files are delivered directly from AWS cloud storage to the user's browser.** The CTDC application server does not stream file bytes — it only issues the secure access link.
- **The 24-hour expiry on pre-signed URLs is a configurable setting.** The production value should be reviewed periodically to balance user experience against security policy.
- **Users without an `@nih.gov` or `@login.gov` email domain will be unable to log in.** This is a known constraint of the current identity integration that may warrant discussion with NIH IT or DCF as the user base grows.
- **Controlled access files require an "Approved" status to download.** Pending access requests do not grant download rights.
- **Direct download is intentionally limited to files under 12 MB.** Larger scientific data files continue to require the My Files workflow and cloud compute access as designed.

---

## 8. Relationship to ICDC

CTDC and ICDC share the same underlying file delivery service (`bento-files`) and authentication service (`bento-auth`) as part of the shared Bento platform infrastructure. The key architectural difference is that **ICDC does not require user login** — all ICDC-downloadable files are open access, and ICDC uses DCF IndexD for file lookup via Globally Unique Identifiers (GUIDs) rather than Fence for authorization.

This document covers the CTDC configuration, which includes NIH login, session-based access control, and both open and controlled access file categories.

---

*CTDC is a project of the National Cancer Institute's Cancer Research Data Commons (CRDC) | Managed by BACS/FNL under NCI/CBIIT*
*Prepared by Gina Kuffel, Senior TPM, BACS/FNL — April 2026*
