# File Download Capability: Authorized File Delivery via CRDC IndexD
## CTDC Epic Summary — CTDC-1764

**Document Type:** Leadership Overview — Epic Summary
**Prepared by:** Gina Kuffel, Senior TPM, BACS/FNL
**Date:** April 6, 2026
**Version:** v1.0
**Project:** Clinical and Translational Data Commons (CTDC)
**Ecosystem:** Cancer Research Data Commons (CRDC) | NCI/CBIIT

> **Companion document:** For full engineering detail, see [`claude/architecture/file-download-and-auth-stack.md`](../../claude/architecture/file-download-and-auth-stack.md) (currently at v1.5).
>
> **SharePoint:** The stakeholder `.docx` version of this summary is stored in the **CTDC Epic Summaries** folder in SharePoint. Current published version: `CTDC_FileDownload_LeadershipOverview_v1_0.docx`.

---

## 1. Purpose

This document provides a concise, non-technical overview of how the Clinical and Translational Data Commons (CTDC) enables authorized users to download research files directly from the application. It describes the flow from the moment a user requests a file through to receipt of that file, and explains where CTDC connects to NCI's Cancer Research Data Commons (CRDC) identity and authorization infrastructure — specifically the CRDC Fence and CRDC IndexD services.

CTDC requires users to log in before downloading files. Access is enforced at the individual file level — some files are open to any authenticated user, while others require formal data access approval. This document is intended for leadership and program stakeholders. A companion technical architecture document is maintained separately in the CTDC engineering knowledge base.

---

## 2. Background

CTDC stores scientific research files spanning multiple data types. Unlike some other data commons, CTDC applies no file size restriction on direct downloads — all registered file types, including large genomic and radiology files, are served through the same download pathway.

| File Category | Examples | Access Method |
|---|---|---|
| **Genomic Data Files** | BAM, FASTQ | Direct download via the CTDC file service using a CRDC-issued signed URL |
| **Radiology Images** | DICOM | Direct download via the CTDC file service using a CRDC-issued signed URL |
| **Variant Files** | VCF | Direct download via the CTDC file service using a CRDC-issued signed URL |
| **Document Files** | PDF, study protocols, data supplements | Direct download via the CTDC file service using a CRDC-issued signed URL |

---

## 3. Integration Points: CRDC Fence and CRDC IndexD

CTDC connects to two NCI Cancer Research Data Commons (CRDC) infrastructure services in the course of authenticating users and delivering files. Both are part of the same CRDC platform that CTDC belongs to, maintained by NCI/CBIIT — they are not external third-party services.

| Service | Role in CTDC |
|---|---|
| **CRDC Fence** | Handles OAuth2 login and issues access tokens. When a user clicks "Login" in CTDC, they are redirected to the NIH login page brokered by CRDC Fence. After the user authenticates, Fence issues an access token that CTDC stores in the user's session. Fence is the entry point for all CTDC authentication. |
| **CRDC IndexD** | A centralized file registry that maps every file's unique identifier (GUID) to its current location in cloud storage, and issues time-limited signed URLs for authorized downloads. Think of IndexD as the library catalog for CRDC research files: when a user is authorized to access a file, IndexD hands them a temporary key to retrieve it directly from cloud storage. CRDC IndexD is involved in every file download — not just at login. |

Because CRDC IndexD is called on every download request, CRDC service availability is a dependency for file download functionality. If CRDC services are unavailable, downloads will fail even for users who are already logged in.

---

## 4. How a File Download Works

The following steps describe the complete flow when an authorized user clicks the download icon on a file in CTDC.

| Step | What Happens |
|---|---|
| **1** | The user logs in to CTDC via NIH eRA Commons, brokered by CRDC Fence. After authentication, the user's access token and data access permissions are stored in a secure session database. The session remains active for 30 minutes of inactivity by default. |
| **2** | The user clicks the download icon next to a file in CTDC. The browser sends a request to the CTDC file delivery service, including the user's session cookie. |
| **3** | The file delivery service retrieves the user's stored CRDC access token from the session database. If no valid session or token is found, the request is rejected. |
| **4** | The service checks the file's access tier against the user's permissions. If the file is open access, any authenticated user may proceed. If the file is controlled access, only users with an "Approved" data access status may proceed — pending requests do not grant access. |
| **5** | With access confirmed, the file service calls CRDC IndexD, passing the file's unique identifier (GUID) and the user's access token. CRDC IndexD validates the token and returns a signed URL — a secure, time-limited link — directly to the file in AWS cloud storage. |
| **6** | The signed URL is returned to the user's browser. The file transfers directly from AWS cloud storage to the user's machine. The CTDC application server is not involved in the actual file transfer. |

---

## 5. Access Model

CTDC enforces two levels of access control on file downloads.

