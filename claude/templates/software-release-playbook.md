# 🚀 CTDC Software Release Playbook

> **Use this playbook for every CTDC software release, from planning through Feature Freeze, Release Candidate (RC) build, promotion up the environment ladder, production ("Gamma") deployment, close-out, and retro.** It is the standing process document. Its authoritative parent is the **CTDC Team Charter: Build, Deploy, & Release Process**; this playbook operationalizes the Charter into an executable, phase-by-phase runbook. The per-release *instrument* is the **Release Dashboard** (`.xlsx`); the per-release *schedule* is the **Release Roadmap** deck. When any artifact disagrees, precedence is: **Team Charter → this playbook → dashboard/roadmap**.

**Why this playbook**

The Team Charter defines the *rules* (versioning, Gitflow, Jira workflow, freezes, Definition of Done). The Release Dashboard is the *checklist* for one release. The Roadmap is the *schedule*. None of them, alone, walks a release manager phase-by-phase from kickoff to close with the gates, RACI, and rollback spelled out. This playbook is that connective runbook, written down so the process is durable, diffable in git, and legible to a new team member on day one.

**Operating context.** Part of the CTDC operational skill (`CBIIT/ctdc-documentation → claude/SKILL.md`). Jira project key **CTDC**, board **641** (the Charter's older `rapidView=1137` link is a retired board). The development team is the **Essential Software Inc (ESI)** subcontractor under FNL/BACS (NCI prime); **federal leadership sits in NCI/CBIIT**. Jira-driven steps (scope pull, fix_version hygiene, the Final Design QA epic, the version-merge) require the **Docker/Atlassian MCP container running**; if it is down, release data work stops until restored.

**Software releases vs. data releases.** Strictly separate; never conflated. This playbook covers *software* (versioned application components). Study loads, data-model versions, and OpenSearch index rebuilds are *data* releases with their own tasks. Data-team work may run in parallel on the roadmap but does not gate a software release.

**Cadence.** Targeted **quarterly** (`YYYY QN` naming), scope-readiness driven. The team runs **3-week sprints**; a release spans roughly **three sprints**, and the **last sprint of the cycle is the "Testing Sprint"** (feature freeze is issued at its start). Off-cycle releases (hotfixes, security bumps) follow this same playbook.

---

## 🎫 Jira Mechanics of a Release

How work becomes a release *inside Jira*. This is the backbone the Charter defines and the dashboard assumes.

### Workflow states (verified against live CTDC tickets)

| State | Category | Meaning |
|---|---|---|
| **Open** | To Do | Triaged, not started |
| **In Progress** | In Progress | Developer actively working |
| **Ready for Review** | In Progress | PR opened; **assigned to the Technical Lead** for code review |
| **Ready for QA Testing** | In Progress | Merged + built + deployed to QA; **assigned to the QA Lead** |
| **Testing** | In Progress | QA actively testing on the QA environment |
| **Reopened** | In Progress | QA test failed; **reassigned to the original developer** to fix |
| **On Hold** | In Progress | Blocked/parked with reason |
| **Closed** | Done | Passed QA; terminal state (this is the team's "Done") |

Issue types: **Epic · User Story · Task · Bug.** The lifecycle: a feature branches off `develop`, a PR moves its issue to *Ready for Review* (Tech Lead), and on merge + QA deploy it becomes *Ready for QA Testing* (QA Lead). Test pass → *Closed*; test fail → *Reopened* → back to the developer.

### Internal releases, `fix_version`, and release notes

- **Weekly builds are "internal releases"** — perpetual-beta Docker builds handed to QA, named `major.minor.patch.jenkins-build-number` (e.g., **1.0.0.285**).
- Every Jira issue in a build is stamped with that build's **`fix_version`**, creating a durable map between completed issues and the build that carried them. *(Field: `fixVersion`; per component.)*
- Approaching the quarterly release, the latest beta (post feature + code freeze) is designated the **Release Candidate**. At that point **all the prior internal-release versions are merged in Jira** into the single finalized quarterly production version — which is what **generates the official release notes** for the Gamma build.

### Beta → Release Candidate → Gamma (release maturity)

**The core mental model.** Development never stops. Between production releases, the team is continuously building, iterating, and pushing new incremental builds — first to **dev**, then to **qa** for QA to test. **Every one of these internal builds is a "beta release."** They are "perpetual beta": each new build just increments the **build number** (the 4th digit), and they stay on dev + qa. Betas are *never* pushed to stage or prod.

Only when a beta build is stable and complete enough — after Feature Freeze and Code Freeze — does the team **identify it as the Release Candidate (RC)**. The RC is the single beta build chosen to become the release. Only the RC is promoted up the rest of the ladder: to **stage**, then to **prod**. Once it passes all verification there, that RC becomes the **gamma release** — the final, stable, public production build.

In short: **beta = the many internal builds we test on dev/qa; gamma = the one build we promote all the way to prod.** The RC is the hand-off point between them.

| Environment | Release maturity |
|---|---|
| **dev** | Beta (internal) — newest builds land here first |
| **qa** | Beta (internal) — QA tests every beta build |
| **stage** | **Release Candidate (RC)** — the chosen beta, promoted for pre-prod validation |
| **prod** | **Gamma** — the final released build |

**Versioning walk-through (`major.minor.patch.build`).** Say production currently runs **`1.3.0.31`** — that was last cycle's gamma (build 31 of the 1.3.0 line). Starting the next cycle, the release version bumps per SemVer based on what's going in — here a **minor** bump for new features, `1.3.0 → 1.4.0`. The first internal build is **`1.4.0.1`**. Through the sprints, every new beta build increments the build number: **`1.4.0.2`, `1.4.0.3`, … `1.4.0.x`** — all betas, all on dev/qa. When `1.4.0.x` is deemed ready, it's identified as the RC, promoted to stage → prod, and becomes the next **gamma release**. *(The one exception, and it's uncommon: a **release-blocker fix** bumps the **patch** — the 3rd digit — of the RC, not the build number, e.g. `1.4.0.x → 1.4.1.x`. Everything else through the cycle just moves the build number.)*

### Bugs Parking Lot & triage

- A bug found by QA or anyone else is **linked to its originating issue** (when applicable) and **announced in Slack**. The default channel for **all bug reporting and triage** is `#ctdc-test-questions`; **while a Release Candidate is being actively prepared** (Testing Sprint through release), bug reporting and triage move to the per-RC channel `#ctdc-<year>-q<N>-release`.
- New bugs are **assigned to the TPM with no priority** and dropped into the **Bugs Parking Lot**.
- The **TPM + co-TPM** review and prioritize, and decide whether to pull a bug into the active sprint. During code freeze, only **release-blocker** bugs may be pulled in.

### Semantic Versioning (semver.org, `x.y.z`)

- **Major (`x`)** — incompatible/breaking change (e.g., removing an operation). `1.2.4 → 2.0.0`.
- **Minor (`y`)** — new functionality, backward-compatible (e.g., a new feature). `1.2.4 → 1.3.0`.
- **Patch (`z`)** — backward-compatible bug fix. `1.2.4 → 1.2.5`. Release-blocker fixes bump the patch (`z+1`).

---

## 📆 Milestones & Freeze Calendar

Four scheduled milestones. The Roadmap deck sets dates; the Charter sets the minimum durations.

| Milestone | Relative timing | Definition | QA duration (Charter minimum) |
|---|---|---|---|
| **Feature Freeze** | Start of the Testing Sprint (~T-3 wks) | All user stories complete; **no new feature work**. Bugs may still be reported/triaged. Opens the bug-fix window. | Initial RC testing **≥ 5 business days** |
| **Code Freeze** | ~T-2 wks | **Entire codebase frozen.** Clean RC build to QA; only release-blocker fixes allowed after. | Full regression on QA **≥ 3–5 business days** |
| **Stage Deployment** | ~T-1 wk | RC promoted to Stage after QA regression passes. Final Design QA must be complete by now. | Full regression on Stage **≥ 2 business days** |
| **Release (Gamma)** | T-0 | QA signs off; deploy to production. Remaining low bugs are "acceptable." | — |

> **The release date floats.** Any **release-blocker fix resets the QA clock** — an additional **3–5 business days** of full regression on the new build — pushing the release date out. Communicate slips in the RC channel.

**Feature Freeze vs. Code Freeze — plainly:** Feature Freeze = "stop adding *features*" (scope locked, bugs still fixable). Code Freeze = "stop changing *anything*" (features *and* bugs closed; only regression + release-blocker fixes after). Roadmap effort bars (Q3 2026 example): ~10 days Feature→Code, ~5 + ~5 days after.

---

## 🧩 Scope: What Ships in a Release

Not every component bumps every release; the dashboard **Release Info** tab records the exact version matrix.

| Component | Repo (default branch) | Notes |
|---|---|---|
| **Front-End (FE)** | `CBIIT/crdc-ctdc-ui` (`main`) | React/Bento app; `x.y.z`, blocker fixes bump patch (`z+1`) |
| **Back-End (BE)** | `CBIIT/crdc-ctdc-backend` (`master`) | GraphQL API layer |
| **File Service** | `CBIIT/crdc-ctdc-files` | Signed-URL file delivery (IndexD) |
| **Auth** | `CBIIT/crdc-ctdc-authn` | Fence-brokered OAuth2 login |
| **Interoperation** | `CBIIT/crdc-ctdc-interoperation` (`main`) | Cross-commons interoperation service |
| **DMN Core** | `CBIIT/Data-Model-Navigator` (`main`) | Data Model Navigator (embedded); shared Bento component |
| **DMN Standalone** | *(confirm — likely the same `Data-Model-Navigator` repo, standalone build)* | Data Model Navigator (standalone) |
| **OpenSearch DB backup** | *(internal)* | Snapshot version when a restore is required (also the rollback point) |

**Release Info tab fields:** Expected Production Release Date · Build Date · Release Package Number · FE/BE/File Service/Auth/Interoperation versions · OpenSearch backup version · DMN Standalone · DMN Core · Release Blockers Identified · Regression Test Status on QA.

**Where release notes live:** the merged quarterly `fix_version` generates notes published to GitHub at **`https://github.com/CBIIT/crdc-ctdc-starter-kit/releases`**.

---

## 👥 Roles, Swimlanes & RACI

Two swimlane lenses: **Execution** (dashboard) = Engineering · QA · Management · DevOps · UI-UX; **Planning** (roadmap) = FE · BE · DevOps · QA · Data. Same ESI team.

| Execution swimlane | Role | Current holder(s) | Owns |
|---|---|---|---|
| **Engineering** | Technical Lead + engineers | Yizhen Chen (lead), Nahom Tesfatsion (FE), Adam Davenport (BE/Jenkins), Eric Miller (BE) | RC build, code review, blocker fixes, tags, final merges |
| **QA** | QA Lead | Valentina Epishina | Katalon + manual smoke/regression at each tier; promotion sign-off |
| **Management** | TPM + co-TPM | Gina Kuffel (TPM), Stephanie Singleton (co-TPM) | Authorization gate, bug triage, release notes, all announcements |
| **DevOps** | DevOps Lead | Charles Ngu | CloudOne/ServiceNow scheduling, stage/prod deploys, infra + monitoring |
| **UI-UX** | Designer | Hannah Stogsdill | Final Design QA (incl. 508), filing design bugs |
| **Data** *(roadmap lane)* | Data engineer | Patrick Breads | Feature-side data work; **data *releases* tracked separately** |

**The promotion gate (repeats at every tier):**

> **QA verifies → TPM authorizes → DevOps deploys.**

The TPM is the single authorization pivot; no untested build jumps a tier.

---

## 🗂️ Artifacts & Sources of Truth

| Artifact | Where | Role |
|---|---|---|
| **Team Charter** | SharePoint (Build, Deploy & Release Process) | The rules (top authority) |
| **This playbook** | `claude/templates/software-release-playbook.md` | The runbook (process) |
| **Release Roadmap deck** | `.pptx`, produced at kickoff | The **schedule**: freeze dates + sprint plan |
| **Release Dashboard** | Cloned per release from `CTDC_Release_Dashboard_MASTER.xlsx` | The per-release **instrument** |
| **Per-RC Slack channel** | `#ctdc-<year>-q<N>-release` | RC coordination, build announcements, and **bug reporting/triage while a RC is being prepared** |
| **Bug intake channel** | `#ctdc-test-questions` | **Default channel for all bug reporting and triage** (except during RC preparation) |
| **Final Design QA epic** | Jira, one per RC (example: **CTDC-2130**) | Design + 508 findings; discrepancies filed as linked bugs |
| **CTDC operational skill** | `SKILL.md` | Umbrella SOP: JQL, deck standards, roster, Slack |
| **Release Tagging Hygiene Checklist** | `SKILL.md` §13 | Pre-announce fix_version/Developer-field verification + JQL |
| **fixVersion release query** | `SKILL.md` §4 | Pull full multi-component scope |
| **Demo deck / Slack announce** | `SKILL.md` §11–12 / §14 | Demo Day deck + prod announcement format |

---

## 🌐 Environments & Promotion Ladder

One linear ladder, no skips: `dev → qa → stage → prod`.

| Env | URL | Maturity | Role |
|---|---|---|---|
| **Dev** | `https://clinical-dev.datacommons.cancer.gov` | **Beta** | Engineering integration; newest beta builds land first |
| **QA** | `https://clinical-qa.datacommons.cancer.gov` | **Beta** | Katalon + manual regression on every beta; first sign-off |
| **Stage** | `https://clinical-stage.datacommons.cancer.gov` | **Release Candidate (RC)** | The chosen beta, promoted for pre-prod validation; go/no-go rehearsal |
| **Prod** | `https://clinical.datacommons.cancer.gov` | **Gamma** | Public release (final build) |

---

## 🌿 Versioning & Branching (Gitflow)

- **Feature work:** each feature in its own branch off `develop`; PR merges it back → Jira issue to *Ready for Review* (Tech Lead) → merge on approval.
- **Weekly build:** Tech Lead reviews all *Ready for Review* issues, stamps `fix_version`, forks `develop` into a dockerized build, deploys to QA, moves issues to *Ready for QA Testing* (QA Lead).
- **Release Candidate:** cut from `develop` after Feature Freeze; stabilized on the RC branch.
- **Blocker fixes:** new branch from the most recent released-version branch, named `x.y.(z+1)`; PR → new build → QA. **Resets the QA clock.**
- **Close-out merge:** RC merged into **`main`** and tagged with the version; also merged back into **`develop`**; the release branch is deleted.
- **Build naming:** `major.minor.patch.jenkins-build-number` (e.g., 1.0.0.285).

---

## 💬 Communication Channels

| Channel | Workspace | Purpose |
|---|---|---|
| `#ctdc-<year>-q<N>-release` | CTDC Slack | **Per-RC channel** (kickoff-created): build announcements, QA regression/promotion posts, slip notices, and **all bug reporting/triage while the RC is being prepared**. Example `#ctdc-2026-q4-release`. |
| `#ctdc-test-questions` | CTDC Slack | **Default channel for all bug reporting and triage** — used except when a Release Candidate is actively being prepared |
| `#ctdc-discussions` (`CQ4ABNA4T`) | CTDC Slack | Team-wide news; backup of major posts |
| `#tech_channel` | **NCI CRDC** workspace | **External** production release announcement |

---

## 🔁 The Release Lifecycle (Phases)

### Phase 0 — Planning & Kickoff *(TPM)* · around Feature Freeze
1. Produce/update the **Release Roadmap deck** (four milestone dates + sprint plan).
2. Create `#ctdc-<year>-q<N>-release`.
3. Clone a fresh Release Dashboard from the master; fill **Release Info**.
4. Create the **Final Design QA epic** (model CTDC-2130) with the 508 column.
5. Confirm scope via the `fixVersion in (...)` query (SKILL.md §4). *(MCP required.)*

**Gate → Phase 1: FEATURE FREEZE** — all user stories complete; no new feature work.

### Phase 1 — Stabilize the Release Candidate *(Engineering)* · Feature Freeze → Code Freeze
1. Fork `develop` → **RC branch**.
2. **Document OpenSearch requirements** (record the snapshot version = rollback point).
3. Work the bug-fix window (~10 days): triaged bugs, reopened bugs, blockers. Each blocker build gets its own `z+1` patch.
4. Move build PRs' issues to *Ready for QA Testing*, assign the QA Lead.
5. Provide the RC build to QA; **announce in the RC channel**.

**Gate → Phase 2: CODE FREEZE** — codebase frozen; clean RC to QA.

### Phase 2 — QA on the QA Environment *(QA)* · ≥ 3–5 business days
1. Verify metadata against the Release Info tab.
2. Manual **smoke test**.
3. **Katalon** automated full regression; results to SharePoint.
4. Post to the RC channel that **regression has started**.
5. Manual regression; report to SharePoint. Failures → issue to *Reopened* → developer.
6. Post to verify the RC can promote to Stage.

**Gate → Phase 4:** QA's written promote-to-Stage verification to Tech Lead **and** TPM.

### Phase 3 — Final Design QA *(UI-UX)* — parallel with Phase 2, **complete before Stage**
1. Conduct Final Design QA on the QA environment.
2. Check design fidelity **and 508 compliance** on every new feature/page.
3. File each discrepancy as a **bug linked to the Final Design QA epic** (CTDC-2130 pattern), grouped by page/feature; announce in the RC channel.
4. Notify the TPM when complete.

**Gate → promotion:** any design/508 **release blocker** returns to the Phase 1 blocker loop.

### Phase 4 — Authorize & Deploy to Stage *(TPM → DevOps)* · Stage Deployment milestone
1. **TPM:** review Final Design QA + regression report; confirm no open blockers.
2. **TPM:** on QA's verification, **notify DevOps** to deploy to Stage.
3. **DevOps (pre-scheduled):** ServiceNow ticket + Outlook invite for the **CloudOne** Stage window (CTDC-2128).
4. **DevOps:** deploy to Stage; run Environment/Logging/Alert checks; notify TPM.

### Phase 5 — QA on Stage *(QA)* · ≥ 2 business days
1. Verify build accuracy on Stage.
2. Manual smoke → **Katalon** regression → manual regression; all to SharePoint.

**Gate → Phase 6 (GO/NO-GO):** QA's written promote-to-Prod verification (as a Gamma build) to Tech Lead **and** TPM.

### Phase 6 — Authorize & Deploy to Production (Gamma) *(TPM → DevOps)* · Release milestone
1. **TPM:** on QA's verification, **notify DevOps** to deploy to Production.
2. **DevOps (pre-scheduled):** ServiceNow ticket + Outlook invite for the CloudOne Prod window (CTDC-2129).
3. **DevOps:** deploy to Production; notify TPM.

### Phase 7 — QA on Production *(QA)*
1. Verify the deployment; manual smoke → **Katalon** regression → manual regression.
2. Post to the RC channel that **production is stable**.

**Gate → Phase 8:** QA Lead confirms prod stable. *(If not → Rollback & Recovery.)*

### Phase 8 — Close-Out *(Engineering + TPM)* — the "Definition of Done"
1. **Engineering:** create the **GitHub tag** for the Release Notes.
2. **Engineering:** merge the RC into **`main`** (tagged) **and `develop`**; **delete the release branch**.
3. **Engineering:** OpenSearch restore **only if required**; verify data alignment.
4. **TPM:** **merge the internal-release `fix_version`s in Jira** into the finalized quarterly version; generate + publish **release notes** to GitHub.
5. **TPM:** run the **Release Tagging Hygiene Checklist** (SKILL.md §13). **Verify before you count** — script tallies over raw Jira JSON, `resolutiondate` as the authoritative completion signal.
6. **TPM:** courtesy email to the full leadership list — **FNL:** Todd Pihl, John Otridge, Mark Jensen; **CBIIT:** Erika Kim, Tanja Davidsen, Esmeralda Casas-Silva (Emi, CTDC Federal Lead).
7. **TPM:** production announcement in the NCI CRDC `#tech_channel` (SKILL.md §14).
8. **TPM:** engage the **CRDC communications team**.

**Definition of Done:** all user-story requirements implemented + tested; all Design QA addressed or deprioritized to backlog; RC tagged on `main`, deployed to Stage + Prod, merged back to `develop`, release branch deleted.

### Phase 9 — Retrospective *(Whole team)*
1. Retro in **Liked · Lacked · Learned** (10-min silent brainstorm; confirm the retrotool.io board URL with the TPM).
2. Resolve any tagging gaps from Phase 8 (back-fill vs. process fix; never paper over — SKILL.md §13c).
3. Capture a **lessons-learned** entry.

---

## 🚦 Go/No-Go Criteria (Stage → Prod / Gamma)

**Go** requires all of:
- ✅ Katalon + manual regression **passed on Stage** (results in SharePoint).
- ✅ **Zero open release-blocker bugs** (incl. Final Design QA / 508).
- ✅ Release Info version matrix **matches** what is deployed on Stage.
- ✅ DevOps Environment/Logging/Alert checks **green** on Stage.
- ✅ QA's written promote-to-Prod verification to Tech Lead **and** TPM.
- ✅ CloudOne production window scheduled (ServiceNow + Outlook).

**Decision owner:** TPM with the Technical Lead. Record the decision in the dashboard Notes + RC channel. Unresolved blockers **move the date** (+3–5 days); they are not waived.

---

## 🔥 Hot Fixes *(post-release path)*

A hotfix bypasses the normal cycle to patch **production** fast.

- Technical Lead designates a **`hotfix_branch`** based on **`main`** (not `develop`).
- On completion, merge into **both `main` and `develop`**; tag `main` with `x.y.(z+1)`.
- DevOps deploys up the ladder (fast-tracked) with QA smoke verification.
- A hotfix is **not** a rollback: it rolls the fix *forward*. Use Rollback & Recovery only when a bad deploy must be reverted.

---

## ↩️ Rollback & Recovery *(DRAFT — needs team validation)*

> The dashboard is forward-only; no rollback is documented. This is a proposed default. **Confirm every specific with Engineering + DevOps before finalizing.**

**Trigger:** prod smoke/regression failure or a Sev-1 defect post-Gamma.
**Decision owner:** TPM with the Technical Lead; announced in the RC channel.
**Mechanism (proposed):**
1. App components are versioned Docker containers via CloudOne → **redeploy the previous known-good version** per component (targeted, not necessarily whole-release).
2. **OpenSearch:** restore the pre-release snapshot recorded in Phase 1; verify data alignment.
3. Confirm Redis, containers, Sumologic, and New Relic green on the restored version.

**Comms:** TPM posts cause + action to the RC channel; notifies CBIIT leadership.
**Post-mortem:** a lessons-learned entry is mandatory.
*Open questions: (a) per-component revert vs. whole-release only? (b) acceptable prod recovery-time target? (c) who may deploy outside a CloudOne window?*

---

## 🩺 DevOps Environment Checklist (Stage + Prod)

**Cloud One:** ServiceNow ticket + Outlook invite for the **Stage** window (CTDC-2128) and **Prod** window (CTDC-2129).
**Environment:** Redis listening on endpoint · BE/FE/auth/files containers running on Docker · OpenSearch connection strings set.
**Logging:** Sumologic collectors active · correct categories per collector.
**Alerts (New Relic):** CTDC hosts present · apps in APM · synthetics (URL) monitors · alert policies + notification channels · required dashboards.

---

## 🕐 T-Minus Timeline (relative)

| When | Milestone / activity | Owner |
|---|---|---|
| **T-3 wks** | **Feature Freeze** (Testing Sprint starts). Roadmap, RC channel, dashboard, Final Design QA epic, scope. | TPM |
| **T-3 → T-2 wks** | RC branch; ~10-day bug-fix window; OpenSearch snapshot; RC to QA. | Engineering |
| **T-2 wks** | **Code Freeze.** RC to QA; Katalon + manual regression begins (≥3–5 days). Final Design QA in parallel. | Eng / QA / UI-UX |
| **T-1 wk** | **Stage Deployment.** QA regression on Stage (≥2 days); go/no-go rehearsal. | DevOps / QA |
| **T-0** | **Release (Gamma).** Prod deploy; regression; tag; merge; version-merge in Jira; release notes; comms. | All |
| **T+** | Retro (Liked·Lacked·Learned); lessons-learned; tagging back-fill. | Whole team |

> **+3–5-day blocker reset:** any release-blocker fix restarts the QA regression clock and pushes T-0.

---

## 🧼 Master Dashboard Hygiene (when cloning per release)

- **No hidden sheets — ever.** Verify:
  ```bash
  python3 -c "from openpyxl import load_workbook; wb=load_workbook('DASHBOARD.xlsx'); \
  bad=[ws.title for ws in wb.worksheets if ws.sheet_state!='visible']; \
  print('HIDDEN SHEETS:', bad or 'none — clean')"
  ```
- **No project cross-contamination** (scrub carried-over ICDC tickets/Figma/names).
- **Blank all values, keep all structure.**
- **Verify hyperlinks resolve** (no `http://https:/...`).
- **Consistent tab labels** (A1 title matches tab).
- **Clean step numbering** (no duplicates within a swimlane).

---

## 📖 Glossary

- **Beta release:** any internal build pushed to **dev** then **qa** for testing. The team ships betas continuously ("perpetual beta"); each increments the build number (4th digit) and never leaves dev/qa. All internal releases are beta releases.
- **Internal release:** synonym for a beta build; named `major.minor.patch.jenkins-build-number`.
- **Release Candidate (RC):** the single beta build designated for release after Feature + Code Freeze — the one promoted from qa up to stage → prod. Stabilized on the RC branch.
- **Gamma release:** the RC after it passes all verification and reaches **prod** — the final, stable, public build. (Beta = the many internal builds; Gamma = the one that ships.)
- **Feature Freeze:** all user stories complete; no new feature work; bugs still fixable. Opens the bug-fix window (Testing Sprint start).
- **Code Freeze:** entire codebase frozen; only release-blocker fixes after.
- **Testing Sprint:** the last sprint of the release cycle, when feature freeze is issued and QA rigorously evaluates the RC.
- **Stage Deployment:** promotion of the RC to Stage for pre-prod validation.
- **Release blocker:** a major/critical/blocker defect that stops promotion; incurs the +3–5-day QA reset.
- **Bugs Parking Lot:** the unprioritized Jira holding area for new bugs, triaged by TPM + co-TPM.
- **Hot Fix:** a post-release production patch off `main` via `hotfix_branch`, tagged `x.y.(z+1)`.
- **Katalon:** the automated regression test suite; results archived in SharePoint.
- **fix_version:** the Jira field mapping issues to the build/release that carried them.
- **508 Compliance:** U.S. Section 508 federal accessibility law; checked in Final Design QA every release.
- **CloudOne:** the deploy team engaged via ServiceNow ticket + scheduled window for Stage/Prod.
- **SemVer (`x.y.z`):** major (breaking) · minor (compatible feature) · patch (compatible fix).
- **FAIR:** Findable, Accessible, Interoperable, Reusable — the data principles CTDC serves.

---

## 🗒️ Change Log / Lessons Learned

| Date | Change | Author |
|---|---|---|
| v1 | Initial playbook from the 2026 Q4 Release Dashboard, CTDC-2130, SKILL.md §4/§11–13/§14. | Gina Kuffel |
| v2 | Integrated the Q3 2026 Release Roadmap deck: Feature Freeze milestone, freeze calendar, T-minus timeline, blocker-slip rule, 3-week sprint cadence, Data Team lane; Command Center context (ESI/CBIIT, board 641, SKILL umbrella, Docker/MCP, verify-before-count, Liked·Lacked·Learned). | Gina Kuffel |
| v3 | Integrated the Team Charter (Build, Deploy & Release Process v4.1.0): new Jira Mechanics section (live-verified workflow states, fix_version/internal-release model, version-merge for release notes, Bugs Parking Lot, SemVer); Katalon in QA phases; Hot Fixes section; Testing Sprint concept + Charter durations; Definition of Done in close-out; `#ctdc-test-questions` intake channel. | Gina Kuffel |
| v4 | Added the **Beta → Release Candidate → Gamma** release-maturity model: explicit explainer + env-maturity mapping (dev/qa = beta, stage = RC, prod = gamma) + `1.3.0.31 → 1.4.0.x` versioning walk-through; glossary Beta-release entry. | Gina Kuffel |
| v5 | Confirmed facts: board **641** (1137 retired); no BA — bug triage is **TPM + co-TPM (Steph Singleton)**; release notes at `crdc-ctdc-starter-kit/releases`; FE repo = `crdc-ctdc-ui` (`main`), Interoperation = `crdc-ctdc-interoperation` (`main`), DMN Core = `Data-Model-Navigator` (`main`); full courtesy-email list confirmed (FNL: Todd Pihl, John Otridge, Mark Jensen · CBIIT: Erika Kim, Tanja Davidsen, Esmeralda Casas-Silva). | Gina Kuffel |
