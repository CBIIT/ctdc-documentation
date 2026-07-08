### 12d. 📝 Post-Meeting Sprint Review Recap Workflow (Drafted)

> **Use this workflow once per sprint, immediately after the Sprint Review & Retrospective meeting concludes.** The canonical examples are the **Sprint 25 recap** (April 24, 2026 — release-heavy, multiple demo highlights) and the **Sprint 26 recap** (May 15, 2026 — artifact- and research-heavy, no live demos). Both follow the same template; the demos section toggles based on what shipped.

**Why this workflow**

The Sprint Review & Retrospective meeting produces a lot of meaningful content — sprint numbers, goal outcomes, demo highlights, retro themes, action items with owners, shout-outs — that lives only in the meeting transcript and a temporary retro board unless someone captures it. The post-meeting recap is the durable artifact that team members and stakeholders refer back to. It also gives the team a written record of action items so they don't get lost between sprints.

This workflow is **separate from** the pre-meeting Demo Day workflow (SKILL.md Section 12a). Demo Day produces a Slack announcement, a deck, per-presenter prep sheets, a retro board, and a goal scorecard *before* the meeting. The post-meeting recap consolidates the actual outcomes *after* the meeting. Two different artifacts, two different cadences, both required.

**Cadence:** Once per sprint, on the day of the Review & Retro meeting. The TPM (Gina) personally posts the recap to the Teams meeting chat the same day, while context is fresh, and posts a backup copy to Slack `#ctdc-discussions`.

---

**Inputs Required (Two Sources — Both Mandatory)**

1. **The meeting transcript** — Microsoft Teams auto-generates a `.docx` transcript after the meeting ends. The user uploads it (typically to `/mnt/user-data/uploads/`). This is the **only source of truth** for retro themes, action items, shout-outs, and goal-scorecard outcomes — never infer these from Jira data alone. Extract with `extract-text /mnt/user-data/uploads/<transcript>.docx`.
2. **The Jira sprint data** — pull via `jira_get_sprint_issues` on the closed sprint's ID. **The sprint being reviewed is the one that just closed, NOT the active sprint.** Verify counts via script per SKILL.md Section 6b — never hand-count.

If either input is missing, surface the gap and wait. Do not generate retro content from Jira alone — that fabricates the human content of the meeting.

---

**Workflow (9 Steps)**

1. **Verify the sprint identity.** Call `jira_get_sprints_from_board` with `board_id=641, state=active` to confirm which sprint is currently active (the *next* sprint). Then locate the closed sprint immediately preceding it (use `state=closed` and paginate with `start_at` if needed — recent closed sprints land deep in the pagination). Confirm the closed sprint's dates align with the meeting date.

2. **Pull all sprint tickets.** `jira_get_sprint_issues` with `fields="summary,status,assignee,issuetype,priority,labels,created,updated,resolutiondate,customfield_12350"` and `limit=50`. Most CTDC sprints have 40–50 tickets and fit in one page. Paginate if `total > 50`.

3. **Tally counts via script.** Write a short Python script that reads the raw JSON and prints counts by status, status category, issue type, assignee, completion (done vs not), carry-over, and epic membership. **Never hand-count.** A reusable tally pattern: load the JSON, use `Counter` for single-field tallies and `defaultdict` for cross-tabs, print sections labeled `=== BY STATUS ===`, `=== BY TYPE x DONE ===`, `=== DONE BY ASSIGNEE ===`, `=== HOT SPOTS (On Hold / Reopened / Open) ===`, etc.

4. **Extract the transcript.** Use `extract-text /mnt/user-data/uploads/<file>.docx` to get the meeting text. Read it in full before drafting. Note: the transcript includes speaker labels with timestamps — preserve attribution as you build retro sections.

5. **Reconcile Jira numbers with transcript numbers.** The TPM may have quoted slightly different counts during the meeting (typical: 1–2 tickets close between the meeting and the post-meeting data pull). **Honor the meeting numbers in the recap** — they reflect what the team discussed. Note minor reconciliations only if material. Sprint 26 example: meeting said "47 tickets, 7 closed, 15%"; post-meeting Jira pull showed "48 tickets, 9 closed, 18.8%" — recap used the meeting numbers because CTDC-1990 closed on the morning of the meeting (after the TPM took her snapshot) and a small drift accumulated.

6. **Apply credit corrections from the transcript.** Jira's assignee field is often the closer (QA or TPM), not the doer. When the TPM explicitly corrects attribution during the meeting ("credit goes to Hannah for the design"), override Jira's assignee for that ticket in the recap. Sprint 26 example: CTDC-2039 was assigned to Gina in Jira but credited to Hannah Stogsdill in the recap.

