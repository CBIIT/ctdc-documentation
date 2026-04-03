# CTDC File Download & Authorization Stack

**Document Type:** Leadership Overview
**Prepared by:** Gina Kuffel, Senior TPM, BACS/FNL
**Date:** April 3, 2026
**Version:** v1.2
**Project:** Clinical and Translational Data Commons (CTDC)
**Ecosystem:** Cancer Research Data Commons (CRDC) | NCI/CBIIT

> **Revision Note (v1.2):** This update resolves two open items from v1.1: (1) Google IDP status confirmed — Google login is **not used** in CTDC in any environment; (2) backend ACL review completed — source code inspection of `bento-backend-core` confirms that `crdc-ctdc-backend` does **not** perform independent ACL resolution. See Section 6 for the full architecture boundary explanation.

> **Revision Note (v1.1):** This document was updated based on a direct source code review of the CTDC authentication service (`crdc-ctdc-authn`), file delivery service (`crdc-ctdc-files`), and backend (`crdc-ctdc-backend`). Several details in v1.0 were carried over from ICDC documentation and did not accurately reflect CTDC's implementation. Key corrections are noted inline.

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

> ✅ **Confirmed (v1.2):** Google is **not** an active identity provider in CTDC. Although the authentication service codebase contains references to a Google IDP, this has been confirmed as not enabled in any CTDC environment — development, QA, staging, or production.

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

## 6. Architecture Boundary: What Each Service Is Responsible For

> ✅ **New in v1.2 — Based on source code review of `bento-backend-core`.**

A common question for this architecture is: *which service actually enforces who can see what data?* The answer involves a clear division of responsibility across three services. Understanding this boundary is important for security reviews, compliance assessments, and incident response.

### The Three Services and Their Roles

| Service | Repository | Role in Access Control |
|---|---|---|
| **Authentication Service** | `crdc-ctdc-authn` | The authority on user identity and permissions. Manages OAuth2 login, stores user sessions and ACLs in MySQL, and answers "is this user authenticated?" queries from other services. |
| **File Delivery Service** | `crdc-ctdc-files` | Enforces per-file access control. Checks the user's ACL against the file's access tier (open vs. controlled) before requesting a signed URL from DCF. |
| **Backend (GraphQL API)** | `crdc-ctdc-backend` | Provides data query access to the application's private GraphQL endpoint. Does **not** perform its own ACL resolution — delegates authentication entirely to the authentication service. |

### How the Backend Handles Authentication

The backend's access control is implemented in a component called `AuthenticationInterceptor` (part of the shared Bento platform core, `bento-backend-core`). This interceptor runs before every request to the private GraphQL API endpoint (`/v1/graphql/`).

Here is what it does — in plain English:

1. It checks whether authentication is enabled in the application configuration
2. It takes the browser cookies from the incoming request and forwards them to the authentication service's `/authenticated` endpoint
3. If the authentication service responds with HTTP 200 and confirms `status: true`, the request is allowed through to the GraphQL API
4. If the authentication service responds with anything else — wrong credentials, expired session, service unavailable — the backend returns a 401 Unauthorized error and stops the request

**What this means:** The backend acts as a gatekeeper, but it asks the authentication service to make the actual decision. The backend itself holds no user permission data and performs no ACL comparison. All of that logic lives in `crdc-ctdc-authn`.

This is a deliberate architectural pattern in the Bento framework: centralize identity and session management in the auth service, and have other services defer to it rather than each maintaining their own access logic.

---

## 7. Access Control Summary

CTDC enforces two levels of access control on file downloads:

| Access Level | Description |
|---|---|
| **Open Access Files** | Files tagged as "open" in the system ACL. Any user who has logged in and has a valid session may download these files — no specific data access approval is required. |
| **Controlled Access Files** | Files associated with specific research arms or datasets that require formal data access approval. The system checks the user's ACL at the time of download. Only an "Approved" status grants access — pending or expired requests do not. |

Access permissions are stored in the user's session at login time and retrieved from MySQL on each download request.

---

## 8. Audit and Compliance

> ⚠️ **Action required:** As noted in Section 5, the download event logging function is currently commented out in the CTDC file service. Until this is confirmed active or re-enabled, the audit trail described below may not be operational. This is a compliance risk that should be addressed.

