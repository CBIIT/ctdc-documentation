# 📣 Release Notes Template

> **Use this template for the GitHub release notes of every CTDC software release, quarterly or feature-specific (for example the RAS release).** The notes are published as the body of a GitHub release at `https://github.com/CBIIT/crdc-ctdc-starter-kit/releases`. Companion: the Software Release Playbook (`../workflows/software-release-playbook.md`), Phase 8, step 4. Release tickets have their own spec in `release-ticket-templates.md`.
>
> Canonical CTDC example: **CTDC v.1.5.0** (tag `1.5.0.8`, the RAS feature release, October 2026).

**Why this template**

Release notes are the public record of what a release changed for CTDC users and for the other Bento-based data commons. They are read by researchers, federal leadership, and the CRDC communications team, so they describe outcomes in plain language rather than tickets or code.

Every line must be traceable to work that actually shipped. The scope comes from the release's Jira Fix Versions, and each claim is checked against the merged code, not only the ticket title: ticket summaries are often thin, and a claim nobody can trace back is a claim that may be wrong.

---

## Deriving the scope

1. Confirm the Deployment Package on the Release Dashboard: the version of every microservice in the release. These versions fill the Build Details table.
2. Pull every shipped ticket with one JQL query across the release's Fix Versions, excluding test artifacts:

   ```
   project = CTDC AND fixVersion in ("CTDC FE <x.y.z.b>", "CTDC Auth Service <x.y.z.b>", "CTDC File Service <x.y.z.b>", ...) AND issuetype not in ("Test", "Test Set", "Test Execution") ORDER BY key ASC
   ```

3. For each ticket, confirm what shipped against the merged pull requests on the release branch. When a ticket's description is empty or a pull request changed the scope, the code wins.
4. Run the Release Tagging Hygiene Checklist (SKILL.md §13): a microservice version in Build Details with no tagged tickets is either an infrastructure-only bump (fine, no bullet) or a tagging gap to fix before publishing.
5. List open bugs against features in this release; they become Known Limitations.

---

## GitHub release fields

* *Release title*: `CTDC v.<major>.<minor>.<patch>` (for example `CTDC v.1.5.0`).
* *Tag*: the Frontend gamma version, `<major>.<minor>.<patch>.<build>` (for example `1.5.0.8`).
* *Target*: `main`, after Engineering has merged and tagged the release candidate (Playbook Phase 8, steps 1 and 2).

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
* The TPM has merged the internal-release Fix Versions in Jira into the final release version (Playbook Phase 8, step 4).

---

## Changelog

- **v1 (2026-10-23)**: Initial template, derived from the CTDC v.1.5.0 RAS release notes. Adds the scope-derivation steps (Fix Version JQL excluding test artifacts, code verification, hygiene cross-check), GitHub release fields, body skeleton with six sections, section rules, house style, and a pre-publish check.