7. **Cross-reference the previous sprint's flags.** If the prior sprint flagged a recurring issue (e.g., "code review backlog growing") and the current sprint's data shows it got worse, that's not a new flag — it's a worsened flag. Call it out explicitly in the Risks & Flags section so the team sees the trend. Sprint 26 example: the Ready-for-Review backlog grew from 5 (S25) to 13 (S26) — the recap surfaced this as a worsened flag rather than a fresh observation.

8. **Draft the recap** following the template structure below. Save markdown to `/mnt/user-data/outputs/CTDC_Sprint{N}_Review_Retro_Recap.md` for the user to paste into Teams and Slack.

9. **Update SKILL.md or this workflow file if the workflow itself evolved during this run.** Each sprint may reveal a new edge case (a sprint without a Jira-recorded goal; a meeting where the TPM corrected ticket attribution; a sprint with no demos following a release-heavy sprint). Capture these in the Lessons Learned section so they're available for the next sprint.

---

**Template Structure (10 Sections, Exact Order)**

Each section header uses the emoji + bold title format shown. Sprint 25 and Sprint 26 both use this structure — match them for visual continuity across sprints.

1. **Title block** — `🎯 **Sprint {N} Review & Retrospective — Recap**` + meeting date + sprint dates as a single line: *"Date: {meeting date} · Sprint dates: {start} – {end}"*

2. **📊 Sprint {N} by the Numbers** — Bulleted list:
   - Total tickets pulled into the sprint
   - Closed count + completion %
   - Carry-over count (into next sprint)
   - Work mix breakdown: bugs (done/total), tasks (done/total), user stories (done/total)
   - Release context if relevant ("Release candidate deployed to prod on {date}" or "No release this sprint — research-heavy")
   - Demo state ("Live demos this sprint" with brief description, or "No live demos — work is at earlier pipeline stages")

3. **✅ Sprint Goal Scorecard** — Score each goal against ✓ DELIVERED / ▲ PARTIAL / ✗ NOT STARTED. If no Jira-recorded sprint goal, score the *implicit* focus areas the TPM names during the meeting. Headline format: *"X of Y goals delivered."* Use a brief one-sentence-per-goal narrative explaining the outcome.

4. **🎤 Demo Highlights** *(or **🎤 No Live Demos This Sprint**)* — Brief description of each demo with credits (presenter + feature + ticket refs), OR an explanation of why no demos this sprint (artifact-heavy, research-heavy, work still in Ready-for-Review).

5. **📦 What Landed in Sprint {N} ({count} Closed Tickets)** — Bulleted list of closed tickets with the human story — who did what, why it matters. Pull credits from the transcript when they correct Jira's assignee. One bullet per ticket; group related tickets when sensible (e.g., the BlindID work spanning multiple tickets).

6. **🔭 What's Still in Flight (Major Bodies of Work)** — Grouped by initiative (not by status), with epic-level framing of where each body of work is in its lifecycle. Typical groupings: Security upgrade bundle, Megazip / file download architecture, Study Files implementation, NCI-MATCH data work, Prefect / OpenSearch infrastructure.

7. **🚩 Risks & Flags** — Color-coded bullets: 🟥 high / 🟨 medium / 🟩 info. Cross-reference with the previous sprint's flags — any that *got worse* are explicitly flagged as such.

8. **🪞 Retro Themes (Liked · Lacked · Learned)** — Three subsections (💚 **Liked**, 🟡 **Lacked**, 🧠 **Learned**) with bullets pulled directly from the transcript. Attribute each bullet to the speaker in parentheses where the transcript identifies them. Each bullet is one sentence to one short paragraph.

9. **✅ Action Items** — Numbered table with columns: # | Item | Owner | Notes. Each action must have an explicit owner — if the transcript doesn't name one, ask the user before publishing. Notes column carries context (e.g., "Carry-over from S25", "Ongoing", or a related Jira key).

10. **🙌 Shout-outs** — From the transcript: who thanked whom for what. Include the TPM's shout-outs to team members and any peer-to-peer thanks captured in the retro. End with a closing line: *"Thanks everyone — have a great weekend!"* with an emoji.

---

**Distribution Channels**

- **Primary:** Paste markdown into the Microsoft Teams meeting chat. Teams renders the markdown formatting (bold, bullets, tables, emoji) correctly.
- **Backup:** Post the same content to Slack `#ctdc-discussions` (channel ID `CQ4ABNA4T`). Slack handles emoji and bullets but does NOT render markdown tables — convert the action items table to a bulleted format (`1. **Item** — Owner: X — Notes: Y`) before pasting to Slack, OR post the action items as a separate threaded message. See SKILL.md Section 14b for Slack formatting failures to avoid.
- **Who posts:** The TPM (Gina) personally posts the recap to both channels. Do NOT use `slack_send_message` or `slack_send_message_draft` automation for this post unless specifically directed — the recap reads as a personal communication from the TPM, not a bot post.