When active, the intended audit record for each download captures:

| Field | Detail |
|---|---|
| **User Identity** | User ID and email address from the session |
| **Identity Provider** | NIH (eRA Commons) or Login.gov — how the user authenticated |
| **File Details** | File name, format, and size |
| **Timestamp** | Date and time of the download event |

---

## 9. Key Notes for Leadership

- **DCF is involved in every file download, not just login.** The CTDC file service retrieves the user's stored DCF token and calls DCF IndexD on every download request to obtain a signed URL. DCF availability is a dependency for file download functionality.
- **Files are delivered directly from AWS cloud storage to the user's browser.** The CTDC application server brokers the signed URL but does not stream file bytes.
- **There is no file size restriction on direct downloads in CTDC.** All registered file types — including large genomic and radiology files — are served through the same download pathway.
- **Users authenticate via NIH eRA Commons or Login.gov only.** Google is not an active identity provider in CTDC. Users without a valid `@nih.gov` or `@login.gov` email domain in their NIH profile cannot log in under the current configuration.
- **The backend does not perform its own ACL checks.** It delegates authentication decisions to the authentication service (`crdc-ctdc-authn`) via a cookie-forwarding pattern. All session and permission data lives in the auth service.
- **Audit logging for file downloads should be verified.** The audit event call is currently commented out in source code. This is a compliance gap that requires engineering follow-up.

---

## 10. Open Items Requiring Engineering Confirmation

| # | Question | Why It Matters | Status |
|---|---|---|---|
| 1 | Is the `storeDownloadEvent` audit log call intentionally disabled, or is this a bug? | Compliance risk — no download audit trail if disabled | ⚠️ Open |
| 2 | ~~Is the Google IDP active in any CTDC environment (dev, QA, stage, prod)?~~ | ~~User identity classification and access scope~~ | ✅ Resolved — Google IDP is not active in any environment |
| 3 | What is the production session timeout value? | User experience and security policy alignment | ⚠️ Open |
| 4 | ~~Has the backend (`crdc-ctdc-backend`) ACL resolution logic been reviewed for completeness?~~ | ~~Needed to confirm full access control picture~~ | ✅ Resolved — Backend delegates auth to `crdc-ctdc-authn` via `AuthenticationInterceptor`; performs no independent ACL resolution. See Section 6. |

---

## 11. Relationship to ICDC

CTDC and ICDC share the same underlying file delivery service (`bento-files`) and authentication service (`bento-auth`) as part of the shared Bento platform infrastructure. However, the two projects differ in important ways:

| | CTDC | ICDC |
|---|---|---|
| **Login required** | Yes — NIH eRA Commons or Login.gov | No — all downloadable files are open access |
| **File access control** | Open and controlled access tiers | Open access only |
| **DCF involvement** | Login + every file download (via IndexD) | IndexD for file lookup (GUIDs); no Fence auth |
| **File size restriction** | None | None |
| **Active identity providers** | NIH eRA Commons, Login.gov | N/A |

---

## 12. Document History

| Version | Date | Author | Summary of Changes |
|---|---|---|---|
| v1.0 | March 2026 | Gina Kuffel, FNL | Initial publication |
| v1.1 | April 3, 2026 | Gina Kuffel, FNL | Revised based on source code review of `crdc-ctdc-authn`, `crdc-ctdc-files`, `crdc-ctdc-backend`; corrected file size restriction and DCF involvement details carried over from ICDC docs |
| v1.2 | April 3, 2026 | Gina Kuffel, FNL | Resolved Open Items #2 and #4: confirmed Google IDP not active; completed backend ACL review — backend delegates to auth service via `AuthenticationInterceptor`, no independent ACL resolution. Added Section 6 (Architecture Boundary). |

---

*CTDC is a project of the National Cancer Institute's Cancer Research Data Commons (CRDC) | Managed by BACS/FNL under NCI/CBIIT*
*Prepared by Gina Kuffel, Senior TPM, BACS/FNL — April 2026*
*v1.2 — Google IDP confirmed inactive; backend ACL architecture boundary documented*
