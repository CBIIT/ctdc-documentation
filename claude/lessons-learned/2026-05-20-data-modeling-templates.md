# Lessons Learned — 2026-05-20: Data Modeling Template Iteration

> **Context.** Single-day session that produced the canonical 6-section Data Modeling for Study Submission template (v3) and applied it to two ticket pairs: CTDC-1666 ↔ CTDC-2051 (NCI-MATCH Arm Z1D) and CTDC-1804 ↔ CTDC-1799 (CIMAC-CIDC). The template went through three iterations — v1 (10 sections, drafted off pre-cleanup CTDC-1799), v2 (10 sections, bloated, not approved), v3 (6 sections, canonical, approved). The journey produced enough methodology lessons to warrant their own record.

---

## Lesson 1: The canonical example is upstream of the template, not the other way around

**What happened.** Earlier in the session, the TPM and I trimmed CTDC-2051 together to a deliberate 6-section shape (Modeling Summary, CDE Request Workbook, Branch & Release Signoff, Verification Surfaces, Open Questions & Risks, Notes). The TPM signed off on that shape with "Do it" — explicit approval. Later, when I drafted the template file in GitHub, I expanded it to 10 sections by importing structure from a sibling template (the Data Model Update Task / Section 7f), assuming "the more comprehensive template is better." Then when CTDC-1799 came along and needed retrofitting, I built it against my own 10-section draft rather than against the approved CTDC-2051 shape. The TPM caught the divergence — "why doesn't 1799 look just like 2051?" — and I bloated CTDC-2051 *back up* to match my template rather than trusting the approved shape.

**The error.** I treated my own working draft as authoritative rather than the artifact the user had approved. The approved canonical example is the template; the template *file* exists to document the example, not the other way around.

**The rule going forward.**

- When a user approves a specific ticket shape (sections, fields, structure), that ticket is the template. Future template files must match it. Future sibling tickets must match it.
- A separately-drafted template file that contains *more* sections than the approved example is wrong by definition, even if it looks more complete or more "professional."
- Build templates from the approved example outward (extracting structure from the real, approved case), not from theoretical "what should a complete template have" inward.
- When the user says "looks perfect" or "do it" or otherwise approves a shape, treat that as a contractual definition — drift from it is correctable only by explicit re-approval.

---

## Lesson 2: Don't import sections from sibling templates without an approval signal

**What happened.** When drafting v2 of the Data Modeling for Study Submission template, I imported four sections from the Section 7f (Data Model Update Task) template: Linked Work, Term Status Summary, Modeling & Promotion Workflow, and Collaboration & External Dependencies. My reasoning was "the heavyweight 7f template has these, so the lightweight 7g should too — for consistency." None of those sections were in the approved CTDC-2051 shape. All four duplicated content that lives elsewhere:

| Section I imported | What it duplicated |
|---|---|
| Linked Work | Jira's native issue-link UI (already shows parent/child relationships) |
| Term Status Summary | The CDE Request Workbook (the explicit source of truth) |
| Modeling & Promotion Workflow | `SOP_CTDC_Data_Model_Contribution.md` (already linked from the Modeling Summary) |
| Collaboration & External Dependencies | The Jira assignee field + Open Questions & Risks section |

**The error.** Sibling templates have their own approved shapes for their own reasons. The Data Model Update Task template (7f) has those sections because *infrastructure-level* model changes genuinely need them — multi-repo coordination, breaking-change tracking, loader code dependencies. Study-driven modeling tickets do not. Cross-pollinating sections without checking whether the new template's *use case* needs them imports complexity that doesn't earn its keep.

**The rule going forward.**

- When drafting a new template by analogy to an existing one, the analogy stops at the meta-structure (numbered sections, emoji header style, Markdown conventions). The actual section list must be derived from the new template's approved canonical example.
- If a section in a sibling template would be useful in the new template, that's a hypothesis to test with the user, not an assumption to ship.
- The question to ask before adding any section is: "Does this section hold content that *cannot* live elsewhere (workbook, SOP, Jira native UI, parent user story)?" If the answer is "no, it duplicates," the section does not belong.

---

## Lesson 3: "Source of truth" is a structural rule, not a paragraph in the description

**What happened.** v2 of the template included a paragraph in the CDE Request Workbook section that said:

> *"The CDE Request Workbook is the **system of record** for this modeling effort. Per-term decisions, term status, and caDSR/SI coordination live in the workbook — not in this ticket."*

…and then, immediately after, included a **Term Status Summary section with a counts table.** The framing said one thing; the structure said another. The two were in direct contradiction.

