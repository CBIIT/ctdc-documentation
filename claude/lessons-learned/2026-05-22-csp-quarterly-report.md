# Lessons Learned — CSP Quarterly Report Workflow

**Date:** 2026-05-22
**Session:** Drafting the March 2026 – May 2026 CSP
**Related workflow file:** [`../csp-quarterly-report-workflow.md`](../csp-quarterly-report-workflow.md)
**Related prior CSP:** November 2025 – January 2026 (drafted 2026-03-02)

---

## Context

This session produced the March 2026 – May 2026 CSP and, in doing so, established the workflow file `csp-quarterly-report-workflow.md`. The session uncovered seven lessons that materially shaped the workflow's rules. Documenting them here so the *reasoning* survives, not just the rules.

The session also produced a leadership-styled v1.0 of the CSP before the prior-CSP format was disclosed. When the prior format was shared, the entire document was re-styled from scratch. Both files were retained — the leadership v1.0 may be useful for separate stakeholder briefings, but the paste-into-master version is the actual deliverable.

---

## Lesson 1: Software releases and data releases are strictly separate events

**What went wrong**

Initial CSP draft described the CMB Radiology Images data release as shipping "as part of" the v1.3.0.3 software release. This was the TPM's first hard correction in the session.

**Why it matters**

CTDC deliberately never ships software and data together. They are managed as separate events because:
- Software releases have engineering risk profiles (build manifest, regression test, rollback plan)
- Data releases have data governance risk profiles (consent codes, dbGaP approvals, file integrity)
- Conflating them means a software rollback would imply a data rollback, which is operationally impossible (and vice versa)
- Federal reviewers reading the CSP will assume the contractor cannot articulate the difference if the report conflates them

**Rule baked into the workflow**

> Never write that data shipped "as part of" a software release. Use "in conjunction with — but managed as a separate event from —" when their timing aligns. Software release detail belongs in subsection 1.3.1; data release detail belongs in subsection 1.2.2. The two should appear in parallel structure but as distinct events.

---

## Lesson 2: Three dates per software release; lead with the public one

**What went wrong**

Initial Jira pull showed the production deployment ticket CTDC-1997 closed on April 15, 2026. The CSP draft used this date. The TPM corrected: the public release was April 7, 2026.

**Why it matters**

There are three legitimate dates for a software release, each pointing to a different event:

| Date | What happened | Source |
|---|---|---|
| April 1, 2026 | GitHub release tag created (internal release-cut) | `CBIIT/crdc-ctdc-starter-kit` release tag |
| April 7, 2026 | Public release announcement to users | April Monthly Summary |
| April 15, 2026 | Jira deployment ticket closed (post-release validation) | Jira CTDC-1997 resolution date |

The public release date is what stakeholders experienced. The GitHub tag is internal cut. The Jira closure is QA sign-off. **Lead with the public date** — that's the one federal leadership and the TPM's team care about for stakeholder communications.

**Rule baked into the workflow**

> Capture all three dates for traceability. Lead with the public release date in the CSP narrative; subordinate the GitHub tag and Jira closure dates as supporting traceability information.

---

## Lesson 3: The Bento Framework does not yet include a DMN

**What went wrong**

The April Monthly Summary described the node-path-highlighting feature in v1.3.0.3 as "a shared Bento Framework contribution that benefits ICDC and other commons." The CSP draft copied this language. The TPM corrected: the contribution was to the CRDC Data Hub team's Data Model Navigator package, not to Bento, because **Bento doesn't have a DMN**.

**Why it matters**

The CRDC ecosystem has multiple shared packages owned by different teams. The CRDC Data Hub team owns the canonical DMN package shared across CRDC commons. The Bento Framework is a separate (and overlapping) shared infrastructure that does **not** include a DMN. Reporting a DMN contribution to Bento is factually incorrect — the contribution path was to the CRDC Data Hub package.

The CSP and the GitHub release notes agree on this: the GitHub release notes' "Contributions to the Bento Framework" section is `None` for v1.3.0.3 even though the DMN feature did get contributed upstream. The Monthly Summary phrasing was simply wrong on this attribution.

**Rule baked into the workflow**

