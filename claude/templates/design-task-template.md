### 7d. 🎨 Design Task Template (Drafted v4)

> **Use this template for every CTDC design task.** The canonical example is **CTDC-2044 (Design: Local Find single-ID autocomplete)**: drafted 2026-05-06 as the first application of this template, parented to epic CTDC-2042 and linked to user story CTDC-2043. (Its sibling CTDC-2045, the multi-ID Input Set modal design, was drafted the same day and follows the same shape, but CTDC-2044 is the single canonical reference.) Future design work should follow the same shape.
>
> **v4 (2026-10-09): slimmed from 7 to 6 sections and moved to Jira wiki authoring, matching ICDC.** The 🧩 Design System & Standards section was removed: WCAG 2.1 AA, Section 508, and the design system are standing site-wide obligations, not per-ticket content, and responsive and browser targets are boilerplate. The standard deliverables dropped from 5 to 3 (accessibility annotations and responsive variants are added when the surface needs them) and the Definition of Done from 7 items to 5. ICDC's design task template (`CBIIT/icdc-documentation → claude/templates/design-task-template.md`, canonical ICDC-4242) has the identical shape; the two are kept in lockstep.
>
> **v3 (2026-09-04): no Jira ticket keys in the body, colons instead of em dashes.** Links holds external reference materials only; the user story, sibling design tasks, and any requirements task are Jira issue links, never cited in the description. Labeled bullets use `* *Label*: content`.

**Why this template**

