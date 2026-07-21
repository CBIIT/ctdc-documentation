### 7d. 🎨 Design Task Template (Drafted)

> **Use this template for every CTDC design task.** The canonical example is **CTDC-2044 (Design — Local Find single-ID autocomplete)** — drafted 2026-05-06 as the first application of this template, parented to epic CTDC-2042 and linked to user story CTDC-2043. (Its sibling CTDC-2045, the multi-ID Input Set modal design, was drafted the same day and follows the same shape, but CTDC-2044 is the single canonical reference.) Future design work should follow the same shape.

**Why this template**

Design tasks sit in a different orbit than user stories and epics. The user story owns the *what* and the *why*; the epic owns the *strategic envelope*; the design task owns the *how it should look and behave visually*. Conflating these — by copying the user story's acceptance criteria into the design task, or by leaving the design task as a one-line "make it look good" stub — is the most common antipattern in design ticketing. Both errors cost time: the duplicated AC list rots out of sync the moment the user story changes, and the one-line stub forces designers to context-switch out to other tickets to figure out what's actually being designed.

The template below resolves this with three commitments:

1. **No Acceptance Criteria section.** AC belongs on the user story. The design task has a **Definition of Done** instead — the designer's checklist for marking the task complete, which is a different kind of artifact than QA's pass/fail criteria.
2. **A short Design Summary** so the designer can read the ticket in five seconds and know what they're designing, without opening the parent user story.
3. **Concrete deliverables**, not implicit ones. The designer produces specific, named artifacts (mockups, prototypes, redlines, accessibility annotations) that are checkable on the way to "done."

The emoji set borrows shared anchors from the user story and epic templates (🎯 🔗) so a reader scanning a design task next to its sibling user story sees consistent visual structure. The unique additions are 🎨 (Design Scope), 📐 (Design Deliverables), 🧩 (Design System & Standards), and ✅ (Definition of Done — same emoji as story AC, deliberately, to mark "this is the completion bar").

