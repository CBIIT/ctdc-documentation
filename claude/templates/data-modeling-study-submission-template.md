### 7g. 📚 Data Modeling for Study Submission Template (Drafted v4)

> **Use this template for every CTDC data modeling task driven by a study submission** — whether the study is brand-new to CTDC or an existing study requesting new properties, permissible values, or enums. Canonical example: **[CTDC-2051](https://tracker.nci.nih.gov/browse/CTDC-2051)** (NCI-MATCH Arm Z1D IHC data modeling). This template covers the **modeling-data** sub-function of data management when the work is anchored on a study's **CDE Request Workbook**. It is **not** for infrastructure-level model changes — those use the Data Model Update Task template (Section 7f). It is **not** for loading the study's data after the model is in place — that's a Data Loading Task (Section 7e).

**Why this template**

CTDC modeling work driven by study submissions is straightforward in shape: a study comes to us, we agree on terms via a CDE Request Workbook, we add them to `ctdc-model`, we tag and release, the Submission Portal picks up the new model, the study can submit. The ticket exists to track *when* milestones land. The workbook exists to track *what* is being modeled.

The hard rule: **the workbook is the term-level source of truth, not the ticket.** Pasting term inventories, per-term status, per-term decisions, or term counts into the ticket creates a stale snapshot that the team has to maintain in two places. The sections below are enough — anything more drifts toward duplicating workbook content or reproducing context that already lives on the parent user story.

**Three commitments**

1. **One Task per study's modeling work.** Single source of truth for the modeling concern from initial workbook review through Submission Portal verification. One study, one ticket, one workbook. Future additions to the same study get their own ticket.
2. **Parent user story carries study identity.** Modeling ticket links to the parent user story (e.g., CTDC-2051 → CTDC-1666). Study identity, submission chronology, document references, and study description live on the user story; this ticket holds only the modeling concern.
3. **CDE Request Workbook is the term-level source of truth.** The ticket references it in a structurally explicit section; the workbook owns the inventory and status of every term. The ticket does not duplicate term counts, term inventories, or per-term decisions.

**Section order (7 sections, exactly this sequence)**

Each section header is an `h3` Markdown heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. Don't add sections. If a section genuinely has no content, state so explicitly ("None at this time") rather than dropping the header.

1. `### 🎯 **Modeling Summary**` — Two to three sentences. Which study is driving this modeling work, whether it's a new study coming to CTDC or an addition to an existing study, and what the modeling work enables. Describe *what kind* of change it is, not *how many* terms it touches (counts live in the workbook). **Include a pointer to the parent user story** for full study identity, chronology, and submission context. Example: *"Update the CTDC data model to support the **NCI-MATCH Arm Z1D IHC Data Submission** — a new submission and study being onboarded to CTDC (full study identity, chronology, and submission context in **CTDC-1666**). The work consists of permissible-value and CDE-mapping changes — no new properties or nodes, no breaking changes. The CRDC Submission Portal and the CTDC Data Model Navigator both reflect the new model version on the same trigger — once promoted to `prod` and the release tag is cut."*

   A second short paragraph names the canonical SOP for the procedure: *"The canonical procedure for editing, branching, tagging, and downstream-syncing model changes lives at [`SOP_CTDC_Data_Model_Contribution.md`](https://github.com/CBIIT/ctdc-model/blob/prod/SOP_CTDC_Data_Model_Contribution.md) on the `prod` branch of `ctdc-model`. This ticket tracks the milestones; the SOP describes the procedure."*

2. `### 📚 **CDE Request Workbook**` — Required section. The CDE Request Workbook is the **system of record** for this modeling effort. The ticket tracks *when* milestones land; the workbook tracks *what* is being modeled. Per-term decisions, term status, and caDSR/SI coordination live in the workbook — not in this ticket.

   This section anchors the ticket to the workbook and declares the scope boundary between ticket and workbook. **Do not** include study identity here — that lives on the parent user story.

   | Field | Value |
   |---|---|
   | Workbook | *(SharePoint URL)* |
   | Workbook Owner | *(@username — CTDC Data Concierge or TPM)* |
   | Source-of-Truth Scope | *(e.g., "Term inventory, per-term status, CDE bindings, nodes and properties touched, per-term decisions")* |
   | Out-of-Scope for Ticket | *(e.g., "Per-term decisions and caDSR / SI coordination for new CDE codes — tracked in the workbook's Status column, not here")* |

   The fields are intentionally lean. The Workbook URL points readers to the source of truth; the Workbook Owner names the responsible party; the Source-of-Truth Scope and Out-of-Scope rows are the boundary statement that keeps workbook content out of the ticket over time. Term counts, version targets, and per-term decisions are not in this table — those belong in the workbook, the parent user story, or (for milestone state) Section 4 below.

3. `### 🔍 **DM Federal Lead & Subject Matter Expertise Review**` — Required section. New CDEs and new PVs require that a ticket is created by the DH Coordinator and linked to the corresponding Submission Request ticket. Per-term decisions and caDSR coordination detail are tracked in the ServiceNow ticket, not duplicated in this Jira ticket. This review gates the `develop` → `prod` promotion (Section 4).

   | Coordination Item | Value | Owner |
   |---|---|---|
   | ServiceNow Ticket | *(link — or "Not required: no new or changed CDEs/PVs")* | DM Fed Lead |
   | DHDM Jira Issue | *(link — created by the DH Coordinator, linked to the Submission Request ticket)* | DH Lead |

4. `### ✅ **Branch & Release Signoff**` — Milestone audit trail. Each row gets a date and reviewer initials as the milestone lands. The two verification surfaces are listed separately because each must be checked explicitly, but both update on the same release-tag trigger.

   | Milestone | Completion Date | Reviewer Initials | Notes |
   |---|---|---|---|
   | Feature branch PR opened into `develop` (`ctdc-model`) | | | Link the PR in the notes column |
   | `develop` PR merged | | | |
   | `develop` → `prod` PR opened | | | Link the second PR |
   | `prod` PR merged | | | |
   | Git tag created (matches YAML `Version:` field) | | | Confirm character-for-character match before tagging |
   | GitHub Release published | | | |
   | **CRDC Submission Portal shows new permissible values** | | | |
   | **CTDC Data Model Navigator shows new permissible values** | | | |

   This table is also where operational details land as work progresses: the version target shows up next to the git tag row when known; PR links go in the Notes column of their relevant rows; review history (e.g., "Reviewed 05/06/2026 (GK); awaiting final merge") sits in the Notes column of the `develop` PR merged row.

5. `### 🧪 **Verification Surfaces**` — Bullet list. Two end-user surfaces reflect every CTDC model version bump. Both update on the same trigger — the `prod` merge + release tag — and both must show the new model values before this ticket closes.

   - **CRDC Submission Portal** — confirms the submission team can target the new permissible values in a draft submission.
   - **CTDC Data Model Navigator (DMN)** — confirms researchers using CTDC see the new permissible values on the model's detail views.

6. `### ⚠️ **Open Questions & Risks**` — Bullet list of decisions or unknowns to resolve. Each bullet is one question or risk. Only items that fall outside the workbook's scope live here. Per-term decisions (Uberon choice, CDE bindings, term status, nodes and properties touched) are tracked in the workbook. Resolve in ticket comments so the audit trail lives with the modeling work. If none at ticket creation, state "None at this time." Common entries on study-modeling tickets:

   - **Version coordination with other in-flight modeling work (risk).** If multiple modeling efforts target the same release window, the version-bump sequencing needs to be coordinated so neither claims a version the other expects. Surface the conflicting ticket key and name the coordination owner.
   - **External dependency on caDSR / SI.** If a subset of terms are missing CDE codes and the ServiceNow request to caDSR is in flight, surface that here as a risk to `prod` promotion timing. Don't enumerate the terms (they're in the workbook); just note the dependency and the DHDM Jira Issue tracking it (Section 3).
   - **Cross-cutting model questions** (e.g., a consultation with the CRDC SI lead on whether to add or restructure a node) that affect this modeling work or follow-on tickets.

