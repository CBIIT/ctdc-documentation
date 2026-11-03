<!-- Extracted from claude/SKILL.md on 2026-11-02 so CTDC packages its templates the same way ICDC does. This is the canonical home for this content. -->

## 7a. 📖 User Story Template: CTDC (Drafted v4.2)

> **Use this template for every CTDC user story.** The canonical example is **CTDC-1691 (End User can find specific Participant IDs using an Input Set)**: drafted 2026-05-06 as a child of CTDC-2042 (Local Find: Participant). The template is deliberately scoped at story level, not epic level. It inherits the 7b-shared universal conventions (Jira wiki authoring, curly-brace escaping, render verification by UI screenshot), so most of the gotchas are covered there.
>
> **v4.2 (2026-11-02): three sections; Testing Requirements removed, matching ICDC.** QA designs and owns all testing from the Acceptance Criteria, so the story does not prescribe how the work is tested; unit test coverage is a team-wide engineering standard, not restated per story.
>
> **v4.1 (2026-11-02): moved out of SKILL.md into this file; issue-type and Assignee rules added to match ICDC; Jira calls are REST API v2 requests through Claude in Chrome (the Atlassian MCP was retired).**
>
> **v4 (2026-10-13): four sections; Out of Scope replaces Scope, one functional Acceptance Criteria list, and unit tests only, matching ICDC.** The 🎯 Story Summary section is removed: it restated the User Story in other words. The As-a / I-want / So-that statement is the story's card, the Acceptance Criteria are its confirmation, and the Jira Summary field (the ticket title on boards and in search) is the one-line read. The "name the surface" rule moves to the User Story. In Scope only restated the Acceptance Criteria, so it is dropped; Out of Scope sits directly after the Acceptance Criteria so each exclusion is next to the behaviors it limits and QA reads it before writing test cases. Out of Scope stays because Acceptance Criteria can only say what must happen, not what is deliberately excluded; without it, engineers build adjacent features, QA fails stories for behavior nobody asked for, and settled questions get re-raised. The Performance & Quality sub-block is removed: the quality bar is a team-wide standard QA checks, not restated per ticket. Testing Requirements is unit tests only. Integration and manual test scenarios belong to QA, who write their own test cases from the Acceptance Criteria; the story no longer prescribes them.
>
> **v3 (2026-10-09): Jira wiki authoring and a 3-item quality bar, matching ICDC.** Section headers are `h3.` wiki headings; the Performance & Quality bar drops the cross-browser parity item (browser targets are boilerplate) and folds Section 508 into the accessibility item, so it matches ICDC's bar exactly.
>
> **v2 (2026-09-04): slimmed from 7 to 5 sections, no Jira keys in the body, no em dashes.** The 🔗 Parent Epic & Context section was removed (the Epic Link field `customfield_12350` is the canonical link; restating it in the body is noise) and the 📝 Notes section was removed (predecessor tickets, design tasks, Figma links, and decisions are carried by native Jira links, remote links, and comments: the same reasoning that slimmed the Design Task template to v2 on 2026-07-07). ICDC adopted the identical 5-section shape the same day (`CBIIT/icdc-documentation → claude/templates/user-story-template.md`, canonical ICDC-4244) so the two projects stay aligned. CTDC-1691 still carries the v1 7-section body; retrofit is optional: new stories use v2.

**Why this template**

User stories are smaller-scope deliverables than epics: they ship and close within a sprint or two, where an epic scopes a whole phase of work. The bare-bones As-a/I-want/So-that format is a starting point, but it leaves the engineer without enough structure to know when a story is *done*. This template gives every story:

- The classic As-a/I-want/So-that framing intact, written as flowing prose
- An Out of Scope list that prevents adjacent-feature creep
- Testable Acceptance Criteria, one observable behavior each

The ✅ emoji is shared with the epic templates so a reader scanning a story and its parent epic side-by-side sees the same anchor. The two unique ones are 👤 (User Story) and 🚫 (Out of Scope).

**Section order (3 sections, exactly this sequence)**

Each section header is a Jira wiki `h3.` heading using the emoji + bold title format shown. Author in Jira wiki markup (7b-shared, "Authoring format"). Don't omit, reorder, or merge sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header: same rule as the epic templates.

