# 🎟️ Release Ticket Templates

> **Use this component for the Jira artifacts that recur with every CTDC software release: the Final Design QA epic and the Stage and Production deployment tasks.** This file is the canonical spec and the only template: tickets are created from the bodies below, never cloned from Jira template tickets. Real tickets are cited as canonical examples. Companion: the Software Release Playbook (`software-release-playbook.md`). For DevOps work that is not a release promotion (upgrades, environment variables, infrastructure changes), use the DevOps Task template (`devops-task-template.md`). ICDC's equivalent, kept in lockstep, is `CBIIT/icdc-documentation/claude/templates/release-ticket-templates.md`.

**Why this component**

These tickets are created for every release. When each is hand-built from memory, formatting drifts release to release (escaped markup, inconsistent sections, stale ticket references). Keeping one canonical body per artifact here, with real tickets as examples, keeps every release's tickets identical in shape, so reviewers and automation can rely on the structure.

---

## Release kinds

| Kind | What ships | Deployment task summary |
|---|---|---|
| **Monthly** | Security remediation plus whatever feature work is ready | `Deploy <Month YYYY> monthly release to CTDC <Stage \| Production>` |
| **Feature** | One feature's scope (for example, RAS) | `Deploy <YYYY> <Feature> release to CTDC <Stage \| Production>` |

## Canonical examples

| Artifact | Canonical example |
|---|---|
| Final Design QA epic | **CTDC-2130** |
| Stage deployment task (feature release) | **CTDC-2253** (2026 RAS release) |
| Production deployment task (feature release) | **CTDC-2254** (2026 RAS release) |
| Stage / Production deployment tasks (monthly release) | ICDC's **ICDC-4291** / **ICDC-4292** (same body; CTDC differences noted below) |

---

## Deployment Package Number

The identifier of what is deployed. It is the `CBIIT/ctdc-deployment` git tag created by the **CTDC Create Release** workflow (`<frontend version>.<build number>`, for example `1.5.0.8.3`), and it is the `release_number` input to the **CTDC Deploy Release** workflow. One tag pins all five service images (frontend, backend, files, authn, interoperation) and the tier configs as they were when the package was created, so deploying the same number to Stage and Production deploys the same code.

* *Owner*: tech lead.
* *Where it lives*: the Release Dashboard, never the tickets. The deployment tasks' Definition of Done checks the deployed package against it.
* *Config changes*: tier configs are frozen into the package at creation. A config change made after the package is created needs a new package.

---

## House style (all artifacts)

`h3.` section headers, emoji-prefixed, with bold titles; slim shape; `#` numbered lists; italic-label `* *Label*: content` bullets; Jira wiki tables (`||header||` header row, `|cell|` body rows); `{{ }}` for monospace. Bodies are written directly in **Jira wiki markup**: Markdown translation is disabled on the mcp-atlassian connector (`DISABLE_JIRA_MARKUP_TRANSLATION=true`, Sep 2026), so Markdown pushed through `jira_update_issue` renders as literal `###` and `**` characters. Push with the two-step create-then-update pattern, escape literal curly braces as `\{` and `\}`, then confirm the render in the Jira UI: the API echo is not a reliable preview. Colons, not em dashes. Never cite ticket keys in a body: Jira's links panel carries them.

---

## Final Design QA epic