7. `### 📝 **Notes**` — Bullet list. Operational context that's worth keeping with the ticket but doesn't fit anywhere else. Common entries:

   - Working-meeting cadence reference, with a pointer to the parent user story for full chronology.
   - Pointers to sibling modeling tickets (closed or active) for the same study or program.
   - Methodology learnings worth preserving as precedent (e.g., a SemVer decision: "new property/enum additions are MINOR per `ctdc-model` contribution README").
   - References to attached files on the ticket (e.g., `missing_codes.xlsx`) and what they feed.
   - DH ticket pointers (DHDM-XX) once they exist.
   - Pointer back to the parent user story's comment that finalized the scope for this modeling work.

   If there's no meaningful note, write "None at this time."

**Standing emoji set (7 entries)**

| Section | Emoji |
|---|---|
| Modeling Summary | 🎯 |
| CDE Request Workbook | 📚 |
| DM Federal Lead & Subject Matter Expertise Review | 🔍 *(shared with the Data Model Update Task — same caDSR/PV governance review gate)* |
| Branch & Release Signoff | ✅ |
| Verification Surfaces | 🧪 |
| Open Questions & Risks | ⚠️ |
| Notes | 📝 |

**Required content rules**

- **Seven sections, no further additions.** Linked Work, Term Status Summary, Modeling & Promotion Workflow, and Collaboration & External Dependencies are deliberately *not* sections in this template. Linked Work belongs in Jira's native issue-link UI. Term Status Summary duplicates workbook content. Modeling & Promotion Workflow duplicates the SOP. Collaboration is implicit in the assignee, reviewer, and external dependency tracking in Sections 6 and 7.
- **Scope is study-driven model additions only.** A study submission's CDE Request Workbook drives the work. Infrastructure-level model changes use the Data Model Update Task template (Section 7f). Data loading uses the Data Loading Task template (Section 7e).
- **No counts.** The number of properties, enums, or permissible values being modeled is term-level detail that lives in the CDE Workbook, not the ticket. Describe the kind of change ("permissible-value and CDE-mapping changes"), never the tally.
- **One Task per study's modeling work.** New study onboarding gets one ticket. Subsequent additions for the same study get their own ticket. Don't roll forward into a single open ticket.
- **A parent user story is required.** Every modeling ticket links to a parent user story that carries study identity. If no parent user story exists, file it before this modeling ticket. Identity content lives on the user story — never duplicated on the modeling ticket.
- **The DM Federal Lead & Subject Matter Expertise Review is not optional when CDEs/PVs change.** New or updated CDEs/PVs go through a ServiceNow request to caDSR, and a DHDM Jira Issue is created by the DH Coordinator and linked to the Submission Request ticket (Section 3). This review gates `develop` → `prod` promotion. If no new or changed CDEs/PVs are involved, mark the row "Not required" rather than dropping the section.
- **Issue type is Task** on this tracker, matching CTDC-2051's convention.
- **Parent Epic field set on the ticket itself** via `customfield_12350` when a study onboarding or release epic exists.
- **The CDE Request Workbook is the term-level source of truth, not this ticket.** Section 2 anchors the URL with explicit scope and out-of-scope rows. Do not replicate the workbook's term inventory or per-term status anywhere on the ticket.
- **YAML `Version:` field and git tag must match exactly.** Verify before tagging; record the tag value in the Notes column of Section 4 when it's cut. The downstream Data Hub reads the version from the YAML, not the tag — a mismatch silently fails the Submission Portal sync.
- **The Submission Portal is the close trigger.** Not `prod` merge, not the GitHub Release — the actual verification that the Submission Portal displays the new model is what closes the ticket.
- **Curly braces escaped as `\{...\}`** anywhere they appear in description text — same rule as every other Jira description on this tracker.

