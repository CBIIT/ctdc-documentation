# CTDC File Download & Authorization Stack

**Document Type:** Leadership Overview
**Prepared by:** Gina Kuffel, Senior TPM, BACS/FNL
**Date:** April 3, 2026
**Version:** v1.1
**Project:** Clinical and Translational Data Commons (CTDC)
**Ecosystem:** Cancer Research Data Commons (CRDC) | NCI/CBIIT

> **Revision Note (v1.1):** This document has been updated based on a direct source code review of the CTDC authentication service (`crdc-ctdc-authn`), file delivery service (`crdc-ctdc-files`), and backend (`crdc-ctdc-backend`). Several details in v1.0 were carried over from ICDC documentation and did not accurately reflect CTDC's implementation. Key corrections are noted inline.

---

## 1. Purpose

This document provides a concise, non-technical overview of how the Clinical and Translational Data Commons (CTDC) enables authorized users to download research files directly from the application. It describes the flow from the moment a user requests a file through to receipt of that file, and explains where CTDC connects to NIH's identity and authorization infrastructure — specifically the Data Commons Framework (DCF) services.

This document is intended for leadership and program stakeholders. A companion technical architecture document covering the full engineering implementation is maintained separately in the CTDC engineering knowledge base.

---

## 2. Background

CTDC stores scientific research files spanning multiple data types — including genomic sequencing files (BAM, FASTQ), radiology images (DICOM), variant call files (VCF), and document-type files (PDFs, study protocols, data supplements). All files registered in CTDC are accessible through the file download workflow.

| File Category | Examples | Access Method |
|---|---|---|
| **Genomic Data Files** | BAM, FASTQ | Direct download via the CTDC file service using a DCF-issued signed URL |
| **Radiology Images** | DICOM | Direct download via the CTDC file service using a DCF-issued signed URL |
| **Variant Files** | VCF | Direct download via the CTDC file service using a DCF-issued signed URL |
| **Document Files** | PDF, study protocols, data supplements | Direct download via the CTDC file service using a DCF-issued signed URL |

> ⚠️ **Correction from v1.0:** The previous version of this document stated that direct download was limited to files under 12 MB and that large scientific files required a separate "My Files" manifest workflow. This was carried over from ICDC documentation and does not reflect CTDC's implementation. CTDC's file service applies no file size restriction — all registered file types are handled through the same download pathway.

---

## 3. Identity and Authentication: NIH Login via eRA Commons

CTDC uses NIH's centralized OAuth2 login infrastructure for user authentication. Users log in using their **NIH eRA Commons credentials** or **Login.gov** account — these are the two supported identity providers (IDPs) in the current implementation.

### What Happens at Login

When a user clicks "Login" in CTDC, the application redirects them to NIH's login page. After the user authenticates, NIH issues an access token. CTDC uses this token to:

1. Retrieve the user's profile information (name, email)
2. Classify the user's identity provider based on their email domain (`@nih.gov` → NIH; `@login.gov` → Login.gov)
3. Store the user's session — including their access token and data access permissions — in a secure MySQL database

The session remains active for **30 minutes** of inactivity by default. After that, the user is required to log in again.

> ⚠️ **Known constraint:** Users whose NIH-linked account does not carry an `@nih.gov` or `@login.gov` email address will be unable to log in. This is a known limitation of the current identity integration.

> ⚠️ **Open question for leadership:** The authentication service codebase also includes support for a Google identity provider. It should be confirmed with the engineering team whether Google login is active in any CTDC environment (dev, QA, staging, or production).

---

## 4. How DCF Is Involved: More Than Just Login

> ⚠️ **Correction from v1.0:** The previous version stated that DCF Fence was only involved at login time and played no role in individual file download requests. This was incorrect. DCF is actively involved in **every file download**, as described below.

The Data Commons Framework (DCF) is NIH's infrastructure for managing access to controlled research data across data commons applications. CTDC uses two DCF components:

- **DCF Fence** — handles OAuth2 login and issues access tokens
- **DCF IndexD** — a file registry service that maps a file's unique identifier (GUID) to its actual location in cloud storage, and issues time-limited signed URLs

Think of DCF IndexD as a library catalog: it knows where every file lives, and when a user is authorized to access one, it hands them a temporary key to retrieve it directly.

### DCF's Role in Every File Download

When a user clicks the download icon on a file in CTDC, the file delivery service:

1. Retrieves the user's DCF access token from their stored session in MySQL
2. Uses that token to call the DCF IndexD endpoint with the file's GUID
3. DCF validates the token and returns a **signed URL** — a secure, time-limited link directly to the file in cloud storage
4. CTDC passes that signed URL back to the user's browser, which initiates the download directly from cloud storage

This means that if DCF is unavailable, file downloads will fail — even for users who are already logged in.

---

## 5. How a File Download Works — Step by Step