**The error.** I treated "source of truth" as a label to apply to the workbook in prose, when actually it's a structural commitment about what the ticket does and does not contain. A ticket that *says* the workbook is the source of truth but *contains* a counts table is doing exactly what the rule prohibits — maintaining the same state in two places, with the ticket version guaranteed to go stale.

**The rule going forward.**

- "Source of truth" is a structural rule. When a ticket designates another artifact as the source of truth for some kind of content, the ticket must not contain that content. The presence of the source-of-truth label is not sufficient; the absence of the duplicated content is what enforces it.
- A useful heuristic: if removing the source-of-truth artifact would invalidate the ticket's content, the ticket is duplicating. If removing the source-of-truth artifact would leave the ticket fully informative within its scope, the ticket is correctly delegating.
- Apply this rule wherever a ticket points to an external artifact as authoritative: workbooks, SOPs, parent user stories, contribution READMEs, data dictionaries, design files.

---

## Lesson 4: Read the current state of the canonical example before retrofitting siblings

**What happened.** When CTDC-1799 came up for retrofit to match CTDC-2051, I went straight to my own freshly-drafted v2 template file rather than pulling CTDC-2051 fresh from Jira. CTDC-2051 was already in its approved 6-section shape; my template file had a different (bloated) shape; CTDC-1799 ended up matching my template, not the approved canonical example. The drift was invisible to me until the TPM compared them side-by-side and noticed they didn't look alike.

**The error.** I trusted my session memory and my own drafts over the ground truth of the approved ticket. Once an approved canonical example exists in Jira, that ticket — not my draft, not my memory, not my template file — is the reference. The template file is a reflection of the canonical example, not a parallel authority.

**The rule going forward.**

- When retrofitting a sibling ticket to a template, the workflow is: (1) pull the approved canonical ticket fresh from Jira via `jira_get_issue`, (2) read its current section structure, (3) apply that structure to the sibling. Skip the template file entirely on the first pass — it's a description of the canonical example, not the canonical example itself.
- Use the template file only as a *cross-check* after the sibling matches the canonical example: "does the sibling match the canonical? does the template file match both?" If all three agree, you're done. If the template file disagrees with the canonical, the template file is wrong and needs correcting.
- This is especially important when same-day iteration is happening — the template file and the canonical ticket can diverge within hours, and the canonical wins.

---

## Lesson 5: The parent-user-story pattern is a CTDC-wide convention worth formalizing

**What happened.** Today established the pattern across two pairs of tickets: CTDC-1666 (user story) ↔ CTDC-2051 (modeling task), and CTDC-1804 (user story) ↔ CTDC-1799 (modeling task). The pattern: the user story carries study identity (program, study name, dbGaP ID, submitter, chronology, document references, study description); the modeling task carries only the modeling concern and links back to the user story for identity.

**Why this matters beyond Data Modeling.** Every CTDC data submission generates multiple tickets — a user story for the submission as a whole, a modeling ticket for schema additions, eventually a loading ticket for the actual data load, possibly a documentation-review task, possibly a BlindID-options task (NCI-MATCH specifically), etc. If each of those tickets duplicates study identity, the moment one ticket gets edited and the other doesn't, the team has drift. The user-story-as-identity-anchor pattern eliminates the duplication.

**The rule going forward.**

- For every CTDC data submission, file a parent **user story** that owns study identity. The user story is the context anchor for every downstream ticket related to that submission.
- All downstream tickets (modeling, loading, documentation review, anonymization options, mapping work, etc.) link back to the parent user story via `customfield_12350` (Epic Link) or as a "Relates" issue link, and **do not duplicate** study identity in their descriptions.
- When study identity changes (a new POC, an updated dbGaP ID, a corrected publication link), the change happens once on the user story. Downstream tickets stay current automatically because they don't carry the duplicated content.
- This pattern is now formalized for Data Modeling tickets (Section 7g of templates). It should be considered for Data Loading tickets (Section 7e) the next time one is drafted; the same parent-user-story-carries-identity discipline applies.

---

## Lesson 6: Surgical-restore is a valid recovery move

**What happened.** When the TPM said "the official template should be based on what we had previously in 2051," I had three artifacts in inconsistent states: CTDC-2051 (bloated to 10 sections by my last edit), CTDC-1799 (built to 10 sections from the start), and the template file in GitHub (also 10 sections). The recovery was direct: rewrite CTDC-2051 to the original 6-section shape, rewrite CTDC-1799 to match, then rewrite the template file to match both. Three artifacts, one shape, in three sequential pushes. No need for clever undo logic or "what if we kept some of the new content" negotiation.

**The rule going forward.**

