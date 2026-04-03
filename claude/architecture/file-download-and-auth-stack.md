# CTDC File Download & Authorization Stack

**Document Type:** Leadership Overview
**Prepared by:** Gina Kuffel, Senior TPM, BACS/FNL
**Date:** April 3, 2026
**Version:** v1.3
**Project:** Clinical and Translational Data Commons (CTDC)
**Ecosystem:** Cancer Research Data Commons (CRDC) | NCI/CBIIT

> **Revision Note (v1.3):** This update resolves the two remaining open items from v1.2: (1) audit logging status fully characterized from source code — `storeDownloadEvent` is implemented but deliberately disabled at the call site; (2) session timeout default confirmed as 30 minutes from source, with a note that the production value may be overridden by deployment configuration.

> **Revision Note (v1.2):** Resolved Google IDP status (not active in any environment) and completed backend ACL review — `crdc-ctdc-backend` delegates authentication to `crdc-ctdc-authn` via `AuthenticationInterceptor`. Added Section 6 (Architecture Boundary).

> **Revision Note (v1.1):** Revised based on source code review of `crdc-ctdc-authn`, `crdc-ctdc-files`, and `crdc-ctdc-backend`. Corrected file size restriction and DCF involvement details carried over from ICDC documentation.

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

The session timeout defaults to **30 minutes** of inactivity. After that, the user is required to log in again. See Section 8 for details on session timeout configuration.

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
| **6** | A download event record — capturing user identity, IDP, file name, format, and size — is intended to be written to the audit log at this point. **This step is currently disabled in the deployed code.** See Section 8 for details. |

---

## 6. Architecture Boundary: What Each Service Is Responsible For

> ✅ **Added in v1.2 — Based on source code review of `bento-backend-core`.**

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

## 8. Audit, Compliance, and Session Configuration

### 8a. Audit Logging — Status and Details

> ⚠️ **Action required (v1.3 update):** Source code review of `crdc-ctdc-files` has fully characterized the audit logging situation. The `storeDownloadEvent` function is **fully implemented** — the infrastructure exists and is wired — but it has been **deliberately disabled at the call site**. Both the `require` import and the function call in `routes/files.js` are commented out. This is not an incomplete feature; it is a functioning capability that has been switched off. A deliberate engineering decision is needed to re-enable it.

**What the audit system does when active:**

The `storeDownloadEvent` function (in `neo4j/neo4j-operations.js`) captures the following fields for each download event and writes them to a **Neo4j graph database** via the shared `bento-event-logging` module:

| Field | Detail |
|---|---|
| **User ID** | Internal user identifier from the session |
| **Email Address** | User's email address from the session |
| **Identity Provider** | NIH (eRA Commons) or Login.gov — how the user authenticated |
| **File ID** | The file's GUID (globally unique identifier) |
| **File Name** | Human-readable file name |
| **File Format** | File type (e.g., BAM, FASTQ, VCF, PDF) |
| **File Size** | Size of the downloaded file |

Note that audit records are written to **Neo4j**, not MySQL. These are separate data stores: MySQL holds user sessions and ACLs; Neo4j holds the download event audit trail. Both must be operational for the full system to function as designed.

The function also handles the case where a user is not logged in — it records the download as attributed to an anonymous user rather than failing silently.

**Recommended action:** Engineering should confirm whether the audit logging call was intentionally disabled and, if so, document the reason. If it was disabled during a migration or refactoring and not re-enabled, it should be restored before any compliance review.

---

### 8b. Session Timeout Configuration

> ✅ **Confirmed from source (v1.3):** The session timeout default is **30 minutes**, set in `config.js` of `crdc-ctdc-authn`.

The timeout is configured as follows:
- **Default (code):** 30 minutes — hardcoded as the fallback value
- **Override:** The `SESSION_TIMEOUT` environment variable can be set at deployment time to specify a different value in seconds; if set, it takes precedence over the 30-minute default

**What this means in practice:** The 30-minute default applies unless the deployment configuration for a given environment (dev, QA, stage, prod) explicitly sets `SESSION_TIMEOUT`. The actual production timeout requires confirmation from the deployment configuration — it may differ from the code default.

**Recommended action:** Confirm with the engineering team whether `SESSION_TIMEOUT` is set in the production environment and what value is in use. This is relevant for both user experience (how often researchers are prompted to re-authenticate) and security policy alignment.

---

## 9. Key Notes for Leadership

- **DCF is involved in every file download, not just login.** The CTDC file service retrieves the user's stored DCF token and calls DCF IndexD on every download request to obtain a signed URL. DCF availability is a dependency for file download functionality.
- **Files are delivered directly from AWS cloud storage to the user's browser.** The CTDC application server brokers the signed URL but does not stream file bytes.
- **There is no file size restriction on direct downloads in CTDC.** All registered file types — including large genomic and radiology files — are served through the same download pathway.
- **Users authenticate via NIH eRA Commons or Login.gov only.** Google is not an active identity provider in CTDC. Users without a valid `@nih.gov` or `@login.gov` email domain in their NIH profile cannot log in under the current configuration.
- **The backend does not perform its own ACL checks.** It delegates authentication decisions to the authentication service (`crdc-ctdc-authn`) via a cookie-forwarding pattern. All session and permission data lives in the auth service.
- **Audit logging infrastructure exists but is currently disabled.** The `storeDownloadEvent` function is fully implemented and writes to Neo4j. It has been commented out at the call site and requires a deliberate engineering action to re-enable. This is a compliance gap.
- **Session timeout defaults to 30 minutes.** This can be overridden per environment via deployment configuration. The production value should be confirmed.

---

## 10. Open Items Requiring Engineering Confirmation

| # | Question | Why It Matters | Status |
|---|---|---|---|
| 1 | Is the `storeDownloadEvent` audit log call intentionally disabled, or was it disabled during a migration and not re-enabled? If intentional, what is the documented reason? | Compliance risk — download audit trail is not being written while disabled. Re-enabling requires a deliberate engineering decision. | ⚠️ Open — requires engineering decision |
| 2 | ~~Is the Google IDP active in any CTDC environment (dev, QA, stage, prod)?~~ | ~~User identity classification and access scope~~ | ✅ Resolved — Google IDP is not active in any environment |
| 3 | Is `SESSION_TIMEOUT` set in the production deployment configuration, and if so, what is the value? | Confirms actual production session lifetime for user experience and security policy alignment | ⚠️ Open — production deployment config review needed |
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
| v1.2 | April 3, 2026 | Gina Kuffel, FNL | Resolved Open Items #2 and #4: confirmed Google IDP not active; completed backend ACL review — backend delegates to auth service via `AuthenticationInterceptor`. Added Section 6 (Architecture Boundary). |
| v1.3 | April 3, 2026 | Gina Kuffel, FNL | Resolved Open Items #1 and #3: audit logging fully characterized — `storeDownloadEvent` is implemented but commented out at call site in `routes/files.js`; session timeout default confirmed as 30 minutes from `config.js`, production value pending deployment config review. Updated Sections 5, 8, 9, 10. |

---

*CTDC is a project of the National Cancer Institute's Cancer Research Data Commons (CRDC) | Managed by BACS/FNL under NCI/CBIIT*
*Prepared by Gina Kuffel, Senior TPM, BACS/FNL — April 2026*
*v1.3 — All original open items resolved from source code review; two engineering actions remain outstanding*