**Writing-and-publishing workflow**

1. Confirm the parent user story exists. If it doesn't, file it first using the User Story template — the modeling ticket assumes it as the context anchor.
2. Confirm a CDE Request Workbook exists or is in active authoring. If it doesn't, the modeling ticket is premature — author the workbook first.
3. Confirm this work is study-driven (anchored on a CDE Request Workbook), not infrastructure-level (use Section 7f) and not loading (use Section 7e).
4. Create the data modeling task via `jira_create_issue` with `issue_type = "Task"`, a short placeholder description, and the parent epic linked via `customfield_12350` in `additional_fields`. Assign per the working-meeting cadence.
5. Push the description in two steps: create with the placeholder, then `jira_update_issue` with the full Markdown body. Same two-step pattern as every other CTDC template. **Do not hand-edit the description in the Jira UI afterward** — the wiki editor mangles monospace tokens and Markdown bullets; re-push through `jira_update_issue` instead.
6. Add a "Relates" issue link to the parent user story (required) and to any sibling modeling tickets for the same study or program.
7. Verify the rendered description with a UI screenshot — wiki source is unreliable as a render preview.
8. As work progresses, the data engineer adds GitHub PR links as Jira comments and updates Section 4 (Branch & Release Signoff) rows directly in the description. Section 3 (DM Federal Lead & SME Review) records the ServiceNow ticket and DHDM Jira Issue as those artifacts are created. Section 6 (Risks) and Section 7 (Notes) accumulate operational context as it develops.
9. **Submission Portal verification + tag-and-release together are the close trigger.** Once Section 4's last row is filled in (Submission Portal shows new permissible values) and the workbook's Status column is fully reconciled, transition the ticket to Closed.

