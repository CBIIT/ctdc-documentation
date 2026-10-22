# 🎟️ Release Ticket Templates

> **Use this component for the Jira artifacts that recur with every CTDC software release: the Final Design QA epic (one per release), and the Deployment Package task and Stage and Production deployment tasks (one of each per release) under the standing deployments epic.** This file is the canonical spec. The TPM creates each release's tickets from it; there are no Jira clone-source tickets. Companion: the Software Release Playbook (`../workflows/software-release-playbook.md`), whose "Release Tickets" section links here. For DevOps work that is not a release promotion (upgrades, environment variables, infrastructure changes), use the DevOps Task template (`devops-task-template.md`).
>
> Canonical CTDC examples: **CTDC-2007** (standing deployments epic), **CTDC-2130** (2026 Q4 Final Design QA), **CTDC-2128** (2026 Q4 Stage deployment), **CTDC-2129** (2026 Q4 Production deployment). ICDC's equivalent, kept in lockstep, is `CBIIT/icdc-documentation/claude/templates/release-ticket-templates.md`.

**Why this component**

A release deployment is not a feature and not a DevOps change. It needs its own record that answers three questions an auditor or the TPM will ask: what shipped, when did it reach each tier, and under which ServiceNow change.

The Deployment Package task answers "what shipped" before anything moves. It pins the exact version of every microservice in the release, so DevOps deploys a known package to Stage and the same package to Production. It blocks both deployment tasks: a deployment cannot start until the package it deploys is defined.

Stage and Production get separate tasks because they are separate events: each has its own ServiceNow ticket, its own scheduled date, and its own outcome. Separate tasks let Stage close when Stage is done and make a Production delay visible on its own ticket.

**Dates live on the Release Dashboard, never in the tickets.** Deployment dates move, and a date copied into a ticket goes stale silently. Release tickets carry no Due Date and no date in their body; the Release Dashboard is the one place a deployment date is set and changed.

The tickets are written from this spec rather than cloned from Jira template tickets. Clone sources drift out of step with the spec and need their own upkeep; one spec, read at creation time, cannot drift.

**Who fills what**

The release tickets are created by the TPM with their structure and links in place. Each remaining piece has one owner, and the tickets do not duplicate data that lives elsewhere.

| Item | Owner | Where it lives |
|---|---|---|
| Ticket structure, Release Dashboard link, issue links | TPM | Each release ticket (at creation) |
| Fix Versions | Developers | Their own story, bug, and finding tickets, never the release tickets |
| Microservice versions and Deployment Package Number | Tech lead, through the Deployment Package task | The Release Dashboard, never the tickets |
| Service Now Cloud One Ticket Link | DevOps engineer | Each deployment task's Tracking table |
| Deployment dates | TPM | The Release Dashboard, never the tickets |

---

## Standing deployments epic

**CTDC-2007, "CTDC Software Release Deployments"**, is the permanent parent of every Deployment Package, Stage, and Production task. Do not create a new one per release.

---

## Final Design QA epic

One per release.

**Fields**

* *Summary*: `Final Design QA: <YYYY> Q<N> Release Candidate`
* *Type*: Epic
* *Assignee*: TPM (`kuffelgr`)
* *Labels*: the current release-line label (for example `Task-1.3`)

**Body**: four sections (🎯 Design QA Summary · 🔎 Findings · 🚦 Workflow · ✅ Definition of Done), Jira wiki markup:

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

## Deployment Package task

One per release, created first; it blocks both deployment tasks.

**Fields**

* *Summary*: `Identify the <YYYY> Q<N> release candidate Deployment Package`. Hotfix release: `Identify the <YYYY> Q<N> hotfix Deployment Package`.
* *Type*: Task
* *Epic Link*: the standing epic CTDC-2007, set with a follow-up `jira_update_issue` on `customfield_12350`.
* *Assignee*: Tech Lead, Nahom Tesfatsion (`nahom.tesfatsion@nih.gov`).
* *Developer* (`customfield_23650`): `[{"name": "tesfatsionnh"}]`.
* *Labels*: `release`, `deployment`
* *Fix Version*: leave empty.
* *Due Date*: leave empty. Schedule this work so it finishes before the Stage deployment date on the Release Dashboard, leaving time to file the Stage ServiceNow request.
* *Issue links*: "blocks" the Stage task and "blocks" the Production task.