| Step | What Happens |
|---|---|
| **1** | The user clicks the download icon next to a file in CTDC. The browser sends a request to the CTDC file delivery service, including the user's session cookie. |
| **2** | The file delivery service extracts the session ID from the cookie and queries the MySQL session database to retrieve the user's stored DCF access token. If no valid session or token is found, the request is rejected. |
| **3** | The service checks the user's Access Control List (ACL) against the file's ACL. If the file is tagged as "open," any authenticated user may proceed. If the file requires specific data access approval, the user's permission list is checked — only an "Approved" status grants access. |
| **4** | With access confirmed, the file service calls the DCF IndexD endpoint, passing the file's GUID and the user's Bearer token. DCF validates the token and returns a signed URL to the file in AWS cloud storage. |
| **5** | The signed URL is returned to the user's browser. The file transfers directly from AWS cloud storage to the user's machine. The CTDC application server is not involved in the actual file transfer. |
| **6** | *(See note below regarding audit logging.)* |

> ⚠️ **Audit logging status unclear:** The download event logging call (`storeDownloadEvent`) is currently **commented out** in the file service source code. It is not confirmed whether audit records are being written for file download activity. This should be verified with the engineering team and resolved before any compliance review.

---

## 6. Access Control Summary

CTDC enforces two levels of access control on file downloads:

| Access Level | Description |
|---|---|
| **Open Access Files** | Files tagged as "open" in the system ACL. Any user who has logged in and has a valid session may download these files — no specific data access approval is required. |
| **Controlled Access Files** | Files associated with specific research arms or datasets that require formal data access approval. The system checks the user's ACL at the time of download. Only an "Approved" status grants access — pending or expired requests do not. |

Access permissions are stored in the user's session at login time and retrieved from MySQL on each download request.

---

## 7. Audit and Compliance

> ⚠️ **Action required:** As noted in Section 5, the download event logging function is currently commented out in the CTDC file service. Until this is confirmed active or re-enabled, the audit trail described below may not be operational. This is a compliance risk that should be addressed.

When active, the intended audit record for each download captures:

| Field | Detail |
|---|---|
| **User Identity** | User ID and email address from the session |
| **Identity Provider** | NIH (eRA Commons) or Login.gov — how the user authenticated |
| **File Details** | File name, format, and size |
| **Timestamp** | Date and time of the download event |

---

## 8. Key Notes for Leadership

- **DCF is involved in every file download, not just login.** The CTDC file service retrieves the user's stored DCF token and calls DCF IndexD on every download request to obtain a signed URL. DCF availability is a dependency for file download functionality.
- **Files are delivered directly from AWS cloud storage to the user's browser.** The CTDC application server brokers the signed URL but does not stream file bytes.
- **There is no file size restriction on direct downloads in CTDC.** All registered file types — including large genomic and radiology files — are served through the same download pathway.
- **Users authenticate via NIH eRA Commons or Login.gov.** Users without a valid `@nih.gov` or `@login.gov` email domain in their NIH profile cannot log in under the current configuration.
- **Audit logging for file downloads should be verified.** The audit event call is currently commented out in source code. This is a compliance gap that requires engineering follow-up.
- **The Google identity provider is present in the authentication service codebase.** Whether it is active in production should be confirmed.

---

## 9. Open Items Requiring Engineering Confirmation

| # | Question | Why It Matters |
|---|---|---|
| 1 | Is the `storeDownloadEvent` audit log call intentionally disabled, or is this a bug? | Compliance risk — no download audit trail if disabled |
| 2 | Is the Google IDP active in any CTDC environment (dev, QA, stage, prod)? | User identity classification and access scope |
| 3 | What is the production session timeout value? | User experience and security policy alignment |
| 4 | Has the backend (`crdc-ctdc-backend`) ACL resolution logic been reviewed for completeness? | Needed to confirm full access control picture |

---

## 10. Relationship to ICDC

CTDC and ICDC share the same underlying file delivery service (`bento-files`) and authentication service (`bento-auth`) as part of the shared Bento platform infrastructure. However, the two projects differ in important ways:

| | CTDC | ICDC |
|---|---|---|
| **Login required** | Yes — NIH eRA Commons or Login.gov | No — all downloadable files are open access |
| **File access control** | Open and controlled access tiers | Open access only |
| **DCF involvement** | Login + every file download (via IndexD) | IndexD for file lookup (GUIDs); no Fence auth |
| **File size restriction** | None | None |

---

*CTDC is a project of the National Cancer Institute's Cancer Research Data Commons (CRDC) | Managed by BACS/FNL under NCI/CBIIT*
*Prepared by Gina Kuffel, Senior TPM, BACS/FNL — April 2026*
*v1.1 — Revised based on source code review of crdc-ctdc-authn, crdc-ctdc-files, crdc-ctdc-backend*
