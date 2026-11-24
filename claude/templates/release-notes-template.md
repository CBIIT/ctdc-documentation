# 📣 Release Notes Template

> **Use this template for the GitHub release notes of every CTDC software release, quarterly or feature-specific (for example the RAS release).** The notes are published as the body of a GitHub release at `https://github.com/CBIIT/crdc-ctdc-starter-kit/releases`. Companion: the Software Release Playbook (`../workflows/software-release-playbook.md`), Phase 8, step 4. Release tickets have their own spec in `release-ticket-templates.md`.
>
> Canonical CTDC example: **CTDC v.1.5.0** (tag `1.5.0.8`, the RAS feature release, October 2026), the last release titled by Frontend version; releases from 2027 Q1 are titled by release name.

**Why this template**

Release notes are the public record of what a release changed for CTDC users and for the other Bento-based data commons. They are read by researchers, federal leadership, and the CRDC communications team, so they describe outcomes in plain language rather than tickets or code.

Every line must be traceable to work that actually shipped. The scope comes from the release's Jira Fix Version (one per release), and each claim is checked against the merged code, not only the ticket title: ticket summaries are often thin, and a claim nobody can trace back is a claim that may be wrong.

---

## Deriving the scope

1. Confirm the Deployment Package on the Release Dashboard. The version of every microservice comes from `versions.yaml` in CTDC's deployment repository at the deployed package's tag; these versions fill the Build Details table.
2. Pull every shipped ticket with one JQL query on the release's Fix Version, excluding test artifacts:

   ```
   project = CTDC AND fixVersion = "CTDC <YYYY> Q<N>" AND issuetype not in ("Test", "Test Set", "Test Execution") ORDER BY key ASC
   ```

3. For each ticket, confirm what shipped against the merged pull requests on the release branch. When a ticket's description is empty or a pull request changed the scope, the code wins.
4. Run the Release Tagging Hygiene Checklist (SKILL.md §13): a microservice whose version changed since the last Production package, but whose repository has no pull request from a ticket in the Fix Version, is either an infrastructure-only bump (fine, no bullet) or a tagging gap to fix before publishing.
5. List open bugs against features in this release; they become Known Limitations.

---

## GitHub release fields

* *Release title*: the release name, the same as its Jira Fix Version: `CTDC <YYYY> Q<N>` for a scheduled release, `CTDC <YYYY> Q<N> Hotfix` for a hotfix release (`CTDC <YYYY> Q<N> Hotfix 2` for a second hotfix in the same quarter).
* *Tag*: a new tag created with the release, from the release name: `release-<yyyy>-q<n>`, `release-<yyyy>-q<n>-hotfix`, or `release-<yyyy>-q<n>-hotfix-2`. Example: `release-2027-q1` for `CTDC 2027 Q1`.
* *Target*: the `main` commit that went to Production (the commit carrying the Frontend's gamma tag), after Engineering has merged and tagged the release candidate (Playbook Phase 8, steps 1 and 2).
* *Why the release name, not a version number*: the release is the whole Deployment Package, not the Frontend. A release that does not change the Frontend has no new Frontend version to use as a title or tag, so naming the release after the Frontend collides with the release before it. The release name matches the Jira Fix Version and the Release Dashboard, and every microservice version stays in Build Details.
* *Earlier releases* keep the titles and tags they were published with.

---

## Body

Copy everything inside the block below into the GitHub release body. Delete any section that has no bullets; never publish an empty section or a placeholder.

```markdown
## Release Notes - [Clinical and Translational Data Commons (CTDC)](https://clinical.datacommons.cancer.gov/#/) ⚕️ 🏥

## Build Details

| Service | Version |
|---|---|
| Frontend (FE) | <version> |
| Backend (BE) | <version> |
| File Service (FS) | <version> |
| Interop | <version> |
| DMN Core | <version> |
| DMN Standalone | <version> |
| Auth | <version> |

---

## ✨ New Features

- **<Feature name>:** <What a user can now do, and where in the CTDC they do it.>

## 🔒 Security

- **<Change>:** <What is now protected or required, in plain language.>

## 🛠️ Improvements

- **<Area>:** <What works better than before, from the user's point of view.>

## 🍱 Contributions to the Bento Framework

- **<Contribution>:** <What was contributed upstream, to which Bento repository and branch, and which data commons can reuse it.>

## 🐛 Bug Fixes

- **<Area>:** <What the user experienced before, and what happens now.>

## ⚠️ Known Limitations

- **<Area>:** <What the user may encounter, and any workaround.>
```

---

## Section rules

| Section | What belongs here | What does not |
|---|---|---|
| ✨ New Features | Capabilities a user did not have before | Refinements to existing features (Improvements) |
| 🔒 Security | Authentication, authorization, logging, and access-control changes | General behavior improvements (Improvements) |
| 🛠️ Improvements | Better behavior of existing features, accessibility fixes | New capabilities, defect fixes |
| 🍱 Contributions to the Bento Framework | Code or designs contributed upstream for reuse by other Bento-based commons | CTDC-only work |
| 🐛 Bug Fixes | Closed bugs, described by user-visible symptom | Bugs found and fixed inside the same release cycle that never reached production |
| ⚠️ Known Limitations | Open bugs in shipped features, with a workaround when one exists | Future plans, roadmap items |

A Bug Fix that resolves an earlier release's Known Limitation says so: "This resolves the known limitation noted in v<x.y.z>."

---

## House style

* `- **Label:** sentence.` bullets: a bold short label, a colon, then one or two plain-language sentences.
* Write for researchers, not engineers: describe what the user sees or can do. No Jira keys, pull request numbers, environment variable names, or internal service names.
* Bold UI labels exactly as they appear on screen (**Login**, **Study Details**, **Request Access**).
* Spell out an acronym on first use, then use the acronym: NIH Researcher Auth Service (RAS), Identity Assurance Level 2 (IAL2).
* Call DCF the "CRDC Data Commons Framework"; it issues pre-signed URLs, it does not "release" files.
* Never call standard browser behavior a bug.
* Colons, not em dashes.

---

## Before publishing

* Every Build Details version matches the Release Dashboard and the `/#/sysinfo` page on Production.
* Every bullet traces to a ticket in the Fix Version query and to merged code.
* No empty sections, no placeholders, no Jira keys.
* Every Build Details version matches `versions.yaml` at the deployed package's tag.
* The TPM has marked the release's Fix Version Released in Jira (Playbook Phase 8, step 4).

---

## Changelog

- **v2.1 (2026-11-24)**: GitHub releases are titled and tagged by release name (`CTDC <YYYY> Q<N>`, tag `release-<yyyy>-q<n>`; hotfix releases add `Hotfix` and `-hotfix`), matching the Jira Fix Version, instead of by Frontend version. Release notes stay on `CBIIT/crdc-ctdc-starter-kit`. ICDC changed in lockstep (v2.1).
- **v2 (2026-11-24)**: Scope is one release-level Fix Version (`CTDC <YYYY> Q<N>` or `CTDC <YYYY> Q<N> Hotfix`) instead of one per microservice; Build Details come from `versions.yaml` in CTDC's deployment repository at the deployed package's tag; the hygiene cross-check compares changed microservice versions with the pull requests of tickets in the Fix Version; the version-merge check is replaced by marking the Fix Version Released. ICDC changed in lockstep (v2).
- **v1 (2026-10-23)**: Initial template, derived from the CTDC v.1.5.0 RAS release notes. Adds the scope-derivation steps (Fix Version JQL excluding test artifacts, code verification, hygiene cross-check), GitHub release fields, body skeleton with six sections, section rules, house style, and a pre-publish check.