**Body**: three sections (🎯 Task Summary · 🚦 Workflow · ✅ Definition of Done), Jira wiki markup:

```
h3. 🎯 *Task Summary*
Identify the version of every microservice shipping in the <YYYY> Q<N> software release candidate and record them, with the Deployment Package Number, on the Release Dashboard, so DevOps deploys one known package to Stage and the same package to Production.

* *Release Dashboard*: [CTDC <YYYY> Q<N> Release Dashboard|<Release Dashboard URL>]

h3. 🚦 *Workflow*
# Confirm the release's Fix Versions are complete for every microservice in the release.
# Record the version of every microservice in the release on the Release Dashboard.
# Record the Deployment Package Number on the Release Dashboard.

h3. ✅ *Definition of Done*
* *Versions*: the version of every microservice in the release is recorded on the Release Dashboard.
* *Fix Versions*: each recorded version matches that microservice's Fix Version for the release.
* *Deployment Package*: the Deployment Package Number is recorded on the Release Dashboard.
```

---

## Stage / Production deployment tasks

Two tasks per release, identical except the environment word (**Stage** / **Production**) and two Production-only phrases that pin Production to the package already verified in Stage (marked below).

**Fields**

* *Summary*: `Deploy <YYYY> Q<N> software release candidate to <Stage | Production>`. Hotfix release: `Deploy <YYYY> Q<N> hotfix release candidate to <Stage | Production>`.
* *Type*: Task
* *Epic Link*: the standing epic CTDC-2007, set with a follow-up `jira_update_issue` on `customfield_12350`.
* *Assignee*: DevOps Lead, Charles Ngu (`charles.ngu@nih.gov`).
* *Developer* (`customfield_23650`): `[{"name": "nguca"}]`. The field rejects a bare username string.
* *Labels*: `release`, `deployment`, `devops`
* *Fix Version*: leave empty. Fix Versions belong to the work being shipped, not to the tasks that ship it.
* *Due Date*: leave empty. The deployment date for each tier is set on the Release Dashboard.
* *Issue links*: both are "blocked by" the Deployment Package task, and the Stage task "blocks" the Production task. Never cite ticket keys in the body.

**Body**: four sections (🎯 Deployment Summary · 🚦 Workflow · 📋 Tracking · ✅ Definition of Done), Jira wiki markup. There is no handoff step or Handoff bullet: the handoff is the ticket's status change in Jira.

```
h3. 🎯 *Deployment Summary*
File an NCI ServiceNow ticket so the CloudOne team reserves the date and time to deploy the <YYYY> Q<N> software release candidate to the CTDC <Stage | Production> environment.

* *Release Dashboard*: [CTDC <YYYY> Q<N> Release Dashboard|<Release Dashboard URL>]

h3. 🚦 *Workflow*
# File the NCI ServiceNow ticket at https://service.cancer.gov/ncisp and add the TPMs ({{kuffelgr}}, {{singletonss}}) as watchers.
# Create an all-day Outlook calendar invite for the scheduled deployment day (exact time added later) and add the TPMs, BE Lead, FE Lead, and CloudOne Engineer.
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

## House style

`h3.` section headers, emoji-prefixed, with bold titles; `#` numbered lists; italic-label `* *Label*: content` bullets; Jira wiki tables (`||header||` header row, `|cell|` body rows); `{{ }}` for monospace. Bodies are written directly in **Jira wiki markup**: Markdown translation is disabled on the mcp-atlassian connector (`DISABLE_JIRA_MARKUP_TRANSLATION=true`, Sep 2026), so Markdown renders as literal `###`, `[text](url)`, and backticks. Push with the two-step create-then-update pattern, escape literal curly braces as `\{` and `\}`, then confirm the render in the Jira UI: the API echo is not a reliable preview. Colons, not em dashes.