- When a template or pattern drifts from its approved shape and the user signals "go back to what we had," the right recovery is a clean restore of all affected artifacts to that approved shape — not a hybrid attempt to keep "the good parts" of the drift.
- The trust signal the user is offering is: "I know what the approved shape is, please match it exactly." Honor that. Don't relitigate.
- Operational content that was added during the drift (e.g., real PR numbers, real version targets, real risk items) can be preserved by relocating it into the sections that remain (Notes, Risks, Branch & Release Signoff). The shape resets; the content gets re-homed.

---

## Lesson 7: One Jira description rewrite per ticket per session, ideally

**What happened.** CTDC-1799 went through four full description rewrites in a single afternoon — initial v2 application, real-data refresh from comments, restore to v3 shape with real data, final cleanup. Each rewrite was justified in isolation but the cumulative effect was a ticket whose history shows churn rather than progress. A reader looking at the history later would not be able to tell what the "real" v3 shape is without scrolling past the noise.

**The rule going forward.**

- When iterating on a ticket's description, batch the changes before pushing. The "draft locally, verify with the user, push once" pattern produces cleaner history than "push, review, push, review, push."
- If a rewrite is needed mid-session because the underlying template changed, do the rewrite once with full awareness of where the template will land — not iteratively as the template evolves.
- The Jira UI does not show description diffs natively, so multiple pushes create archaeology that's hard to navigate later. One push per session per ticket is the target; two is acceptable if the second corrects a clear error; more than two is a smell.

---

## Where these lessons apply

These seven lessons are mostly about **template authorship discipline** — they apply whenever a new ticket template is being drafted, or an existing template is being iterated. Lessons 4 and 5 also apply to **retrofitting existing tickets to a new template shape** (a workflow that comes up whenever a template evolves). Lessons 1, 2, and 3 are general **methodology rules** that apply beyond templates — any time documentation, SOPs, or stakeholder artifacts get drafted with reference to a canonical example.

The most universal of the seven, worth keeping in mind on every CTDC drafting task:

> **The canonical example is upstream of the template. When in doubt, pull the approved example fresh from its source and match it exactly.**

---

## Addendum — 2026-07-20: canonical examples drove a template re-sync (Lessons 1 & 4 in action)

**What happened.** The TPM edited **CTDC-2051** (the 7f canonical example) directly in Jira — renaming the Section 3 review artifact from "ServiceNow Ticket" (filed by the DM Fed Lead) to the **caDSR II Help Desk Request Form** (filed by the Data Concierge, with the DM Federal Lead added as a *watcher* on the caDSR ticket), and expanding **Steps to Completion** from the lean 4-step spine to the fuller workflow the team actually follows (caDSR filing detail with a 1:1 Short-Description-to-Jira-title rule, SI-team curation of the workbook, PR links in comments, move to Ready for QA, and don't-close-until-the-submission-is-complete-in-the-Submission-Portal). Pulling the sibling canonical **CTDC-2068** fresh (per Lesson 4) confirmed the *same* convention had already landed there for internally-driven work, with context-appropriate conditional watcher logic. Both template files (7f v8, 7j v13) were stale against their own canonical examples.

**What we did.** Re-synced the template files to the tickets, not the other way around: **7f → v9**, **7j → v14** (lockstep), plus the two template-table rows in `SKILL.md` and this note. The "caDSR Help Desk" and "ServiceNow" names refer to the same underlying system — caDSR's Help Desk runs on ServiceNow — so the change is a rename-to-how-the-team-refers-to-it plus a genuine ownership shift (Data Concierge files it, not the DM Fed Lead).

**Reinforced lessons.**

- **Lesson 1 held.** The canonical ticket is upstream; when 2051/2068 drifted ahead of the template files, the *files* were the ones that were wrong. We matched the files to the tickets.
- **Lesson 4 held.** Before touching the sibling template (7j), we pulled its own canonical example (CTDC-2068) fresh rather than assuming 2051's exact edits applied — and it paid off, because 7j's watcher logic is conditional (Fed Lead on the Jira issue when no CDEs/PVs change, on the caDSR ticket when they do) in a way 7f's is not.
- **A note on the "spine, not a procedure" rule.** The expanded Steps to Completion pushes closer to procedure than the earlier lean spine, but it stays within the "no specifics / no term enumeration" rule — the steps describe the *workflow*, never the *terms*, which still live only in the CDE Request Workbook (Lesson 3 intact).
- **Still pending:** CTDC-1799 ↔ CTDC-1804 remains the un-retrofitted 7f sibling pair; it should be brought to the v9 shape the next time it's touched.
