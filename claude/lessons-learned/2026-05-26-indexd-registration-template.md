# Lessons Learned — IndexD Registration Task Template Drafting

**Date:** 2026-05-26
**Template produced:** `claude/templates/indexd-registration-task-template.md` (Section 7h)
**Trigger:** New data ingestion conversation. The user identified that an IndexD/file-indexing template was needed before a Data Loading Task could be ticketed for an upcoming Sprint 28 ingestion, and noted that no such template existed yet in the component library.

---

## What we shipped

1. **`claude/templates/indexd-registration-task-template.md`** (32,405 bytes, 9-section recommended structure) — the first template in the data-management lane that formalizes an *upstream artifact creation* work pattern, distinct from the *end-to-end load* pattern owned by `data-loading-task-template.md`.
2. **`claude/README.md`** updated — extended the "Two Functions" decision tree, added the new row to the Data management inventory, added the IndexD branch to the "How Claude Should Use This Folder" decision tree, and appended chronology.
3. **`claude/SKILL.md`** updated — Section 7d (Data Management Templates entry-point) and Section 9a (Template Status Tracker).
4. **This lessons-learned file.**

The IndexD work pattern is now the fourth template in the data-management lane and the only template in either lane that primarily coordinates with an external organization rather than internal CTDC engineering or data-engineering operations.

---

## Lesson 1 — When the user says "use existing reference X," the reference belongs in context before the design conversation begins, not after

The user pointed at CTDC-1907 as the canonical example for IndexD registration work. Reading CTDC-1907 immediately produced the correct understanding of the workflow shape (manifest upload to DCF Google Drive → CRINTAKE intake ticket → DCFS confirmation → GUID spot-check). Without it, the early hypothesis was that IndexD registration would follow a four-environment promotion model parallel to Data Loading — wrong, because IndexD is a single centralized service operated by an external team with no Dev/QA/Stage/Prod separation.

**The methodology lesson:** when a user names a canonical reference ticket, fetch it before the template design discussion, not after. The reference reshapes the template's section model from the first draft. Inferring the workflow from general knowledge (or from sibling templates that look superficially similar) wastes a design pass.

This is the same shape as the "verify the canonical repo before reading code" lesson from CTDC-2042 (SKILL.md Section 9c): the methodology improvement is to confirm the reference *first*, not to use the closest-adjacent reference and hope it transfers.

---

## Lesson 2 — Workflow-shape canonical and relationships-shape canonical can be the same ticket OR different tickets

CTDC-1907 is canonical for the IndexD registration **workflow shape** — the four-step manifest-upload-to-spot-check sequence is exactly right and survived into the template verbatim. But CTDC-1907 was closed 2026-03-11, which is months before the parent-user-story discipline was formalized in the Data Modeling for Study Submission template (2026-05-20). CTDC-1907 does not have a `Relates` link to a parent submission user story.

This produced an awkward design moment: do we copy CTDC-1907's relationships shape (which would silently propagate the missing link), or impose the discipline from the modeling template (which means the canonical example doesn't fully conform to the template)?

The resolution was to **split the canonical-example concept into two kinds**:
- **Workflow-shape canonical** — the ticket whose section content the new template's section model is verified against. CTDC-1907 stays this for IndexD registration.
- **Relationships-shape canonical** — the ticket whose linked-issue pattern the new template's link rules are verified against. CTDC-2051 ↔ CTDC-1666 and CTDC-1799 ↔ CTDC-1804 stay this for the parent-user-story `Relates` pattern; the template borrows their relationships shape and explicitly says so in the rationale.

A one-time retrofit recommendation was added to CTDC-1907 (final section of the template) to bring it into conformance — but the template was published without waiting for the retrofit to happen, because the relationships shape is verified elsewhere.

**Generalizable rule:** if a canonical example predates a discipline that the new template wants to enforce, do not weaken the template to match the example. Split the canonical concept (workflow shape vs. relationships shape), borrow each shape from the most appropriate ticket, and recommend a retrofit. Do not block template publication on the retrofit.

---

## Lesson 3 — The parent-user-story discipline is universal across the data-management lane

SKILL.md Section 7d already articulates this as a universal pattern: *"Every CTDC data submission generates multiple tickets — a user story for the submission as a whole, a modeling ticket for schema additions, eventually a loading ticket, possibly supporting tasks. The parent user story carries study identity ... Downstream tickets link back to the parent user story and do not duplicate study identity in their descriptions."*

The first draft of the IndexD Registration Task template missed this. It included Study (acronym + release) and File count rows in the Submission & Artifacts table — both duplicating content that belongs on the parent user story. The user caught the gap by pointing at CTDC-1805 as the parent for the upcoming Sprint 28 submission.

The revision trimmed the table from 9 rows to 7 (removed Study + File count), added a required Parent Submission User Story field to Section 2 (Linked Work), added the `Relates`-link-is-mandatory rule to the required content rules, and rewrote the Section 1 example to reference CTDC-1805 by key rather than restating its content.

**Generalizable rule:** when drafting any new data-management template, treat the parent-submission-user-story link as a default required field. Look for places in the draft where study identity is being restated (program, study name, dbGaP, submitter, chronology, POC team, SharePoint paths, study description) and either remove them or convert them into a parent-user-story reference. The principle in SKILL.md Section 7d holds across loading, modeling, and any future data-management work patterns.

---

