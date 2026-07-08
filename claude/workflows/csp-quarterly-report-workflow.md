### 12e. 📑 CSP Quarterly Report Workflow (Drafted)

> **Use this workflow once per quarter to produce the CTDC content section for the Cost Status Period (CSP) Report.** The canonical examples are the **November 2025 – January 2026 CSP** (drafted March 2, 2026) and the **March 2026 – May 2026 CSP** (drafted May 22, 2026). The CSP is a federal contractor reporting obligation; the output is a `.docx` of CTDC's task-area content meant to be pasted into a master CSP document maintained at the BACS/FNL prime-contractor level.

**Why this workflow**

The CSP is the quarterly accountability artifact that maps three months of team activity to the contract's SOW task areas (1.2, 1.3, 1.4). It is read by federal leadership (NCI/CBIIT) and consolidated with other BACS/FNL projects into a single contractor report. Unlike sprint recaps (which are team-facing) or the leadership overview series (which are stakeholder-facing), the CSP is a **compliance artifact** — it documents what was delivered against the Statement of Work for that period.

This workflow is **separate from** the Monthly Summary cadence. Monthly Summaries are program-level narrative posts in Asana submitted at month-end and are an input to the CSP. The CSP consolidates three months of Monthly Summaries plus Jira data plus release artifacts into the SOW-task-area structure required by the prime contract.

**Cadence:** Once per quarter, aligned to the reporting period. CSP-reportable periods to date have been three-month windows (Nov–Jan; Mar–May), though the specific dates are set by BACS/FNL. The TPM (Gina) drafts the CTDC content; BACS/FNL assembles the master CSP for federal submission.

---

**Inputs Required (Five Sources — All Recommended)**

The CSP draws from more sources than a sprint recap because the reporting window is longer and includes events that live outside Jira. **Pull all five before drafting.** A CSP drafted from Jira alone will miss program-level context (penetration test results, federal leadership reviews, new data tracks not yet ticketed).

1. **Jira pull — meaningful tickets in the period.**
   ```
   project = CTDC
     AND issuetype not in (Epic, Test, "Test Execution", "Test Set")
     AND updated >= "<start>"
     AND updated <= "<end>"
   ORDER BY updated DESC
   ```
   Use `fields=summary,status,labels,issuetype,updated,resolutiondate,customfield_12350,assignee` and paginate at `limit=50`. **Always exclude Test, Test Execution, and Test Set issue types** — they generate bulk-touched noise from regression prep cycles that distorts work counts. Typical period yields 150–250 meaningful tickets.

2. **Jira pull — epics active in the period.**
   ```
   project = CTDC
     AND issuetype = Epic
     AND updated >= "<start>"
   ORDER BY updated DESC
   ```
   Capture `summary`, `status`, `labels`. The `labels` field carries the SOW task tag (`Task-1.2.1`, `Task-1.3.8.2`, etc.) — this is the canonical mapping from epic to SOW task area. Three or four epics in the reporting period will have `null` SOW labels; treat those as "Other" and place them in the most natural task area by content.

3. **Monthly Summary `.docx` files from Asana.** One per month of the reporting period (three files for a quarter). These carry program-level context Jira alone misses:
   - Penetration test results
   - Federal leadership initiative reviews (SRF, CRDC Chatbot, RFI)
   - SOP/documentation status across the eight in-flight artifacts
   - Data submission tracks not yet ticketed in Jira (e.g., NCTN Data Archive, DataCOUNTS)
   - Software release narrative including build version manifest
   - Bento Core / RAS work restart/hold status
   - Anticipated activities for the next period

   Extract with `extract-text /mnt/user-data/uploads/<file>.docx`. Read all three in full before drafting — the CSP's accuracy depends on this.

4. **GitHub release notes** for any software releases in the window — `CBIIT/crdc-ctdc-starter-kit/releases/tag/<version>` is the authoritative source for what shipped in code. The release page carries:
   - Build manifest (Frontend, Backend, File Service, Auth, Interoperation, DMN, DMN Core versions)
   - New Supported Features (the precise wording stakeholders will see)
   - New Content / New Services / New Integrations / Bento Framework Contributions (often `None`; check anyway)
   - Bug Fixes

   The GitHub release tag date is the **internal release-cut date**, which is typically a few days *before* the public release date cited in the Monthly Summary. Capture both and lead with the public date.

5. **Data release blurbs** from stakeholder communications. When a data release ships, the TPM prepares a stakeholder-facing blurb (Slack, email, leadership doc) that contains the canonical numbers — file count, participant count, study link. This is the source of truth for data release contents. Jira and GitHub don't carry these numbers; the blurb does.

