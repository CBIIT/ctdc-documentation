# 📄 Epic Summary Documents — CTDC

This folder contains **leadership and stakeholder summary documents** for significant CTDC epics. These documents are produced when a Jira epic is too technical or too granular to share directly with NCI leadership, program officers, or external stakeholders.

---

## 📋 Epic Summaries Index

| Epic Key | Epic Name | Date Added |
|---|---|---|
| [CTDC-1764](CTDC-1764-ras-object-file-download/) | Bento Core: RAS-Enabled Object File Download | April 6, 2026 |

---

## 📐 The Two-Artifact Pattern

Every epic summary consists of **two complementary files** that serve different audiences:

| Artifact | Format | Location | Audience |
|---|---|---|---|
| `CTDC-XXXX-summary.md` | Markdown | This GitHub repo (alongside the `.docx`) | Engineering team, technical reviewers, version-controlled source of truth |
| `CTDC-XXXX-summary-vX.Y.docx` | Word Document | SharePoint → **CTDC Epic Summaries** folder | NCI leadership, program officers, external stakeholders |

**The `.md` is the canonical source.** The `.docx` is distilled from it. When content needs to change, update the `.md` first, then generate a new versioned `.docx`.

> This mirrors the same pattern used for architecture documents in `claude/architecture/` — the `.md` lives in GitHub for the engineering team, and a polished `.docx` is produced for leadership.

---

## 📁 Folder Structure

```
epic-summaries/
├── README.md                          ← This file
├── _TEMPLATE.md                       ← Reusable Markdown template for new epic summaries
└── CTDC-XXXX-short-name/
    ├── CTDC-XXXX-summary.md           ← Canonical source (.md) — lives in GitHub
    ├── CTDC-XXXX-summary-v1.0.docx   ← First published stakeholder version
    ├── CTDC-XXXX-summary-v1.1.docx   ← Minor revision (content updates, no scope change)
    └── CTDC-XXXX-summary-v2.0.docx   ← Major revision (scope change or significant re-plan)
```

---

## 📐 Naming Conventions

### Folder names
```
{JIRA-KEY}-{short-kebab-case-name}
```
Examples:
- `CTDC-1764-ras-object-file-download`
- `CTDC-2008-security-remediation`
- `CTDC-2007-software-release-deployments`

### File names
```
{JIRA-KEY}-summary.md           ← The .md source — no version suffix (always the current source)
{JIRA-KEY}-summary-v{MAJOR}.{MINOR}.docx   ← Versioned stakeholder .docx
```
Examples:
- `CTDC-1764-summary.md`
- `CTDC-1764-summary-v1.0.docx`  ← Initial publication
- `CTDC-1764-summary-v1.1.docx`  ← Minor update (ticket status changes, assignee updates)
- `CTDC-1764-summary-v2.0.docx`  ← Major revision (scope change, re-planning)

---

## 🔢 Versioning Rules

### The `.md` file
The `.md` has **no version suffix** — it is always the current source of truth. Changes are tracked via git commit history.

### The `.docx` file
| Version Bump | When to Use |
|---|---|
| `v1.0` | First published stakeholder version of a new epic summary |
| `vX.Y → vX.(Y+1)` | Minor update: ticket status changes, minor content corrections, assignee updates |
| `vX.Y → v(X+1).0` | Major revision: scope change, significant re-planning, new phases added |

> **Never overwrite an existing `.docx` file.** Always create a new versioned file and commit with a descriptive message.

### When to generate a new `.docx`
Not every `.md` commit requires a new `.docx`. Generate a new `.docx` when:
- A milestone is reached and leadership needs an update
- Scope, risks, or key facts materially change
- A sprint closes and the summary needs to reflect final status

Routine ticket status updates in the `.md` do not require a new `.docx`.

---

## 🎨 Brand & Formatting Standards

All documents in this folder follow these standards:

| Property | Value |
|---|---|
| **Page size** | US Letter (8.5" × 11") |
| **Font** | Arial throughout |
| **NIH Blue** | `#20558A` — used for section headers and cover accent |
| **NIH Red** | `#BE0000` — used for risk/blocker callouts and status badges |
| **Light Blue / White** | `#EAF1F8` / `#FFFFFF` — alternating table rows |
| **Prepared by** | Frederick National Laboratory for Cancer Research (FNL) |
| **Logo/Affiliation** | FNL as contractor; NCI/CBIIT as federal sponsor — do NOT list preparer as NCI or CBIIT |

---

## 🤖 How Claude Generates These Docs

1. Pull the epic and all child tickets from Jira using `jira_search` with JQL
2. Populate the `_TEMPLATE.md` content fields and commit to GitHub as `CTDC-XXXX-summary.md`
3. Generate a `.docx` using the `docx` npm package (US Letter, Arial, NCI/FNL branding)
4. Validate the `.docx`
5. Commit `.docx` to the appropriate `CTDC-XXXX-short-name/` subfolder
6. Upload `.docx` to SharePoint → **CTDC Epic Summaries** folder
7. Update this README's index table and Registered Epics table

---

## 📋 Registered Epics

| Epic Key | Name | Folder | `.md` Source | Latest `.docx` |
|---|---|---|---|---|
| CTDC-1764 | Bento Core: RAS-Enabled Object File Download | `CTDC-1764-ras-object-file-download/` | `CTDC-1764-summary.md` | v1.0 (April 6, 2026) |