## Lesson 4 — The "what's NOT in this template" footer is as important as the section model

The "When NOT to use this template" section did real work in this drafting session. The IndexD registration template sits in a complicated neighborhood — adjacent to Data Loading (downstream sibling), Data Modeling (different sub-function), Software Development (different lane entirely), other upstream artifact creation work (megazip generation, metadata loading file creation — not templated yet), and CRDC platform changes (out of CTDC scope entirely). Without the footer, the template would either get used for those other work patterns or get blocked by uncertainty about whether it applies.

The footer also produced an explicit acknowledgment that *megazip generation and metadata loading file creation are also upstream artifact work patterns that don't have templates yet*. That's a forward-looking observation worth carrying: the loading-data sub-function may eventually have three or four templates — end-to-end load, IndexD registration, megazip generation, metadata file creation — and the README's decision tree will need to grow with them.

**Generalizable rule:** the "When NOT to use this template" section is not a courtesy. It is load-bearing structure that prevents misuse and surfaces adjacent work patterns that may need their own templates. Write it with the same care as the required-content rules.

---

## Lesson 5 — "Use what you need" vs. "All sections required with placeholders" is a real choice driven by ticket size

The Data Loading Task template enforces strict 10-section discipline with "None at this time" placeholders for empty sections. The first draft of the IndexD Registration Task template mirrored this rule. The user explicitly chose the looser "omit empty sections entirely" rule for IndexD work because registration tickets are short and bounded enough that strict section discipline would add noise.

This is the first data-management template to take the looser approach. The rule is now explicit in the template's required-content rules and is called out as a deliberate difference from Data Loading.

**Generalizable rule:** template strictness is a function of ticket complexity, not template authorship style. The bigger the ticket and the more cross-cutting concerns it carries, the more value comes from forcing every section to be present with placeholders (so reviewers can confirm nothing was missed). The smaller and more bounded the ticket, the more value comes from letting empty sections drop out (so the rendered description is scannable). Pick deliberately when drafting; don't default to the strictness of the previous template just because it's a sibling.

---

## Lesson 6 — External-coordination work patterns deserve their own emoji and section

The standing emoji set for the IndexD Registration Task template shares eight emojis with the Data Loading Task template (🎯 🔗 📦 🚦 🧪 🤝 🔍 📝). The one unique addition is **🌐 External Handoff Coordination** — a new section that names the cross-organizational seam (CTDS, NCI DCFS, DCF Google Drive, CRINTAKE board, the two PMs, and the time-bound-work communication norm).

This section did real work too. Without it, the template would have buried "external team" inside Collaboration & Handoffs, which on every other CTDC template is a list of internal roles (data engineering, DevOps, QA, TPM, tech lead). The external seam is fundamentally different operationally — it depends on a queue managed by a different organization, with different timing dynamics — and surfacing it as its own section gives readers a place to look when the external dependency is what's gating the work.

**Generalizable rule:** when a template's operational reality includes a cross-organizational seam, give it its own section and its own emoji. Do not collapse it into the internal collaboration section.

---

## Implications for future template work

- **Megazip generation** and **metadata loading file creation** are documented in the IndexD template's "When NOT to use this template" footer as upstream artifact creation work patterns *that do not have templates yet*. When a recurring pattern emerges (i.e., when there are 2–3 megazip or metadata loading file tickets to reference), draft templates using the IndexD Registration Task as the closest sibling.
- **The retrofit of CTDC-1907** is recommended one-time work, not a normalization sweep. Other registration tickets that predate this template stay as-is. The retrofit is captured in the template's final section so the next reader knows it's been considered and scoped.
- **The parent-user-story discipline is now four-for-four** across the data-management lane templates (Data Loading already implicitly through SKILL.md Section 7d, Data Modeling for Study Submission v3 explicitly, Data Model Update explicitly, IndexD Registration explicitly). Future data-management templates should treat this as a default. The "Universal pattern for data submissions" note in SKILL.md Section 7d is the canonical place to track this.

---

## Cross-references

- **Template:** `claude/templates/indexd-registration-task-template.md` (Section 7h)
- **Sibling templates:** `claude/templates/data-loading-task-template.md` (Section 7e), `claude/templates/data-modeling-study-submission-template.md` (Section 7g), `claude/templates/data-model-update-template.md` (Section 7f)
- **Canonical workflow example:** CTDC-1907 (TCIA CMB radiology images, 2,632 files, closed 2026-03-11)
- **Canonical relationships example:** CTDC-2051 ↔ CTDC-1666 and CTDC-1799 ↔ CTDC-1804 (Data Modeling for Study Submission pattern)
- **Parent epic for IndexD registration tickets:** CTDC-1664 (CTDC Data Integration)
- **External team:** UChicago CTDS (Center for Translational Data Science) — operators of CRDC IndexD via the open-source `github.com/uc-cdis/indexd` codebase
- **External intake board:** `https://tracker.nci.nih.gov/projects/CRINTAKE/`
- **DCF Google Drive folder for manifest upload:** `https://drive.google.com/drive/u/2/folders/1ZVsv2vFEcTPBT2IYsaOb_XCjpWjjMGTb`
- **Public IndexD resolution endpoint (verification):** `https://nci-crdc.datacommons.io/index/<guid>`
- **Prior lessons-learned file (data modeling templates):** `claude/lessons-learned/2026-05-20-data-modeling-templates.md`
