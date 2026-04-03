# 📄 Epic Summary Documents — CTDC

This folder contains **leadership and stakeholder summary documents** for significant CTDC epics. These documents are produced when a Jira epic is too technical or too granular to share directly with NCI leadership, program officers, or external stakeholders.

---

## 📁 Folder Structure

```
epic-summaries/
├── README.md                        ← This file
├── _TEMPLATE.md                     ← Reusable Markdown template for new docs
└── CTDC-XXXX-short-name/
    ├── CTDC-XXXX-summary-v1.0.docx  ← First published version
    ├── CTDC-XXXX-summary-v1.1.docx  ← Minor revision (content updates, no scope change)
    └── CTDC-XXXX-summary-v2.0.docx  ← Major revision (scope change or significant re-plan)
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
{JIRA-KEY}-summary-v{MAJOR}.{MINOR}.docx
```
Examples:
- `CTDC-1764-summary-v1.0.docx`  ← Initial publication
- `CTDC-1764-summary-v1.1.docx`  ← Minor update (ticket status changes, assignee updates)
- `CTDC-1764-summary-v2.0.docx`  ← Major revision (scope change, re-planning)

---

## 🔢 Versioning Rules

| Version Bump | When to Use |
|---|---|
| `v1.0` | First published version of a new epic summary |
| `vX.Y → vX.(Y+1)` | Minor update: ticket status changes, minor content corrections, assignee updates |
| `vX.Y → v(X+1).0` | Major revision: scope change, significant re-planning, new phases added |

> **Never overwrite an existing `.docx` file.** Always create a new versioned file and commit with a descriptive message.

---

## 🎨 Brand & Formatting Standards

All documents in this folder follow these standards:

| Property | Value |
|---|---|
| **Page size** | US Letter (8.5" × 11") |
| **Font** | Arial throughout |
| **NIH Blue** | `#20558A` — used for section headers and cover accent |
| **NIH Red** | `#BE0000` — used for risk/blocker callouts and status badges |
| **White / Light** | `#FFFFFF` / `#EAF1F8` — used for table header text and row alternation |
| **Prepared by** | Frederick National Laboratory for Cancer Research (FNL) |
| **Logo/Affiliation** | FNL as contractor; NCI/CBIIT as federal sponsor — do NOT list preparer as NCI or CBIIT |

---

## 🤖 How Claude Generates These Docs

1. Pull the epic and all child tickets from Jira using `jira_search` with JQL
2. Populate the `_TEMPLATE.md` content fields
3. Generate a `.docx` using the `docx` npm package (US Letter, Arial, NCI/FNL branding)
4. Commit the file to the appropriate `CTDC-XXXX-short-name/` subfolder
5. Update this README if a new epic folder is created

---

## 📋 Registered Epics

| Epic Key | Name | Folder | Latest Version |
|---|---|---|---|
| CTDC-1764 | Bento Core: RAS-Enabled Object File Download | `CTDC-1764-ras-object-file-download/` | v1.0 (April 2, 2026) |
