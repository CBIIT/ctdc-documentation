# 📚 CTDC Templates Library

This folder is the **component library** for CTDC ticket templates. Each template is a discrete `.md` file that can be linked from individual tickets, evolved independently, and read directly without paging through the much larger SKILL.md.

The SKILL.md remains the operational knowledge base (SOPs, JQL recipes, deck standards, session recovery, etc.). Templates that have outgrown an inline section in SKILL.md, or that are large enough to deserve their own home, live here.

---

## 🎫 Template Inventory

| Template | File | Status | Canonical Examples |
|---|---|---|---|
| **Design Task** | [`design-task-template.md`](./design-task-template.md) | ✅ Drafted v1 (2026-05-06) | CTDC-2044, CTDC-2045 |

---

## 📖 Templates Still Inline in SKILL.md (Not Yet Migrated)

These templates still live as sections inside `claude/SKILL.md`. They will be migrated here when they next get materially expanded, or in a focused migration pass.

| Template | SKILL.md Section | Status | Canonical Examples |
|---|---|---|---|
| **User Story** | Section 7a | ✅ Drafted v1 (2026-05-06) | CTDC-1691 |
| **Application Pages Epic** | Section 7b-1 | ✅ Drafted v1 | CTDC-2025 (gold standard) |
| **Microservices Epic** | Section 7b-2 | 🚧 Stub — TBD | TBD |
| **Features Epic** | Section 7b-3 | ✅ Drafted v1 (2026-05-06) | CTDC-2042 |
| **Products Epic** | Section 7b-4 | 🚧 Stub — TBD | TBD |
| **Infrastructure Epic** | Section 7b-5 | 🚧 Stub — TBD | TBD |
| **Security Epic** | Section 7b-6 | 🚧 Stub — TBD | TBD |
| **Data Epic** | Section 7b-7 | 🚧 Stub — TBD | TBD |
| **Bug Format** | Section 7c | ✅ Lightweight format (always was small) | n/a |

---

## 🧱 Why a Component Library?

Three reasons we're moving toward this pattern:

1. **Templates are linkable from tickets.** A Jira ticket can link directly to `claude/templates/design-task-template.md` so any reader (Claude, an engineer, the designer, a reviewer) lands on the canonical template without paging through the 114k-char SKILL.md.

2. **Templates evolve independently.** When the Design Task template gets a new section or a new lessons-learned note, the change touches a focused file — not a sprawling monolith. PRs are easier to review.

3. **The SKILL.md stays leaner.** SKILL.md is still the right home for SOPs, JQL recipes, deck standards, and session recovery — operational knowledge that's used as a working reference. Templates are reference artifacts that get *applied* when creating tickets; they belong in their own catalog.

---

## ➕ Adding a New Template Here

When migrating an inline SKILL.md template to this folder, or drafting a new template from scratch:

1. Create the template file in this folder with the file naming convention `<artifact-type>-template.md` (e.g., `bug-template.md`, `epic-features-template.md`).
2. Inside the template file, follow the standard structure: header with usage statement, "Why this template" rationale, section order, standing emoji set, required content rules, writing-and-publishing workflow, when-to-expand-vs-trim guidance.
3. Add a row to the Template Inventory table above.
4. If migrating from SKILL.md, leave a small cross-reference in SKILL.md pointing here (don't delete the section header — replace its body with a pointer).
5. Update `SKILL.md` Section 9a (Epic Template Status Tracker) with the new entry.
6. Add a lessons-learned subsection in `SKILL.md` Section 9 if the template's drafting produced any reusable methodology insights.

---

## 🧭 How Claude Should Use This Folder

When the user asks Claude to draft a ticket of any kind:

1. **First, check if a template exists for that ticket type** — look in this folder and in `SKILL.md` Section 7.
2. **If yes, follow the template exactly** — section order, emoji set, content rules, all of it.
3. **If no template exists yet**, fall back to the closest adjacent template and note the gap to the user so it can be drafted.
4. **Always link the canonical template file** in the ticket's Notes section if relevant, so the next person picking up similar work knows where the convention is documented.

---

*This folder was created 2026-05-06 alongside the Design Task template. The first migration target is the User Story template (Section 7a), which has grown large enough to warrant its own file.*
