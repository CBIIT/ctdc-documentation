# 🎟️ Release Ticket Templates

> **Use this component for the three Jira tickets that recur every CTDC software release and must be formatted identically each time: the Final Design QA epic, the Stage deployment task, and the Production deployment task.** Each has a canonical **template ticket** in Jira (labeled `template`) that is cloned per release. This file is the canonical spec; the Jira template tickets are the clone sources. Companion: the Software Release Playbook (`software-release-playbook.md`), whose "Canonical Release Tickets" section links here. For DevOps work that is not a release promotion (upgrades, environment variables, infrastructure changes), use the DevOps Task template (`devops-task-template.md`).

**Why this component**

Three tickets are created for every release. When each is hand-built from memory, formatting drifts release to release (escaped markup, inconsistent sections, stale ticket references). Pinning one canonical template ticket per artifact (cloned, never re-typed) keeps every release's tickets identical in shape, so reviewers and automation can rely on the structure.

---

## Template tickets (clone sources)

| Purpose | Template ticket | Type | Clones into | Parent / Epic | Default assignee | Label |
|---|---|---|---|---|---|---|
| **Final Design QA** | **CTDC-2135** | Epic | a per-release epic `Final Design QA: <YYYY> Q<N> Release Candidate` | standalone (epics don't nest) | TPM (`kuffelgr`) | `template` → swap to the release-line label on the clone |
| **Stage deployment** | **CTDC-2136** | Task | `Deploy <YYYY> Q<N> software release candidate to Stage`, under CTDC-2007 | Epic **CTDC-2007** "CTDC Software Release Deployments" (standing) | DevOps Lead (`nguca`) | `template` (drop on the clone) |
| **Prod deployment** | **CTDC-2137** | Task | `Deploy <YYYY> Q<N> software release candidate to Production`, under CTDC-2007 | Epic **CTDC-2007** (standing) | DevOps Lead (`nguca`) | `template` (drop on the clone) |

Live Q4 examples already following this format: **CTDC-2130** (Final Design QA), **CTDC-2128** (Stage), **CTDC-2129** (Prod).

---

## House style (all three)

`h3.` section headers, emoji-prefixed, with bold titles; slim shape; italic-label `* *Label*: content` bullets; Jira wiki tables (`||header||` header row, `|cell|` body rows). Bodies are written directly in **Jira wiki markup**: Markdown translation is disabled on the mcp-atlassian connector (`DISABLE_JIRA_MARKUP_TRANSLATION=true`, Sep 2026), so Markdown pushed through `jira_update_issue` renders as literal `###` and `**` characters. Push with the two-step create-then-update pattern, escape literal curly braces as `\{` and `\}`, then confirm the render in the Jira UI: the API echo is not a reliable preview.

---

## Final Design QA epic (clone of CTDC-2135)

Four sections: 🎯 Design QA Summary · 🔎 Findings · 🚦 Workflow · ✅ Definition of Done.

```
h3. 🎯 *Design QA Summary*
Conduct a thorough final design review of every new feature and page implemented during this release cycle, verifying design fidelity and Section 508 accessibility compliance before the release candidate is promoted to Stage.

h3. 🔎 *Findings*
Log each discrepancy below and file it as a bug linked to this epic, grouped by page or feature.

||Feature||Page||508 Compliance||Design Discrepancy Comment||Figma Link||JIRA Bug Ticket||
| | | | | | |

h3. 🚦 *Workflow*
# When the release Slack channel signals QA regression has started in the QA tier, begin Final Design QA on the QA environment.
# Review every new feature and page for design fidelity and Section 508 compliance.
# File each discrepancy as a bug linked to this epic; announce it in the release channel.

h3. ✅ *Definition of Done*
* *Review*: every new feature and page checked for design and 508 compliance.
* *Findings*: all discrepancies logged and filed as linked bugs.
* *Blockers*: any release-blocker findings routed to Engineering before promotion.
```

---

## Stage / Prod deploy tasks (clone of CTDC-2136 / CTDC-2137)

Four sections: 🎯 Deployment Summary · 🚦 Workflow · 📋 Tracking · ✅ Definition of Done. Identical except the environment word (**Stage** / **Production**) and two Production-only phrases that pin Production to the package already verified in Stage (marked below). There is no handoff step or Handoff bullet: the handoff is the ticket's status change in Jira.

```
h3. 🎯 *Deployment Summary*
File an NCI ServiceNow ticket so the CloudOne team reserves the date and time to deploy the software release candidate to the CTDC <Stage | Production> environment.

* *Release Dashboard*: paste the link to this release's Release Dashboard here.

h3. 🚦 *Workflow*
# Add the TPMs ({{kuffelgr}}, {{singletonss}}) as watchers on the NCI ServiceNow ticket.
# Create an all-day Outlook calendar invite for the scheduled deployment day (exact time added later) and add the TPM, BE Lead, FE Lead, and CloudOne Engineer.
# Send the Outlook invite to the assigned CloudOne member and the TPMs.
# Deploy the Deployment Package [Production only: verified in Stage] to <Stage | Production> on the scheduled date.
# Fill in the Tracking table as information becomes available.

h3. 📋 *Tracking*
||Service Now Cloud One Ticket Link||Assignee||Notes about Deployment||
| |(CloudOne point of contact)|(nuances or issues encountered)|

h3. ✅ *Definition of Done*
* *ServiceNow*: the Service Now Cloud One ticket is linked in Tracking and closed by CloudOne as successful.
* *Deployment Package*: the package deployed matches the Deployment Package Number on the Release Dashboard [Production only: and is the same package verified in Stage].
* *Deployment*: the Deployment Package is deployed to <Stage | Production> in the scheduled window.
* *Verification*: the deployed package is confirmed in <Stage | Production>, and the application loads and returns data.
* *Release Dashboard*: the <Stage | Production> deployment date is recorded.
```

---

## Cloning per release

1. Clone the template ticket (Jira **••• → Clone**).
2. Swap `<YYYY> Q<N>` in the summary; update the Release Dashboard link.
3. Remove the `template` label; set the release-line label on the Final Design QA epic as needed.
4. Assign: DevOps Lead for the deploy tasks, TPM for the Final Design QA epic.
5. For the deploy tasks, confirm the clone landed under the standing epic **CTDC-2007**.

---

## Changelog

- **v5 (2026-10-22)**: Final Design QA epic: removed the "Notify the TPM" Workflow step and the *Handoff* Definition of Done bullet; the handoff is the ticket's status change in Jira. Applies the same rule v4 applied to the deploy tasks. The clone source CTDC-2135 needs the same change.
- **v4 (2026-10-22)**: Deploy tasks, matching ICDC Release Ticket Templates v1.3: (1) "Deployment Package" replaces "the build" in Workflow step 4 and the Definition of Done; (2) new *Deployment Package* Definition of Done bullet tying the deployed package to the Deployment Package Number on the Release Dashboard, with Production also requiring the same package verified in Stage; (3) removed the *Handoff* Definition of Done bullet and the "notify the TPM" clause from Workflow step 4, since the handoff is the ticket's status change in Jira. The Final Design QA epic is unchanged. The Jira clone sources CTDC-2136 and CTDC-2137 need both the v3 and v4 changes.
- **v3 (2026-10-20)**: Deploy tasks, matching the new ICDC Release Ticket Templates (v1.2) so both projects use the same shape: (1) the Tracking table's separate "NCI ServiceNow Ticket" and "URL" columns are merged into one "Service Now Cloud One Ticket Link" column; (2) a ✅ Definition of Done section is added as the fourth section. The Jira template tickets CTDC-2136 and CTDC-2137 need the same two changes.
- **v2 (2026-10-07)**: House style and both body blocks rewritten in Jira wiki markup (`h3.`, `#` numbered lists, `||header||` tables) to match the connector's disabled Markdown translation; the old note that the MCP converts Markdown server-side was removed. Em dashes replaced. Pointer added to the new DevOps Task template for non-release DevOps work. The Jira template tickets (CTDC-2135, CTDC-2136, CTDC-2137) were not changed.
- **v1 (2026-07-08)**: Initial component. Template tickets created and labeled `template`: CTDC-2135 (Final Design QA epic), CTDC-2136 (Stage deploy task, under CTDC-2007), CTDC-2137 (Prod deploy task, under CTDC-2007). Live Q4 tickets CTDC-2130 / CTDC-2128 / CTDC-2129 reformatted to this house style.