If any of these inputs is missing, **surface the gap and wait**. Do not improvise — a CSP that fabricates content is a contract-compliance problem, not just a quality problem.

---

**Workflow (10 Steps)**

1. **Confirm the reporting period.** Always verify with the TPM — periods aren't strict calendar quarters and have shifted (Nov–Jan was one period; Mar–May was the next). The dates are set by BACS/FNL prime contractor cadence, not by the team.

2. **Pull the prior CSP as the formatting reference.** The CTDC content section is paste-into-master, not standalone. The prior CSP (`CTDC_CSP_<month>_<year>.docx`) defines the exact format. **Do not invent a new format** — match the prior one verbatim. See *Output Format Spec* below for the rules extracted from the February 2026 submission.

3. **Pull Jira data (both queries above) and save raw JSON to disk.** Paginate to completion. Save each page as `/home/claude/csp/issues_page_N.json` and `/home/claude/csp/epics.json` so the tally script can read raw data.

4. **Tally counts via script — never hand-count.** Write a Python script that reads the raw JSON and prints counts by status, issue type, assignee, and epic membership. This catches two common mistakes:
   - Confusing "In Progress" with "In Progress + Ready for Review + Ready for QA"
   - Including Test / Test Execution / Test Set tickets that were mass-touched during regression prep cycles
   See SKILL.md Section 6b for the reusable tally pattern.

5. **Build the epic → SOW task area mapping.** Each epic's `labels` field carries the SOW tag. Bucket each epic into one of:
   - **1.2** (any `Task-1.2.*` label) — Data work
   - **1.3** (any `Task-1.3.*` label, plus `DevOps`) — Software / Operations
   - **1.4** (any `Task-1.4.*` label) — Stakeholder Outreach
   - **Other** (no label) — place by content judgment

6. **Read all three Monthly Summaries in full.** Note items not visible in Jira: penetration test results, federal leadership reviews, new data submission tracks, SOP/documentation status, release narrative, anticipated activities. These belong in the CSP even though they don't have ticket IDs.

7. **Fetch the GitHub release page(s).** For each software release in the window, web-fetch `https://github.com/CBIIT/crdc-ctdc-starter-kit/releases/tag/<version>` and capture the build manifest, features, and bug fixes verbatim. Cross-reference the release date with the Monthly Summary's stated public release date — these will differ by a few days.

8. **Draft the CSP content in the prior submission's format.** Save to `/mnt/user-data/outputs/CTDC_CSP_<startMonth><year>_<endMonth><year>.docx`. The structure for the March–May 2026 CSP matched the February 2026 submission exactly:
   - Lead line `**CTDC:**`
   - Two summary paragraphs (software first, data second; no "Summary" heading)
   - **Task 1.2** with subsections `1.2.1 Data Acquisition & Ingestion` and `1.2.2 Data validation, quality assurance, and provenance`
   - **Task 1.3** with subsections `1.3.1 Integration of emerging technologies`, `1.3.2 General operations and maintenance`, and `1.3.3 Robust security protocol updates and compliance` (new in the May 2026 CSP to house the penetration test result and Twistlock remediation)
   - **Task 1.4** as a flat bulleted list

9. **Validate the .docx and convert to PDF for spot-check.** Use `python3 /mnt/skills/public/docx/scripts/office/validate.py` followed by `soffice --convert-to pdf` and `pdftoppm` to render preview pages. Confirm the format matches the prior CSP at every page break.

10. **Update this workflow file if the workflow itself evolved during this run.** Each CSP may reveal a new edge case (a new SOW subsection like 1.3.3; a new data submission track type; a new input source). Capture in the Lessons Learned section so it's available for the next quarterly run.

---

**Output Format Spec (Match the Prior CSP Exactly)**

The CTDC content is **paste-into-master**, not a standalone leadership doc. The February 2026 submission established the format rules:

| Element | Rule |
|---|---|
| Font | Calibri, 11pt body |
| Page | US Letter, 1" margins |
| Lead line | `**CTDC:**` (bold, inline, no heading style) |
| Summary | Two paragraphs (software-side first, data-side second); no "Summary" heading |
| Section headers | **Bold inline**, not Heading 2 styling — e.g., `**Task 1.2: Continued Integration of Diverse and Complex Datasets into the CTDC**` |
| SOW reference | One line of italic text quoting the contract language, immediately under the section header |
| Subsection labels | Plain text lines, no bold, no bullet — e.g., `1.2.1 Data Acquisition & Ingestion` |
| Bullets | Flat list, single nesting level, single `•` glyph; do not use sub-bullets |
| Metadata table | None — paste-into-master means no header table |
| Header band / branding | None — no NIH Blue, no logos |
| Metrics table | None |
| Risks/flags section | None — risks are surfaced in Monthly Summaries and Sprint Recaps, not in the CSP |
| Anticipated Activities | None — handled by the Monthly Summary, not by the CSP |
| Source attribution footer | None |