> If a Monthly Summary or release note describes a DMN contribution as "to Bento," correct it in the CSP. Bento doesn't have a DMN. The contribution path is to the CRDC Data Hub team's Data Model Navigator package.

---

## Lesson 4: Monthly Summaries carry program-level context Jira can't see

**What surfaced during the session**

When the Monthly Summary `.docx` files were loaded mid-session, they added six categories of CSP-relevant content that Jira alone would have missed:

1. **Penetration test results** — "zero high, medium, or low vulnerabilities" appears only in the April Monthly Summary. A major positive finding for 1.3.3.
2. **Federal leadership initiative reviews** — SRF, CRDC Chatbot, RFI feedback all appear only in the Monthly Summaries.
3. **SOP and documentation status** — eight artifacts in flight across the period; tracked in Asana, not Jira.
4. **New data submission tracks** — NCTN Data Archive (MOU/DUA approved this period) and DataCOUNTS (assessment phase) appear in the Monthly Summaries but have no Jira tickets yet.
5. **Software release narrative** — build manifest, feature descriptions, bug-fix wording. The GitHub release notes also carry this; the Monthly Summary adds the user-facing context.
6. **Anticipated activities for the next period** — only in the Monthly Summaries.

**Rule baked into the workflow**

> Always pull the Monthly Summaries for every month of the reporting period before drafting. A CSP drafted from Jira alone will miss the program-level context that makes the report complete. If a Monthly Summary mentions a track, item, or event not visible in Jira, include it in the CSP.

---

## Lesson 5: Excluding Test issue types is essential, not optional

**What surfaced**

Initial Jira pull returned 260 tickets for the Mar–May 2026 window. Inspection showed many were Test, Test Execution, and Test Set tickets that had been bulk-touched on `2026-04-06T13:39` — a single mass-timestamp refresh during regression prep for the Q3 release. These weren't real work; they were a housekeeping pass that updated the `updated` field on a large batch of test cases.

Filtering them out narrowed the count to 189 meaningful tickets. The 71-ticket gap is exactly the kind of inflation that hand-counting (or even an unfiltered Jira pull) would silently absorb.

**Rule baked into the workflow**

> The Jira pull for the CSP must use `issuetype not in (Epic, Test, "Test Execution", "Test Set")`. Test cases that were mass-touched during regression cycles are not work product; they are housekeeping.

---

## Lesson 6: Match the prior CSP's format exactly — do not invent

**What went wrong**

Initial CSP draft was built as a polished leadership-styled `.docx` with an NIH Blue header band, a metadata table, a metrics table, a risks-and-flags section, a "next period activities" section, and a source-attribution footer. The TPM then shared the February 2026 CSP, which had none of those elements — it was Calibri 11pt body, bold inline section headers, italic SOW reference, plain numbered subsection labels (`1.2.1`, `1.2.2`, etc.), and flat bullets. Paste-into-master, not standalone.

The leadership draft had to be entirely re-styled. Roughly an hour of work lost to format invention.

**Why it matters**

The CSP's CTDC content is paste-into-master — it gets consolidated by BACS/FNL into a single contractor report covering multiple projects. A bespoke layout breaks the consolidation. The prior CSP's format is the format; the format is not negotiable.

**Rule baked into the workflow**

> Always pull the prior CSP `.docx` first and match its format verbatim before drafting any content. If a leadership-styled version is wanted separately for a briefing or internal review, build it as a parallel artifact — do not mix the styles into the paste-into-master deliverable.

---

## Lesson 7: Scoping, spikes, and design work are NOT delivery

**What went wrong**

Both the summary paragraph and the Task 1.3.1 bullet for the dynamic megazip file generation capability initially used the verb "standing up" — *"...standing up the dynamic megazip file generation capability that will enable on-demand, large-scale file downloads from the Study Details page..."* The TPM corrected this near the end of the session: the megazip capability did NOT ship in v1.3.0.3 and will likely be the headline of the next CSP. What actually happened during this period was architecture and design work — a federal-compliance spike, Gen3 SDK research, a File Service megazip architectural design document (in progress), a sequence diagram, multi-file-download UX designs, and twelve Phase-2 implementation tasks scoped. None of that was delivery to users.

**Why it matters**