**When to expand vs trim**

- **New study onboarding with a large workbook** → use the template as written. The sections handle any scale because counts and per-term state aren't on the ticket; the workbook absorbs scale.
- **Existing study, small addition** → use the template as written. Same shape.
- **Workbook still in early authoring** → file the ticket as a draft; Section 2 fields can be PLACEHOLDER until the workbook stabilizes. Section 4's signoff table is universal.
- **No new or changed CDEs/PVs** (e.g., a pure mapping or structural tweak agreed with the study) → keep Section 3 but mark the ServiceNow Ticket row "Not required: no new or changed CDEs/PVs."
- **A single property addition driven internally by the data team, not a study** → this template is the wrong fit. Use the Data Model Update Task (Section 7f).

**When NOT to use this template**

- **Infrastructure-level model changes** (breaking, framework upgrades, multi-repo refactors initiated internally) → Data Model Update Task (7f).
- **Loading study data after the model lands** → Data Loading Task (7e). Different verification surface, different ladder.
- **Software development that does not touch the schema** → software development template family.
- **CDE Workbook authoring as standalone work** — the workbook is a SharePoint artifact. Workbook authoring is data-adjacent operational work, not its own structured ticket.
- **Bento Core MDF framework changes** — upstream concern; file with the Bento team.

**Changelog**

- **v4 (2026-06-02)** — Added the **🔍 DM Federal Lead & Subject Matter Expertise Review** section (new Section 3), bringing 7g into lockstep with 7f and the canonical pilot CTDC-2068. The same caDSR/PV governance applies to study-driven modeling — arguably more directly, since study work always has a Submission Request: new CDEs/PVs require a ServiceNow request to caDSR and a DHDM Jira Issue created by the DH Coordinator and linked to the Submission Request ticket (owners: DM Fed Lead, DH Lead), gating `develop` → `prod`. Moved **Open Questions & Risks** to the ⚠️ emoji (🔍 now belongs to the new review section) and to Section 6; **Branch & Release Signoff** → Section 4, **Verification Surfaces** → Section 5, **Notes** → Section 7. Harmonized the caDSR/SI risk bullet and the writing-workflow steps to reference the new section. Added an explicit **No counts** rule (the discipline was already implicit in v3). Now **7 sections**. Canonical examples CTDC-2051 and CTDC-1799 predate this section and are flagged for an opportunistic retrofit (add the DM Federal Lead & SME Review section), the same way CTDC-1907 is flagged for its IndexD retrofit.
- **v3 (2026-05-20)** — Reduced to canonical 6-section shape per Gina's correction. v2 had inadvertently expanded to 10 sections by importing Linked Work, Term Status Summary, Modeling & Promotion Workflow, and Collaboration & External Dependencies — none of which were in the approved CTDC-2051 shape. v3 restores the original lean intent: the workbook is the source of truth for term-level state, and the ticket exists for milestone tracking only. CTDC-2051 and CTDC-1799 are both in v3 shape as of 2026-05-20.
- **v2 (2026-05-20, superseded)** — Bloated to 10 sections; not approved. Do not use.
- **v1 (2026-05-20, superseded)** — Initial draft modeled on CTDC-1799 pre-cleanup. Section 3 combined study identity with workbook anchor; superseded when the parent-user-story-carries-identity pattern was established.