If a leadership-facing styled version is requested separately (e.g., for an internal briefing), build it as a parallel artifact — do NOT mix the styles. The leadership v1.0 of the March–May 2026 CSP was built first and then re-styled as the paste-into-master version on request; both files were retained.

---

**Section → SOW Mapping**

Use this mapping to sort epics and ticket clusters into the right SOW subsection:

| Section | Belongs Here |
|---|---|
| **1.2.1 Data Acquisition & Ingestion** | Active submission tracks (CTDC-1664 children); per-study CDE workbooks and submission docs; new submission track onboarding; stakeholder/submitter engagement specific to a track |
| **1.2.2 Data validation, QA, provenance** | Data model updates (CTDC-1801); megazip indexing via DCF; mock data generation; **data releases delivered to Production** (this is where data releases live — see lesson below on never conflating with software releases); CMB data migration / loading |
| **1.3.1 Integration of emerging technologies** | Software releases (with full build manifest and feature list); new epics (megazip download capability, Local Find feature, Webpack 5 migration, Bento Data Retriever); RAS authentication work; technology spikes |
| **1.3.2 General operations and maintenance** | Page-level work and bug fixes (DMN, Study Details, Explore Dashboard); infrastructure work (Neo4j → Memgraph, Jenkins → Prefect migrations); Auth Service; deployment work; cross-cutting application maintenance |
| **1.3.3 Robust security protocol updates and compliance** | Penetration test results; Twistlock / container security remediation epics; CVE patching; federal compliance reviews; AWS Firewall Manager and other security infrastructure work. *Added in the May 2026 CSP — prior CSPs did not have this subsection because the security work hadn't yet warranted its own bucket. If a future quarter has no security work, fold it back into 1.3.2.* |
| **Task 1.4 Stakeholder Outreach** | SOP and documentation artifacts (the eight in-flight at any time); TPM monthly touch base; federal leadership initiative reviews (SRF, CRDC Chatbot, RFI); stakeholder/submitter engagement; CRDC Component Report Out; Monthly Summary and RWDN reporting cadence |

---

**Critical Rules — Do Not Violate**

These are not style preferences; they are **factual integrity** rules. Violating any of these produces a CSP that misrepresents what the team did.

- **Never conflate software releases and data releases.** Software releases (CTDC v1.3.0.3) and data releases (CMB +1,864 images / 129 participants) are **strictly separate events** in the CTDC workflow. They may be delivered in conjunction (the data release timed to land alongside a software release for stakeholder communication), but they are managed independently. Use the phrasing "in conjunction with — but managed as a separate event from —" when their timing aligns. Never write that data shipped "as part of" a software release. Software release detail goes in 1.3.1; data release detail goes in 1.2.2.

- **Three dates per software release; use the right one for the right purpose.**
  - **Public release date** (from the Monthly Summary or the stakeholder release blurb) is what users experienced. This is what leadership and stakeholders care about. **Lead with this date.**
  - **GitHub release tag date** is the internal release-cut date, typically a few days earlier. Mention it for traceability but subordinate it.
  - **Jira deployment ticket closure date** is the post-release validation completion date, typically a few days later. Mention it for compliance traceability.

  For CTDC v1.3.0.3, the three dates were April 7 (public), April 1 (GitHub tag), April 15 (Jira closure). The Monthly Summary cited April 7; the CSP leads with April 7.

- **DMN contributions go to the CRDC Data Hub team's DMN package, not Bento.** The Bento Framework does **not** include a Data Model Navigator. The canonical DMN package is owned by the CRDC Data Hub team and is shared across CRDC commons. If a Monthly Summary or release note describes a DMN contribution as "to Bento," **correct it** in the CSP — Bento doesn't have a DMN, so the contribution path was to the CRDC Data Hub DMN package. This was the case for the node-path-highlighting feature in v1.3.0.3.