1. `h3. 👤 *User Story*`: The classic As-a / I-want / So-that, written as flowing prose. One paragraph, not three bullet lines. Name the surface the story touches (the page or component) in the I-want clause. Example: *"As an end user with a known list of Participant IDs relevant to my research, I want to paste or upload that list directly into CTDC so that I can seed a cohort without having to rebuild it from scratch using filters."*

2. `h3. ✅ *Acceptance Criteria*`: Numbered list. Each item is verifiable by QA on a deployed environment. Use plain English with `*bold*` on key UI labels and component names. Escape every curly brace as `\{...\}` if path parameters or variable names appear. No Performance & Quality sub-block: The quality bar (Section 508 and WCAG 2.1 AA accessibility, CTDC design system conformance, page performance; contract stability, observability, secrets handling, and cross-environment parity for backend work) is a team-wide standard that QA checks on every story and release. It is not written into user stories, tasks, or epics (TPM decision, 2026-10-13).

3. `h3. 🚫 *Out of Scope*`: Bullet list of work this story deliberately does not do: adjacent features, behaviors someone might reasonably expect, and questions already settled. Describe each excluded item in words; do not cite the Jira key of the ticket that covers it (add a `Relates` link instead). If there are none, state "None at this time." There is no In Scope list: the Acceptance Criteria are the definition of what the story delivers, and an In Scope list only restates them less precisely.

**Standing emoji set (3 entries)**

| Section | Emoji |
|---|---|
| User Story | 👤 *(unique to story scale)* |
| Acceptance Criteria | ✅ |
| Out of Scope | 🚫 *(unique to story scale)* |

**Required content rules (Story specific: universal rules in 7b-shared also apply)**

- **Issue type is `User Story`.** Not `Story`, not `Task`. Create with `"issuetype": {"name": "User Story"}`, then push the body in a second call. Matches ICDC.
- **Leave Assignee empty at creation** unless the TPM names an owner; the engineer is assigned when the story is pulled into a sprint. Populate the Developer field (`customfield_23650`) with the implementing engineer when known. Matches ICDC.
- **User Story names the surface.** Name the page or component the story touches (e.g., "Explore Dashboard," "Local Find Box on the Explore Dashboard," "Cart drawer") in the I-want clause. The Jira Summary field is the one-line title; there is no summary section in the body.
- **Parent Epic field set on the ticket itself.** Use `customfield_12350` per Section 10. There is no Parent Epic line in the body: the field is the only link.
- **No Jira ticket keys anywhere in the body.** Design tasks, predecessor stories, and sibling stories are `Relates` issue links; Figma and upstream-package references are remote links or comments. Jira's Links panel is the single source; repeating keys in the text is redundant and goes stale. There is no Notes section.
- **Acceptance Criteria are testable.** Each Functional item must be a single observable behavior QA can pass/fail in one check. If an item has two clauses joined by "and," consider splitting.
- **No testing section.** QA designs and owns all test cases, written from the Acceptance Criteria; the story does not prescribe how the work is tested. Unit test coverage is a team-wide engineering standard, like the quality bar, and is not restated per story (TPM decision, 2026-11-02).
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text: same rule as epics.

**Writing-and-publishing workflow**

1. Pull the existing ticket via `GET /rest/api/2/issue/<KEY>` to capture the original requirements text: preserve the substance, restructure the form. Fix typos and tighten wordings; don't drop requirements.
2. Confirm the parent epic link via `customfield_12350` on the ticket itself; if missing, set it before pushing the description.
3. Draft the description in Jira wiki markup with all 3 sections in order, applying the section emojis and content rules above.
4. Push the description via `PUT /rest/api/2/issue/<KEY>`, passed as `{"description": "..."}` in `fields`; read it back and confirm it starts with `h3.`, not `###`.
5. Add `Relates` issue links to the design task and any predecessor or sibling stories via `POST /rest/api/2/issueLink`.
6. Verify the rendered description with a UI screenshot from the user: wiki source is unreliable as a render preview (per 7b-shared).
7. If rendering is broken, first re-check the wiki source for any unescaped `{...}`.

**When to expand vs trim**

- **Story has fewer than 5 functional ACs** → keep all 3 sections; the structure earns its keep on small stories by giving the engineer and QA a checklist.
- **Story is a pure documentation update or copy change with no code path** → same 3 sections; QA verifies against the Acceptance Criteria.
- **Story is a spike or research task with no acceptance criteria** → this template is the wrong shape. Use the General Task template (7m), which states a spike's deliverable as a written assessment.