Verbs like "stood up," "delivered," "shipped," "rolled out," "launched," and "deployed to Production" tell federal leadership that a capability is live. Architecture documents, spikes, sequence diagrams, design mockups, and "Phase-2 tasks scoped" tell federal leadership that a capability is being designed. Mixing the two is one of the most easily-made factual errors in a CSP, because the distinction often turns on a single verb. Once federal leadership reads "the team stood up megazip downloads," they will assume the feature works in Production — and the contractor's credibility on factual reporting takes a hit when they later find out it doesn't.

The reverse case matters just as much: when a capability that was previously reported as in-flight does ship in the current period, that's the moment to call it out as delivered — and to cross-reference the prior CSP that reported the scoping/design progress so federal leadership can connect the two reporting periods.

**Rule baked into the workflow**

> Architecture documents, spikes, sequence diagrams, design mockups, and scoped Phase-2 tasks are progress on a capability planned for a future release, not delivery of the capability itself. They belong in the CSP narrative as in-flight work, never as completed delivery. Use "advancing the architecture and design for [X] that will enable [outcome] in a future release" instead of "standing up [X]." Add explicit "did NOT ship in v[version]" framing on the in-flight bullet. The capability itself is reported as delivery in the CSP for the quarter when it ships to users — at which point the previously-in-flight CSP should be cross-referenced.

**Canonical examples**

- *Mar–May 2026 CSP (in-flight phrasing):* "Dynamic megazip file generation capability — new epic scoped to deliver on-demand, large-scale file downloads from the Study Details page in a future release. The capability did NOT ship in v1.3.0.3 and is expected to be reported in a future CSP once it is publicly released. Progress this period: [spike, research, design, UX, query update, twelve scoped Phase-2 tasks]."
- *Next CSP (delivery phrasing, anticipated):* "Dynamic megazip file generation capability publicly released in CTDC v[version] on [public date], enabling on-demand, large-scale file downloads from the Study Details page. See the March–May 2026 CSP for the architecture and design work that preceded this release."

---

## Format spec (extracted from the February 2026 submission)

For quick reference when matching the prior CSP. See the workflow file for the canonical version of this table.

| Element | Rule |
|---|---|
| Font | Calibri, 11pt body |
| Page | US Letter, 1" margins |
| Lead line | `**CTDC:**` (bold inline, no heading style) |
| Summary | Two paragraphs (software-side first, data-side second); no "Summary" heading |
| Section headers | Bold inline, not Heading 2 — `**Task 1.2: ...**` |
| SOW reference | One line of italic text quoting contract language, immediately below the section header |
| Subsection labels | Plain text lines — `1.2.1 Data Acquisition & Ingestion` |
| Bullets | Flat list, single nesting level, `•` glyph; no sub-bullets |
| Metadata table | None |
| Header band / branding | None |
| Metrics table | None |
| Risks/flags section | None |
| Anticipated Activities section | None |
| Source attribution footer | None |

---

## New subsection: 1.3.3 Robust security protocol updates and compliance

The February 2026 CSP did not have a 1.3.3 subsection — security work fit naturally into 1.3.2. The Mar–May 2026 CSP introduced 1.3.3 because the period included two security artifacts substantial enough to warrant their own bucket:

1. **Penetration Test result** (zero findings across high, medium, low) — a positive compliance finding that warrants prominent placement.
2. **Twistlock Container Security Vulnerabilities epic (CTDC-2008)** — five service-level upgrade tasks remediating critical and high findings.

The SOW language for Task 1.3 explicitly includes "robust security protocol updates as well as compliance with federal mandates," so 1.3.3 has a clear contractual home. The decision rule for future CSPs:

- **Has the period contained a discrete security event or a dedicated security epic?** Use 1.3.3.
- **Was security work limited to routine CVE patching or scattered hardening?** Fold into 1.3.2.

---

## Cross-references

- Workflow file: [`../csp-quarterly-report-workflow.md`](../csp-quarterly-report-workflow.md)
- Sibling workflow: [`../post-meeting-sprint-recap-workflow.md`](../post-meeting-sprint-recap-workflow.md) — both produce stakeholder artifacts; CSP differs in cadence (quarterly vs per-sprint) and audience (federal vs team)
- SKILL.md Section 6b — Verify Before You Count