---

## Creating each release's tickets

1. Create the Final Design QA epic, the Deployment Package task, and the Stage and Production tasks, each with a placeholder body; then `jira_update_issue` each with the body above and labels. Leave Due Date empty.
2. On the three tasks, set the epic link (`customfield_12350` to CTDC-2007) and Developer (`customfield_23650`), and confirm both with `jira_get_issue`.
3. Swap `<YYYY> Q<N>` and `<Stage | Production>` in the summaries and bodies, resolve the two `[Production only: ...]` phrases (keep the words on Production, delete them on Stage), and paste the release's Release Dashboard link. The Release Dashboard must always be linked; a release task without one is incomplete.
4. Link the Deployment Package task "Blocks" the Stage task and "Blocks" the Production task, and the Stage task "Blocks" the Production task.
5. Open all four tickets in the Jira UI and confirm they render.

---

## Changelog

- **v7.1 (2026-10-22)**: Named the Deployment Package task's default Assignee and Developer: Tech Lead Nahom Tesfatsion (`tesfatsionnh`), matching how ICDC names its tech lead.
- **v7 (2026-10-22)**: Dates live on the Release Dashboard only: release tickets carry no Due Date and no date in their body, because deployment dates change and a copied date goes stale. Ownership table row renamed to "Deployment dates". Matches ICDC v1.5.
- **v6 (2026-10-22)**: Dropped the clone model to match ICDC. The Jira clone sources (CTDC-2135, CTDC-2136, CTDC-2137) no longer exist; the TPM now creates each release's tickets from this spec, as ICDC does. Restructured to ICDC's shape: "Who fills what" ownership table, per-ticket Fields lists, and "Creating each release's tickets" steps. Added the **Deployment Package task** (Tech Lead identifies every microservice version and records the Deployment Package Number on the Release Dashboard; blocks both deployment tasks), matching ICDC v1.4. Deployment tasks now carry the `release`, `deployment`, and `devops` labels, the Developer field, a Due Date, and Stage "Blocks" Production, all matching ICDC. Workflow step 1 now files the ServiceNow ticket as well as adding watchers, and the Release Dashboard bullet is a link, not an instruction.
- **v5 (2026-10-22)**: Final Design QA epic: removed the "Notify the TPM" Workflow step and the *Handoff* Definition of Done bullet; the handoff is the ticket's status change in Jira. Applies the same rule v4 applied to the deploy tasks.
- **v4 (2026-10-22)**: Deploy tasks, matching ICDC Release Ticket Templates v1.3: (1) "Deployment Package" replaces "the build" in Workflow step 4 and the Definition of Done; (2) new *Deployment Package* Definition of Done bullet tying the deployed package to the Deployment Package Number on the Release Dashboard, with Production also requiring the same package verified in Stage; (3) removed the *Handoff* Definition of Done bullet and the "notify the TPM" clause from Workflow step 4, since the handoff is the ticket's status change in Jira.
- **v3 (2026-10-20)**: Deploy tasks, matching the new ICDC Release Ticket Templates (v1.2) so both projects use the same shape: (1) the Tracking table's separate "NCI ServiceNow Ticket" and "URL" columns are merged into one "Service Now Cloud One Ticket Link" column; (2) a ✅ Definition of Done section is added as the fourth section.
- **v2 (2026-10-07)**: House style and both body blocks rewritten in Jira wiki markup (`h3.`, `#` numbered lists, `||header||` tables) to match the connector's disabled Markdown translation; the old note that the MCP converts Markdown server-side was removed. Em dashes replaced. Pointer added to the new DevOps Task template for non-release DevOps work.
- **v1 (2026-07-08)**: Initial component. Template tickets created and labeled `template`: CTDC-2135 (Final Design QA epic), CTDC-2136 (Stage deploy task, under CTDC-2007), CTDC-2137 (Prod deploy task, under CTDC-2007). Live Q4 tickets CTDC-2130 / CTDC-2128 / CTDC-2129 reformatted to this house style.