**Section order (7 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header — same rule as every other CTDC template.

1. `### 🎯 **Design Summary**` — Two to three sentences. What's being designed, on which surface, for which user need. Example: *"Design the sidebar autocomplete search box for Local Find on the Explore Dashboard. The search box lets researchers type a partial Participant ID and select from autocomplete suggestions, populating the dashboard with a single-Participant cohort."*

2. `### 🔗 **Links**` — Bullet list of reference materials relevant to the design: implementations in other CRDC data commons (e.g., CCDI, ICDC), external websites, documentation, design references, and mockup or image links. Each bullet is a labeled link — say what it is, then give the URL. Wrap any URL containing underscores in backtick monospace so it isn't misread as italics. If there are none at ticket creation, state "None at this time".

3. `### 🎨 **Design Scope**` — Bullet list of surfaces, screens, components, and states the designer is responsible for. Be specific about state coverage — empty / loading / error / success / disabled / hover / focus / active. State coverage is where design tickets most often come back incomplete, so naming it explicitly here protects the deliverable.

4. `### 📐 **Design Deliverables**` — Numbered list of concrete artifacts the designer will produce. The standard CTDC set:
   1. High-fidelity Figma mockups for every surface in scope
   2. Interactive Figma prototype showing state transitions
   3. Accessibility annotations meeting WCAG 2.1 AA (focus order, ARIA roles, keyboard navigation, screen-reader announcements)
   4. Design redlines / specs for engineering handoff (spacing, typography, color tokens, component variants)
   5. Cross-browser / responsive variants where applicable (desktop primary, tablet supported)

   This list is the floor, not the ceiling. Add deliverables when the surface needs them; never drop one because the surface "feels small" — every CTDC user-facing artifact carries the same accessibility and handoff bar.

5. `### 🧩 **Design System & Standards**` — Bullet list naming the standards the design must conform to:
   - CTDC design system conformance (colors, typography, spacing, button states, modal layout, chip styling)
   - WCAG 2.1 AA accessibility
   - Responsive support: desktop primary, tablet supported (mobile not currently a CTDC target)
   - Cross-browser parity: Chrome, Firefox, Safari, Edge
   - Section 508 compliance (federal accessibility requirement)

6. `### 🖼️ **Figma File**` — Required field. The designer fills this in when work begins. **Format:**
   - **Figma Design:** *(Figma URL)*
   - **Figma Prototype:** *(Figma URL — same file, prototype view)*
   - **Date Design Started:** *(YYYY-MM-DD)*
   - **Date Design Completed:** *(YYYY-MM-DD)*

   At ticket creation time, leave these blank — the designer fills them in. The "Date Completed" field is the trigger for moving the ticket to Ready for Review.

7. `### ✅ **Definition of Done**` — Designer's completion checklist. **Not** the user story's acceptance criteria — those belong on the story. The standard CTDC set:
   - [ ] All Figma mockups published in the shared CTDC Figma workspace
   - [ ] Interactive prototype reviewed and approved by PO
   - [ ] Accessibility annotations complete (focus order, ARIA, keyboard nav, screen reader)
   - [ ] Design redlines / specs documented for engineering handoff
   - [ ] Figma URL added to this ticket (Section 6)
   - [ ] Figma URL added to the linked user story
   - [ ] Date of completion noted in this ticket (Section 6)

**Standing emoji set (7 entries)**

| Section | Emoji |
|---|---|
| Design Summary | 🎯 |
| Links | 🔗 |
| Design Scope | 🎨 *(unique to design task)* |
| Design Deliverables | 📐 *(unique to design task)* |
| Design System & Standards | 🧩 |
| Figma File | 🖼️ *(unique to design task)* |
| Definition of Done | ✅ |

**Required content rules (Design Task specific — universal rules in 7b-shared also apply)**

- **No Acceptance Criteria section.** AC belongs on the parent user story. The design task has Definition of Done instead — these are different artifacts and should not be conflated. If a designer ever needs to know "what does the system have to do," the answer is *open the linked user story*, reachable directly from the ticket's Jira links (the Epic Link field and the Relates link to the story).
- **One design task per user story.** Granularity matches the user story scope. If a single user story has multiple visual surfaces, they all live in one design task. If two user stories share visual surfaces (like the Local Find sidebar), each story gets its own design task and the two are cross-linked via a Relates issue link for visual consistency.
- **Issue type is Task** on this tracker. Confirmed via existing CTDC design tasks (CTDC-2038 Update Design for the Participant Details page; CTDC-2039 Update Design for Explore Dashboard table). Do not use Story or Subtask.
- **Parent Epic field set on the ticket itself**, not just named in the description. Use `customfield_12350` per Section 10. This makes the design task discoverable from the epic's child issues panel.
- **Sibling design tasks cross-linked via "Relates" issue link** when two stories share visual surfaces. The formal Jira issue link makes them navigable from each ticket. See Section 10 for issue link conventions.
- **Figma URL is required before the ticket can move to Ready for Review.** Empty Figma fields after work has visibly progressed are a signal something is wrong — either the design lives elsewhere (a process gap) or the work hasn't actually been done.
- **Designer is the assignee.** Hannah Stogsdill (`stogsdillhh`) owns CTDC design work. Assign on creation; do not leave unassigned for triage unless the user explicitly requests it.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as every other Jira description on this tracker.

**Writing-and-publishing workflow**

1. Confirm the user story has been written (and ideally normalized into the 7a User Story Template) before drafting the design task. The design task's deliverables must enable the user story's AC; if the AC isn't stable, the design task scope isn't stable either.
2. Create the design task via `jira_create_issue` with `issue_type = "Task"`, the canonical Markdown body in the description, the parent epic linked via `customfield_12350` in `additional_fields`, and the designer assigned.
3. Push the description in two steps if it's long: create with a placeholder, then `jira_update_issue` with the full Markdown body. This avoids description-conversion edge cases. (Same pattern as the Features template, 7b-3 step 4.)
4. Add a "Relates" issue link between the design task and its parent user story.
5. If a sibling design task exists for a sibling user story, add a "Relates" link between the two design tasks as well.
6. Verify the rendered description with a UI screenshot from the user — wiki source is unreliable as a render preview (per 7b-shared).
7. Add the design task's Jira key to the parent user story's Notes section (or wherever that story's template captures related links).

**When to expand vs trim**

- **Single-surface design with no parallel siblings** → keep all 7 sections; Links may legitimately be "None at this time" but stays as a header.
- **Cross-feature design that touches many surfaces** → expand Design Scope and Design Deliverables; consider whether the work is large enough to warrant splitting into multiple design tasks (one per major surface) rather than one mega-task.
- **Design QA / Final Review style task** (verifying an already-built feature against design) → this template is overkill. Use a free-form Task with a checklist of the QA points to verify, plus a Figma comparison reference.
- **Pure copy / typography / icon-only change** → this template is overkill. A short Task description with the before/after content and the affected surface is enough.
