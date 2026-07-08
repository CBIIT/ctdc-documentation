# 🎟️ Release Ticket Templates

> **Use this component for the three Jira tickets that recur every CTDC software release and must be formatted identically each time: the Final Design QA epic, the Stage deployment task, and the Production deployment task.** Each has a canonical **template ticket** in Jira (labeled `template`) that is cloned per release. This file is the canonical spec; the Jira template tickets are the clone sources. Companion: the Software Release Playbook (`software-release-playbook.md`), whose "Canonical Release Tickets" section links here.

**Why this component**

Three tickets are created for every release. When each is hand-built from memory, formatting drifts release to release (escaped markup, inconsistent sections, stale ticket references). Pinning one canonical template ticket per artifact — cloned, never re-typed — keeps every release's tickets identical in shape, so reviewers and automation can rely on the structure.

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

`### **Section**` headers (round-trip to Jira `h3.`), emoji-prefixed; slim shape; italic-label `* *Label*:` bullets (not `- **Label:**`); Markdown tables (convert to `||header||`). Push the body via `jira_update_issue` using the two-step create-then-update pattern (MCP converts Markdown to wiki server-side), then confirm the render in the Jira UI — the wiki echo is not a reliable preview.

---

## Final Design QA epic (clone of CTDC-2135)

Four sections: 🎯 Design QA Summary · 🔎 Findings · 🚦 Workflow · ✅ Definition of Done.

```
### 🎯 **Design QA Summary**
Conduct a thorough final design review of every new feature and page implemented during
this release cycle, verifying design fidelity and Section 508 accessibility compliance
before the release candidate is promoted to Stage.

### 🔎 **Findings**
Log each discrepancy below and file it as a bug linked to this epic, grouped by page or feature.

| Feature | Page | 508 Compliance | Design Discrepancy Comment | Figma Link | JIRA Bug Ticket |
|---|---|---|---|---|---|
| | | | | | |

### 🚦 **Workflow**
1. When the release Slack channel signals QA regression has started in the QA tier, begin Final Design QA on the QA environment.
2. Review every new feature and page for design fidelity and Section 508 compliance.
3. File each discrepancy as a bug linked to this epic; announce it in the release channel.
4. Notify the TPM when Final Design QA is complete.

### ✅ **Definition of Done**
* *Review*: every new feature and page checked for design and 508 compliance.
* *Findings*: all discrepancies logged and filed as linked bugs.
* *Blockers*: any release-blocker findings routed to Engineering before promotion.
* *Handoff*: TPM notified.
```

---

## Stage / Prod deploy tasks (clone of CTDC-2136 / CTDC-2137)

Three sections: 🎯 Deployment Summary · 🚦 Workflow · 📋 Tracking. Identical except the environment word (**Stage** / **Production**).

```
### 🎯 **Deployment Summary**
File an NCI ServiceNow ticket so the CloudOne team reserves the date and time to deploy
the software release candidate to the CTDC <Stage | Production> environment.

* *Release Dashboard*: paste the link to this release's Release Dashboard here.

### 🚦 **Workflow**
1. Add the TPMs (`kuffelgr`, `singletonss`) as watchers on the NCI ServiceNow ticket.
2. Create an all-day Outlook calendar invite for the scheduled deployment day (exact time added later) and add the TPM, BE Lead, FE Lead, and CloudOne Engineer.
3. Send the Outlook invite to the assigned CloudOne member and the TPMs.
4. Deploy the build to <Stage | Production> on the scheduled date and notify the TPM.
5. Fill in the Tracking table as information becomes available.

### 📋 **Tracking**
| NCI ServiceNow Ticket | URL | Assignee | Notes about Deployment |
|---|---|---|---|
| | | (CloudOne point of contact) | (nuances or issues encountered) |
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

- **v1 (2026-07-08)**: Initial component. Template tickets created and labeled `template`: CTDC-2135 (Final Design QA epic), CTDC-2136 (Stage deploy task, under CTDC-2007), CTDC-2137 (Prod deploy task, under CTDC-2007). Live Q4 tickets CTDC-2130 / CTDC-2128 / CTDC-2129 reformatted to this house style.