---

**Standing Emoji Set**

| Section | Emoji |
|---|---|
| Title | 🎯 |
| Sprint by the Numbers | 📊 |
| Sprint Goal Scorecard | ✅ |
| Demo Highlights (or No Demos) | 🎤 |
| What Landed | 📦 |
| What's Still in Flight | 🔭 |
| Risks & Flags | 🚩 |
| Retro Themes | 🪞 |
| Liked (within Retro) | 💚 |
| Lacked (within Retro) | 🟡 |
| Learned (within Retro) | 🧠 |
| Action Items | ✅ |
| Shout-outs | 🙌 |

---

**Format Continuity Across Sprints**

The two recaps (S25 and S26) share the same emoji vocabulary, section order, and tone. The structural difference is the demos section — S25 had multiple demo highlights because FE v1.3.0 shipped to prod; S26 had a "No Live Demos This Sprint" callout because the sprint was artifact-heavy. Both fit the same template — just toggle the demos section based on what shipped.

| Element | Sprint 25 | Sprint 26 |
|---|---|---|
| Tickets in sprint | 40 | 47 |
| Closed (DoD) | 11 | 7 |
| Completion rate | 28% | 15% |
| Carry-overs | 29 | 40 |
| Release shipped | FE v1.3.0 to prod | No release |
| Demo section | Multiple demo highlights | "No Live Demos This Sprint" callout |
| Goal scorecard | 2 of 3 Jira-recorded goals delivered | 0 of 2 implicit focus areas fully delivered (both ▲ PARTIAL) |
| Ready-for-Review backlog | 5 tickets | 13 tickets *(tripled — flagged in recap as a worsened flag from S25)* |

---

**Lessons Learned (Reinforcement)**

- **Transcript is the source of truth, not Jira.** Retro themes, action items, shout-outs, and goal-scorecard outcomes come from what was said in the meeting. Jira data provides the numerical backdrop only. If the transcript is missing, the workflow stops — do not improvise human content from Jira data.
- **Always reconcile transcript counts vs Jira counts.** If the TPM quoted "47 tickets, 7 closed" during the meeting and the post-meeting Jira pull shows "48 tickets, 9 closed," the small drift is normal — tickets close between meeting time and data pull. Honor the meeting numbers in the recap; they reflect what the team discussed.
- **Credit corrections come from the transcript.** Jira's assignee field is sometimes the closer (often QA or TPM), not the doer. When the TPM says "credit goes to Hannah for the design," override Jira's assignee for that ticket in the recap.
- **Cross-reference the previous sprint's flags.** If S25 flagged "code reviews need to be timely" and S26's data shows the Ready-for-Review backlog tripled, that's not a new flag — it's a worsened flag. Call it out explicitly so the team sees the trend.
- **The sprint being reviewed is NOT the active sprint.** Verify against `jira_get_sprints_from_board` every time. Confusing them produces wrong-sprint recaps.
- **Sprints without a Jira-recorded goal are common.** When the Jira sprint object has no `goal` field, the TPM names implicit focus areas during the meeting. Score those instead. Don't skip the scorecard section — the scoring framework is what gives leadership a fair read on the sprint regardless of raw completion %. Sprint 26 had no Jira goal but two implicit focus areas (Megazip architecture, CVE remediation epic) — both scored ▲ PARTIAL in the recap.
- **The numbers tell a story, but the transcript tells the story.** Jira can show that 13 tickets are in Ready for Review — only the transcript reveals that the team committed to 1–2 business day review turnaround as a fix. Both belong in the recap; the data anchors the narrative.

---

**Cross-References**

- SKILL.md Section 6b — "Verify Before You Count" (the rule this workflow inherits)
- SKILL.md Section 6c — Pre-meeting Sprint Review Summary Format (the deck template)
- SKILL.md Section 12a — Demo Day Artifacts Workflow (pre-meeting cadence)
- SKILL.md Section 12c — Goal Scorecard Framing (scoring rubric this workflow applies)
- SKILL.md Section 14b — Slack Message Formatting Known Failures (Slack distribution caveats)

---

*Workflow drafted 2026-05-15 from the Sprint 26 Review & Retro meeting. Canonical examples: Sprint 25 recap (April 24, 2026) and Sprint 26 recap (May 15, 2026).*