Design tasks sit in a different orbit than user stories and epics. The user story owns the *what* and the *why*; the epic owns the *strategic envelope*; the design task owns the *how it should look and behave visually*. Conflating these (copying the user story's acceptance criteria into the design task, or leaving the design task as a one-line "make it look good" stub) is the most common antipattern in design ticketing. Both errors cost time: the duplicated AC list rots out of sync the moment the user story changes, and the one-line stub forces designers to context-switch out to other tickets to figure out what's actually being designed.

The template below resolves this with three commitments:

1. **No Acceptance Criteria section.** AC belongs on the user story. The design task has a **Definition of Done** instead: the designer's checklist for marking the task complete, which is a different kind of artifact than QA's pass/fail criteria.
2. **A short Design Summary** so the designer can read the ticket in five seconds and know what they're designing, without opening the parent user story.
3. **Concrete deliverables**, not implicit ones. The designer produces specific, named artifacts (mockups, prototype, redlines) that are checkable on the way to "done."

The emoji set borrows shared anchors from the user story and epic templates (🎯 🔗) so a reader scanning a design task next to its sibling user story sees consistent visual structure. The unique additions are 🎨 (Design Scope), 📐 (Design Deliverables), and 🖼️ (Figma File); ✅ (Definition of Done) reuses the story AC emoji deliberately, to mark "this is the completion bar."

**Section order (6 sections, exactly this sequence)**

Each section header is a Jira wiki `h3.` heading using the emoji + bold title format shown. Author the description in Jira wiki markup (see SKILL.md 7b-shared, "Authoring format"). Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header: same rule as every other CTDC template.

1. `h3. 🎯 *Design Summary*`: Two to three sentences. What's being designed, on which surface, for which user need, and the exemplar if one is used. Example: *"Design the sidebar autocomplete search box for Local Find on the Explore Dashboard. The search box lets researchers type a partial Participant ID and select from autocomplete suggestions, populating the dashboard with a single-Participant cohort."*

2. `h3. 🔗 *Links*`: Bullet list of external reference materials only: implementations in other CRDC data commons (e.g., CCDI, ICDC), the live CTDC surface being changed, design specs, and mockup or image links. **Never list other Jira tickets here** (the user story, a requirements task, sibling design tasks): Jira's native Links panel already carries them. Each bullet is a labeled link (`* *Label*: content`): say what it is, then give the URL. Wrap any URL containing underscores in `{{monospace}}` so it isn't misread as italics. **Never paste a signed or expiring URL** (CloudFront `Expires=` tokens, SharePoint share links with `e=` tokens): describe how to re-create the view instead. If there are none at ticket creation, state "None at this time".

3. `h3. 🎨 *Design Scope*`: Bullet list (`* *Label*: content`) of the surface, the content the design must carry, the interactions, and the **states** the designer is responsible for. State coverage is where design tickets most often come back incomplete, so name it explicitly: populated / empty or no-data / loading / error or unavailable, plus hover / focus / disabled where the surface has controls. Where a state decision is deliberately left to the designer (e.g. "hide the panel vs. show an empty state"), say so.

4. `h3. 📐 *Design Deliverables*`: Numbered list (`#` items) of concrete artifacts the designer will produce. The CTDC floor:
   1. High-fidelity Figma mockup of every surface in scope, shown in context
   2. Figma prototype showing the interactions and state transitions
   3. Redlines for engineering handoff (spacing, typography, color tokens, component variants)

   Add deliverables when the surface needs them (accessibility annotations for a keyboard-heavy control, responsive variants for a layout change); never drop one because the surface "feels small."

5. `h3. 🖼️ *Figma File*`: Required. The designer fills this in when work begins; leave blank at ticket creation. Format:
   - `* *Figma Design*:`
   - `* *Figma Prototype*:`
   - `* *Date Design Started*:`
   - `* *Date Design Completed*:`

   The "Date Design Completed" line is the trigger for moving the ticket to Ready for Review.

6. `h3. ✅ *Definition of Done*`: Designer's completion checklist as `* [ ]` items. **Not** the user story's acceptance criteria: those belong on the story. The CTDC standard set:
   - Figma mockup and prototype published in the shared CTDC Figma workspace
   - Prototype reviewed with the TPM and the feature's stakeholders
   - Redlines documented for engineering handoff
   - Figma URLs and completion date recorded in this ticket
   - Figma URL added to the linked user story

**Standing emoji set (6 entries)**

| Section | Emoji |
|---|---|
| Design Summary | 🎯 |
| Links | 🔗 |
| Design Scope | 🎨 *(unique to design task)* |
| Design Deliverables | 📐 *(unique to design task)* |
| Figma File | 🖼️ *(unique to design task)* |
| Definition of Done | ✅ |

**Required content rules (Design Task specific: universal rules in 7b-shared also apply)**

- **No Acceptance Criteria section.** AC belongs on the parent user story. The design task has Definition of Done instead: these are different artifacts and should not be conflated. If a designer ever needs to know "what does the system have to do," the answer is *open the linked user story*, reachable directly from the ticket's Jira links (the Epic Link field and the Relates link to the story).
- **One design task per user story.** Granularity matches the user story scope. If a single user story has multiple visual surfaces, they all live in one design task. If two user stories share visual surfaces (like the Local Find sidebar), each story gets its own design task and the two are cross-linked via a Relates issue link for visual consistency.
- **Issue type is Task** on this tracker. Confirmed via existing CTDC design tasks (CTDC-2038 Update Design for the Participant Details page; CTDC-2039 Update Design for Explore Dashboard table). Do not use Story or Subtask.
- **Parent Epic field set on the ticket itself**, not just named in the description. Use `customfield_12350` per Section 10. This makes the design task discoverable from the epic's child issues panel.
- **Sibling design tasks cross-linked via "Relates" issue link** when two stories share visual surfaces. The formal Jira issue link makes them navigable from each ticket. See Section 10 for issue link conventions.
- **Figma URL is required before the ticket can move to Ready for Review.** Empty Figma fields after work has visibly progressed are a signal something is wrong: either the design lives elsewhere (a process gap) or the work hasn't actually been done.
- **Designer is the assignee.** Hannah Stogsdill (`stogsdillhh`) owns CTDC design work. Assign on creation; do not leave unassigned for triage unless the user explicitly requests it.
- **No Jira ticket keys in the body.** Not in Links, not in Design Scope, not anywhere. The Epic Link field and `Relates` issue links are the single record of related tickets; repeating keys in the description is redundant and goes stale.
- **Colons, not em dashes.** Labeled bullets are `* *Label*: content`; no em dashes anywhere in the description.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text: same rule as every other Jira description on this tracker.

**Writing-and-publishing workflow**

1. Confirm the user story has been written (and ideally normalized into the 7a User Story Template) before drafting the design task. The design task's deliverables must enable the user story's AC; if the AC isn't stable, the design task scope isn't stable either.
2. Create the design task via `jira_create_issue` with `issue_type = "Task"`, a placeholder description, the parent epic linked via `customfield_12350` in `additional_fields`, and the designer assigned.
3. Push the full description with `jira_update_issue`, authored in Jira wiki markup and passed as `{"description": "..."}` in `additional_fields`. Read it back to confirm it stored as wiki markup.
4. Add a "Relates" issue link between the design task and its parent user story.
5. If a sibling design task exists for a sibling user story, add a "Relates" link between the two design tasks as well.
6. Verify the rendered description with a UI screenshot from the user: wiki source is unreliable as a render preview (per 7b-shared).

**When to expand vs trim**

- **Single-surface design with no parallel siblings** → keep all 6 sections; Links may legitimately be "None at this time" but stays as a header.
- **Cross-feature design that touches many surfaces** → expand Design Scope and Design Deliverables; consider whether the work is large enough to warrant splitting into multiple design tasks (one per major surface) rather than one mega-task.
- **Design QA / Final Review style task** (verifying an already-built feature against design) → this template is overkill. Use a free-form Task with a checklist of the QA points to verify, plus a Figma comparison reference.
- **Pure copy / typography / icon-only change** → this template is overkill. A short Task description with the before/after content and the affected surface is enough.