| Access Control | Description |
|---|---|
| **Authentication** | Required. Users must log in via NIH eRA Commons, brokered by CRDC Fence. There is one login entry point. Login.gov is not a separate login option — it is a federated identity mechanism within NIH's login infrastructure. |
| **Open Access Files** | Files tagged as open access may be downloaded by any user with a valid login. No specific data access approval is required. |
| **Controlled Access Files** | Files associated with specific research arms or datasets require formal data access approval. The system checks the user's access control list at download time. Only an "Approved" status grants access — pending or expired requests do not. |
| **File Size Restriction** | None. CTDC applies no file size limit on direct downloads. All registered file types — including large genomic and radiology files — are served through the same download pathway. |
| **Session Timeout** | User sessions default to 30 minutes of inactivity. After expiry, the user must log in again. The session timeout is configurable per environment. |

---

## 6. Audit and Compliance

The CTDC file delivery service is designed to write an audit record for every file download. Each record captures the following information and is stored in the CTDC database to support compliance reporting and data access monitoring.

> ⚠️ **Note:** As of v1.0 of this document, the audit logging function is fully implemented in the codebase but is currently disabled at the call site. A deliberate engineering action is required to re-enable it. This represents an open compliance gap. See the companion architecture document for full details.

| Field | Detail |
|---|---|
| **User ID** | Internal user identifier from the session |
| **Email Address** | User's email address from the session |
| **Identity Provider** | The session's identity classification, determined at login time by email domain (NIH or Login.gov pathway) |
| **File GUID** | The globally unique identifier for the file as registered in CRDC IndexD |
| **File Name** | Human-readable file name |
| **File Format** | File type (e.g., BAM, FASTQ, VCF, DICOM, PDF) |
| **File Size** | Size of the downloaded file |

---

## 7. Relationship to ICDC

CTDC and the Integrated Canine Data Commons (ICDC) share the same underlying file delivery and authentication services as part of the shared Bento platform infrastructure. However, the two projects differ in several important ways relevant to access control and CRDC service involvement.

| | CTDC | ICDC |
|---|---|---|
| **Login required** | Yes — NIH eRA Commons via CRDC Fence | No — all downloadable files are open access |
| **File access control** | Open and controlled access tiers | Open access only |
| **CRDC service involvement** | CRDC Fence (login) + CRDC IndexD (every download) | CRDC IndexD for file lookup only; no Fence authentication |
| **File size restriction** | None | None |
| **Active identity providers** | NIH eRA Commons (sole login entry point); session classified as NIH or Login.gov by email domain post-authentication | N/A (no login required) |
| **Files delivered from** | AWS cloud storage directly to the user's browser (CTDC server brokers the signed URL only) | AWS cloud storage directly to the user's browser (ICDC server brokers the signed URL only) |

---

## 8. Key Notes for Leadership

- **CRDC infrastructure is involved in every file download, not just login.** The CTDC file service retrieves the user's stored CRDC token and calls CRDC IndexD on every download request to obtain a signed URL. CRDC service availability is a dependency for file download functionality.
- **There is one login entry point: NIH eRA Commons, brokered by CRDC Fence.** Login.gov is not a separate login option in the CTDC application — it is a federated identity mechanism within NIH's login infrastructure. Google is not an active identity provider in any CTDC environment.
- **Files are delivered directly from AWS cloud storage to the user's browser.** The CTDC application server brokers the signed URL but does not stream file bytes.
- **There is no file size restriction on direct downloads in CTDC.** All registered file types — including large genomic and radiology files — are served through the same download pathway.
- **CTDC enforces two access tiers:** open access (any authenticated user) and controlled access (requires formal data access approval with an "Approved" status). Pending access requests do not grant download rights.
- **Users without a valid @nih.gov or @login.gov email domain** in their NIH profile cannot log in under the current configuration. This is a known limitation of the identity integration.
- **Session timeout defaults to 30 minutes of inactivity.** The actual production value is configurable per environment.
- **⚠️ Audit logging is currently disabled.** The infrastructure exists and is implemented, but has been switched off at the code level. Re-enabling it requires a deliberate engineering action.

---

## 9. Open Items

| # | Question | Status |
|---|---|---|
| 1 | Is the audit logging call intentionally disabled, or was it disabled during a migration and not re-enabled? | ⚠️ Open — engineering decision required |
| 2 | Is `SESSION_TIMEOUT` set in the production deployment configuration, and if so, what is the value? | ⚠️ Open — production deployment config review needed |

---

## 10. Document History

| Version | Date | Author | Summary of Changes |
|---|---|---|---|
| v1.0 | April 6, 2026 | Gina Kuffel, FNL | Initial publication — converted from `CTDC_FileDownload_LeadershipOverview_v1_0.docx`; establishes .md as canonical source for this epic summary |

---

*CTDC is a project of the National Cancer Institute's Cancer Research Data Commons (CRDC) | Managed by BACS/FNL under NCI/CBIIT*
*Prepared by Gina Kuffel, Senior TPM, BACS/FNL — April 2026*