- **Scoping, spikes, and design work are NOT delivery — never describe in-flight capabilities as if they shipped.** Architecture documents, spike outcomes, sequence diagrams, design mockups, and "Phase-2 implementation tasks scoped" are **progress on a capability planned for a future release**, not delivery of the capability itself. They belong in the CSP narrative as in-flight work, never as completed delivery. Verbs to avoid for not-yet-shipped capabilities: "stood up," "delivered," "shipped," "rolled out," "launched," "deployed to Production." Verbs to use instead: "advancing the architecture and design for," "scoping," "in development for a future release," "expected to be reported in a future CSP once publicly released." The capability is then reported as delivery in the CSP for the quarter when it actually ships to users.

  *Example — March–May 2026 CSP, dynamic megazip file generation capability.* The team completed a federal-compliance spike, Gen3 SDK research, a File Service megazip architectural design document (in progress), a sequence diagram, multi-file-download UX designs, and scoped twelve Phase-2 implementation tasks. None of that shipped to users in v1.3.0.3. The initial draft of the CSP described this work as "standing up the dynamic megazip file generation capability," which incorrectly implied delivery. The corrected wording: *"advancing the architecture and design for a dynamic megazip file generation capability that will enable on-demand, large-scale file downloads from the Study Details page in a future release. The capability did NOT ship in v1.3.0.3 and is expected to be reported in a future CSP once it is publicly released."*

  *Reverse case to watch for in future CSPs.* When a capability that was previously reported as in-flight does ship in the current period, that's the moment to call it out as delivered — and to cross-reference the prior CSP that reported the scoping/design progress so federal leadership can connect the two reporting periods. Megazip is expected to follow this pattern in the next CSP.

- **Some data submission tracks don't have Jira epics yet.** NCTN Data Archive (MOU/DUA approved in this period) and DataCOUNTS (assessment phase) appear in the Monthly Summaries but not in Jira. **Always cross-check the Monthly Summaries** — if a track appears there but not in Jira, list it in 1.2.1 anyway. Epic creation for new tracks is the *next* reporting period's work.

- **The Penetration Test result is not in Jira.** It appears only in the April Monthly Summary. Don't miss it — it's a major positive finding ("zero high, medium, or low vulnerabilities") that anchors the 1.3.3 subsection.

- **Federal sponsor reviews aren't ticketed in Jira either.** SRF reviews, CRDC Chatbot input, RFI responses — these are pulled from the Monthly Summary into Task 1.4.

- **Always re-verify Jira counts with a script, not by hand.** Bulk timestamp refreshes look like work but aren't. The Mar–May 2026 raw Jira pull was 260 tickets; excluding Test, Test Execution, and Test Set narrowed it to 189 meaningful tickets. The difference is what hand-counting would have miscounted.

- **Excluded data submission tracks from prior CSPs that resumed activity should be re-listed.** If a track was On Hold in the previous CSP and resumed in this period, re-list it with the resumption note. Don't assume continuity from the prior period — the CSP is read by federal leadership who may not remember the prior period's status.

---

**Distribution**

- **Primary:** The TPM (Gina) submits the `.docx` to BACS/FNL prime-contractor leadership, who consolidates it with other projects into the master CSP for federal submission to NCI/CBIIT.
- **No Slack, no Teams broadcast.** Unlike Sprint Recaps, the CSP is not a team-facing artifact. It is internal-to-contractor reporting.
- **Retention:** Keep the `.docx` in the TPM's local SharePoint or OneDrive. Do **not** commit `.docx` files to GitHub (per SKILL.md house rule — only `.md` source goes to GitHub).

---

**Cross-References**

- SKILL.md Section 6b — "Verify Before You Count" (the tally-script rule this workflow inherits)
- SKILL.md Section 12 — Sprint cadence workflows
- `claude/workflows/post-meeting-sprint-recap-workflow.md` — sibling workflow, also produces stakeholder artifacts; CSP differs in cadence (quarterly vs per-sprint) and audience (federal vs team)
- `claude/lessons-learned/2026-05-22-csp-quarterly-report.md` — session lessons from the Mar–May 2026 CSP drafting
- Prior CSP `.docx` files in SharePoint (Nov 2025 – Jan 2026; Mar 2026 – May 2026) — the canonical format references

---

*Workflow drafted 2026-05-22 from the March 2026 – May 2026 CSP drafting session. Canonical examples: November 2025 – January 2026 CSP (drafted March 2, 2026) and March 2026 – May 2026 CSP (drafted May 22, 2026). The March–May 2026 run introduced subsection 1.3.3 (Robust security protocol updates and compliance) to house the Penetration Test result and the Twistlock remediation epic; this subsection is conditional and may be folded back into 1.3.2 in quarters with no dedicated security work. A late-session correction added the "scoping is not delivery" critical rule after the megazip capability was initially described as "standing up" — the rule now explicitly forbids delivery verbs for in-flight architecture/design/spike work, with megazip serving as the canonical example expected to flip to a delivery report in a future CSP.*