* *Summary*: `Final Design QA: <release name>`
* *Type*: Epic (standalone; epics don't nest)
* *Assignee*: TPM (`kuffelgr`)
* *Labels*: the release-line label

Four sections: 🎯 Design QA Summary · 🔎 Findings · 🚦 Workflow · ✅ Definition of Done.

```
h3. 🎯 *Design QA Summary*
Conduct a thorough final design review of every new feature and page implemented during this release cycle, verifying design fidelity and Section 508 accessibility compliance before the release is promoted to Stage.

h3. 🔎 *Findings*
Log each discrepancy below and file it as a bug linked to this epic, grouped by page or feature.

||Feature||Page||508 Compliance||Design Discrepancy Comment||Figma Link||JIRA Bug Ticket||
| | | | | | |

h3. 🚦 *Workflow*
# When the release Slack channel signals QA regression has started in the QA tier, begin Final Design QA on the QA environment.
# Review every new feature and page for design fidelity and Section 508 compliance.
# File each discrepancy as a bug linked to this epic; announce it in the release channel.
# Notify the TPM when Final Design QA is complete.

h3. ✅ *Definition of Done*
* *Review*: every new feature and page checked for design and 508 compliance.
* *Findings*: all discrepancies logged and filed as linked bugs.
* *Blockers*: any release-blocker findings routed to Engineering before promotion.
* *Handoff*: TPM notified.
```

---

## Stage / Production deployment tasks

Two tasks per release, identical except the environment word (**Stage** / **Production**) and the Production-only wording marked below.

**Fields**

* *Summary*: per the Release kinds table.
* *Type*: Task
* *Epic Link*: the standing epic **CTDC-2007** "CTDC Software Release Deployments", set with `jira_update_issue` on `customfield_12350` (`"CTDC-2007"`). Do not use `jira_link_to_epic`: on `tracker.nci.nih.gov` it reports success without setting the field.
* *Assignee*: DevOps Lead (`nguca`).
* *Labels*: `release`, `deployment`, `devops`
* *Issue links*: the Stage task "blocks" the Production task. Feature release: both tasks "relate to" the feature epic. Monthly release: both tasks "relate to" the scan or feature epics whose work ships.

**Body**: four sections (🎯 Deployment Summary · 🚦 Workflow · 📋 Tracking · ✅ Definition of Done), Jira wiki markup. `<release name>` is `<Month YYYY> monthly release` or `<YYYY> <Feature> release`.

```
h3. 🎯 *Deployment Summary*
File an NCI ServiceNow ticket so the CloudOne team reserves the date and time to deploy the <release name> to the CTDC <Stage | Production> environment.

* *Release Dashboard*: [<Release Dashboard name>|<Release Dashboard URL>]

h3. 🚦 *Workflow*
# Add the TPMs ({{kuffelgr}}, {{singletonss}}) as watchers on the NCI ServiceNow ticket.
# Create an all-day Outlook calendar invite for the scheduled deployment day (exact time added later) and add the TPM, BE Lead, FE Lead, and CloudOne Engineer.
# Send the Outlook invite to the assigned CloudOne member and the TPMs.
# Deploy the Deployment Package <to Stage | verified in Stage to Production> on the scheduled date, confirm the {{/#/sysinfo}} page shows the versions recorded in the Release Dashboard, and notify the TPM.
# Fill in the Tracking table as information becomes available.

h3. 📋 *Tracking*
||Service Now Cloud One Ticket Link||Assignee||Notes about Deployment||
| |(CloudOne point of contact)|(nuances or issues encountered)|

h3. ✅ *Definition of Done*
* *ServiceNow*: the Service Now Cloud One ticket is linked in Tracking and closed by CloudOne as successful.
* *Deployment Package*: the package deployed matches the Deployment Package Number on the Release Dashboard<Production only: and is the same package verified in Stage>.
* *Deployment*: the Deployment Package is deployed to <Stage | Production> in the scheduled window.
* *Verification*: the {{/#/sysinfo}} page on <Stage | Production> shows the versions recorded in the Release Dashboard, and the application loads and returns data.
* *Release Dashboard*: the <Stage | Production> deployment date is recorded.
* *Handoff*: TPM notified.
```

**CTDC differences from ICDC**: two TPM watchers; verification uses the `/#/sysinfo` page (CTDC has no `/version` endpoint; `/#/sysinfo` lists every microservice's version).

---

## Creating each release's tickets

1. Create the Stage task with a placeholder body, then `jira_update_issue` with the body above and the labels. Repeat for the Production task.
2. Set the Epic Link (`customfield_12350` = `CTDC-2007`) on each and confirm with the JQL `"Epic Link" = CTDC-2007`.
3. Fill `<release name>` and `<Stage | Production>` in the summary and body, and paste the release's Release Dashboard link. A deployment task without one is incomplete.
4. Link Stage "Blocks" Production, and link both "Related To" the feature or scan epics the release ships.
5. Create the Final Design QA epic from its body when the release includes new features or pages.
6. Open every ticket in the Jira UI and confirm it renders.

---

## Changelog

- **v4 (2026-10-20)**: Moved to the ICDC model: this spec is the only template, and real tickets are cited as canonical examples. The deploy-task template tickets CTDC-2136 and CTDC-2137 are retired (deleted). Two release kinds (monthly, feature) with summary patterns; CTDC-2253 / CTDC-2254 are the canonical feature-release example. "Software release candidate" replaced by the Deployment Package; the Deployment Package Number is defined (the `ctdc-deployment` release tag, owned by the tech lead, kept on the Release Dashboard), and the Definition of Done checks the deployed package against it, with Production required to match the package verified in Stage. Workflow and Definition of Done verify with the `/#/sysinfo` page. Deploy tasks gain the `release`, `deployment`, `devops` labels and ICDC's issue-link rules. Epic Link is set through `customfield_12350` and confirmed by JQL, since `jira_link_to_epic` reports success without setting it. Creation steps replace cloning steps.
- **v3 (2026-10-20)**: Deploy tasks, matching the new ICDC Release Ticket Templates (v1.2) so both projects use the same shape: (1) the Tracking table's separate "NCI ServiceNow Ticket" and "URL" columns are merged into one "Service Now Cloud One Ticket Link" column; (2) a ✅ Definition of Done section is added as the fourth section. The Jira template tickets CTDC-2136 and CTDC-2137 need the same two changes.
- **v2 (2026-10-07)**: House style and both body blocks rewritten in Jira wiki markup (`h3.`, `#` numbered lists, `||header||` tables) to match the connector's disabled Markdown translation; the old note that the MCP converts Markdown server-side was removed. Em dashes replaced. Pointer added to the new DevOps Task template for non-release DevOps work. The Jira template tickets (CTDC-2135, CTDC-2136, CTDC-2137) were not changed.
- **v1 (2026-07-08)**: Initial component. Template tickets created and labeled `template`: CTDC-2135 (Final Design QA epic), CTDC-2136 (Stage deploy task, under CTDC-2007), CTDC-2137 (Prod deploy task, under CTDC-2007). Live Q4 tickets CTDC-2130 / CTDC-2128 / CTDC-2129 reformatted to this house style.
