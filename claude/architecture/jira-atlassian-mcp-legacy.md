# Jira via the Atlassian MCP (Legacy Reference)

> **Status: retired 2026-11-02, kept for reinstatement.** CTDC Jira work now runs through the Jira REST API v2 from a signed-in `tracker.nci.nih.gov` tab in Claude in Chrome. This page preserves how the Atlassian MCP connector was used, so it can be brought back if the Claude desktop app is able to link to claude.ai sessions again. It covers only what was specific to the MCP. Instance facts that hold for any client (custom field IDs, link type names, transition IDs, resolutions, wiki rendering rules) stay in `claude/SKILL.md` and the templates.

## Why it was retired

* *Connector*: the `mcp-atlassian` server, run in a Docker container and configured as a connector in the Claude desktop app on the TPM's work Mac. claude.ai sessions reached it through the desktop app's link to that computer.
* *What changed*: on 2026-10-27 the Claude desktop app on NCI-managed Macs moved to the organization's inference gateway and stopped signing in with the claude.ai account, which ended that link. Docker was retired on 2026-11-02 and both projects moved to Claude in Chrome.
* *What keeps working either way*: the REST calls in the templates work from any client with an authenticated session, so the templates do not need to change if the MCP returns. Only the "how to call Jira" layer changes.

## Connector configuration that mattered

* `DISABLE_JIRA_MARKUP_TRANSLATION=true` was set in the connector's environment (Sep 2026). With it on, descriptions and comments were stored exactly as sent, in Jira wiki markup. Before it was set, `jira_update_issue` converted Markdown to wiki markup on the way in, and that converter re-translated text that was already wiki markup (bold headings became italics, `#` lists became `h1.` headings, underscores gained backslashes). **If the MCP is reinstated, turn this setting on before the first write.**

## Crosswalk: MCP tool to REST call

MCP parameter names below are the ones recorded in this repo's templates and `SKILL.md` usage examples.

| MCP tool (parameters) | REST equivalent (from a signed-in tracker tab) |
|---|---|
| `jira_search` (`jql`) | `GET /rest/api/2/search?jql=...&fields=...&maxResults=...` (page with `startAt`) |
| `jira_get_issue` (`issue_key`, `fields`) | `GET /rest/api/2/issue/<KEY>?fields=...` |
| `jira_create_issue` (`project_key`, `summary`, `issue_type`, `assignee`, `description`, `additional_fields`) | `POST /rest/api/2/issue` with `{"fields": {"project": {"key": "CTDC"}, "summary": "...", "issuetype": {"name": "Task"}, "description": "...", ...}}` |
| `jira_update_issue` (`issue_key`, `fields`, `additional_fields`) | `PUT /rest/api/2/issue/<KEY>` with `{"fields": {...}}`; anything that went in `fields` or `additional_fields` goes in the one `fields` object |
| `jira_add_comment` | `POST /rest/api/2/issue/<KEY>/comment` with `{"body": "..."}` |
| `jira_transition_issue` (`issue_key`, `transition_id`, `fields`, `comment`) | `POST /rest/api/2/issue/<KEY>/transitions` with `{"transition": {"id": "..."}, "fields": {...}, "update": {"comment": [{"add": {"body": "..."}}]}}` |
| `jira_create_issue_link` (`link_type`, `inward_issue_key`, `outward_issue_key`) | `POST /rest/api/2/issueLink` with `{"type": {"name": "Blocks"}, "inwardIssue": {"key": "..."}, "outwardIssue": {"key": "..."}}` |
| `jira_get_sprints_from_board` (`board_id`, `state`) | `GET /rest/agile/1.0/board/641/sprint?state=active` |
| `jira_add_issues_to_sprint` | `POST /rest/agile/1.0/sprint/<sprintId>/issue` with `{"issues": ["CTDC-XXXX"]}` |
| `jira_batch_create_issues` | `POST /rest/api/2/issue/bulk` with `{"issueUpdates": [{"fields": {...}}]}`, or one create per ticket |
| `jira_link_to_epic` | None needed: it was a no-op on this instance (see below). Set `customfield_12350` instead. |

REST write requests also need the headers `X-Atlassian-Token: no-check` and `Content-Type: application/json`; the MCP handled these itself.

## MCP-only quirks

* **`jira_link_to_epic` reported success but never set the Epic Link** (`customfield_12350`) on this tracker. Always set the field with `jira_update_issue` and read it back.
* **The `epicKey` parameter on create was unreliable.** Create first, then set `customfield_12350` in a follow-up `jira_update_issue`.
* **`jira_batch_create_issues` did not apply the Epic Link at creation.** Every ticket in the batch needed its own follow-up update.
* **Two-step create.** `jira_create_issue` with a one-line placeholder description, then `jira_update_issue` with the full body. The tracker also rejects a missing description on epic creation, which holds for REST too.
* **The read tool's echo was not a render preview.** `jira_get_issue` returned wiki source that looked the same for working and broken tickets, and before translation was turned off its Markdown reconversion turned underscores into asterisks. Only a screenshot of the ticket in the Jira UI showed the real render.
* **Batch reads sometimes returned `null` for populated custom fields** (seen with the Developer field on a `jira_search`). Confirm with a single-ticket `jira_get_issue` before treating a field as empty.

## Old recovery message

The project instructions told Claude to stop and send this when the MCP tools were missing:

> "🐳 Docker doesn't appear to be running. The Jira/Atlassian MCP container needs to be active before I can pull any ticket data. Please start Docker and restore the container, then let me know when it's back up and I'll pick up right where we left off."

## Reinstatement checklist

1. Confirm the Claude desktop app on the work Mac can sign in with the claude.ai account and link to claude.ai sessions again (or that the organization's Claude platform supports connectors).
2. Restore the `mcp-atlassian` Docker connector in the desktop app with `DISABLE_JIRA_MARKUP_TRANSLATION=true`.
3. Smoke test before any real work: read one ticket, confirm `customfield_12350` and `customfield_23650` come back populated, and confirm a search returns the active sprint on board 641.
4. In every Claude Project that uses Jira (both Sprint Command Centers, Portfolio & Roadmap, QA & Testing), add the MCP back to *Connected Tools* and decide which recovery message applies. Keep Claude in Chrome as the fallback.
5. Leave the templates alone: their REST calls remain valid. Use the crosswalk above to translate when calling through the MCP.

## Where the pre-switch text lives

The last commit before the switch is `15701a9`. To read any file as it was, for example `claude/SKILL.md`:

```
git show 15701a9:claude/SKILL.md
```

## Appendix: CTDC authoring notes from the MCP era (verbatim)

Removed from `claude/SKILL.md` section 7b-shared (later `claude/templates/epic-templates.md`) on 2026-11-02 and kept here word for word. It documents the connector's Markdown conversion before 2026-09-24 and the CTDC-1960 and CTDC-2040 findings. It is history, not current guidance.

> **Authoring format (Jira wiki, since 2026-09-24): overrides the Markdown guidance below**
>
> The Jira connector (`mcp-atlassian`, Docker, configured in the Claude desktop app) now runs with `DISABLE_JIRA_MARKUP_TRANSLATION=true`. Nothing converts Markdown any more: whatever is sent is stored exactly. Author every description and comment in **Jira wiki markup**, and pass descriptions in `additional_fields` as `{"description": "..."}`.
>
> - h3 heading: `h3. 🎯 *Title*`
> - Bold: `*bold*`; italic: `_italic_`
> - Bullet: `* item` at column 0; numbered: `# item` at column 0
> - Monospace: `{{code_name}}`
> - Table: header row `||h||h||`, data rows `|c|c|`
> - Literal braces: `\{` and `\}`
>
> Why the change: prepending a card line to existing epics meant sending back text already in Jira wiki, and the connector's Markdown converter re-translated it (bold headings became italics, `#` lists became `h1.` headings, underscores gained backslashes). Turning translation off makes every write exact. The Markdown-era guidance below (and in 7a, the templates folder, and Section 9) is historical: read it through the list above. If the setting is ever removed, the Markdown rules apply again.
>
> **Markdown conventions (historical: applied while translation was on)**
> - **The `description` field expects Markdown input, not Jira wiki markup.** The `jira_update_issue` MCP tool docs are explicit: "for 'description', provide text in Markdown format." Markdown is converted server-side into Jira-wiki for storage. **If you write Jira-wiki by mistake (`*bold*`, `h3.`, `#` for ordered lists), the tool interprets the asterisks as Markdown italic emphasis and converts them to underscores — so your bold headers render as italic.** Always author in Markdown:
>
>   | Want | Write (Markdown) | Don't write (Jira-wiki) |
>   |---|---|---|
>   | h3 heading | `### **Title**` | `h3. *Title*` |
>   | Bold | `**bold**` | `*bold*` |
>   | Italic | `*italic*` | `_italic_` |
>   | Bullet | `- item` | `* item` |
>   | Numbered | `1. item` (auto-increments) | `# item` |
>   | Table | Jira-wiki `||h||` and `|c|` (passes through unchanged — see "Risk format" below) | (same — tables are the exception) |
>
>   Verified on CTDC-1960, 2026-04-30. The first push used Jira-wiki `*bold*` and section headers rendered as italic. The second push used Markdown `**bold**` and rendered correctly.
> - Section headers: `### {emoji} **{Title}**` (h3, emoji + bold).
> - Bullets are flush-left; no indented `-` bullets with bold labels.
> - Use `**bold**` for emphasis (matched delimiters).
> - **Glossary entries are flush-left bullets with matched-asterisk bold: `- **Term:**` (matched `**...**`), not `  - **Term:*` (indented, mismatched `**...:*`).** The MCP's Markdown→Jira-wiki converter reads the six-space indent as a level-two bullet and the mismatched closing single asterisk as italic-underline, producing broken garbage in the rendered Key Definitions section. The flush-left, matched-asterisk pattern is the canonical authoring form for any glossary-style bullet in a Jira body (the lean epic template has no glossary section; this applies to stories and comments that use one). Verified on CTDC-1922, 2026-05-11. Some older epics (e.g., CTDC-2040) use the broken pattern — treat that as a known inconsistency, not a template you should copy.
> - Inline route templates: plain text without backticks, but **escape every curly brace as `\{` and `\}`** (e.g., `#/study/\{study_code\}`). See "MCP write notes" below for why this is required. Backticks are fine for single-token names like `participantById` or `customfield_12350`.
> - **Curly-brace rule (universal).** Anywhere curly braces appear in description text — URLs with path parameters, scope items naming a variable, acceptance criteria, glossary entries, notes, anywhere — escape them with backslashes: `\{program_short_name\}`, not `{program_short_name}`. The only exception is text inside a fenced code block, where the renderer treats content as literal. The Markdown source you author with backslash-escapes will round-trip into Jira-wiki as `\{...\}` and render in the UI as literal `{...}` with no parser-state corruption.
>
> **MCP write notes (description field caveat)**
>
> The Jira-wiki renderer treats `{...}` as **macro syntax** — `{code}`, `{noformat}`, `{panel}`, `{color}`, etc. When the renderer encounters an unknown macro name like `{program_short_name}`, the parser does not gracefully fail back to literal text. Instead, parser state gets corrupted for the surrounding block, and the corruption propagates *forward* through the document. Symptoms include:
>
> - Adjacent headers losing their `h3.` recognition and rendering as literal `h3.` text
> - Bullets after the offending point losing their list context
> - Some headers disappearing entirely (no `h3.` prefix, just bare bold text on a line)
> - Paragraph layout fracturing right at the `{...}` occurrence
>
> This was the verified root cause of the CTDC-2040 (Program Details Page) render failure on 2026-04-30. The fix is straightforward: **escape every curly brace in description text as `\{...\}`** per the Markdown conventions rule above. After applying the fix, CTDC-2040 rendered cleanly on the next push.
>
> **Workflow:**
>
> 1. Author Markdown with `\{...\}` escaping anywhere curly braces appear in body text.
> 2. Push the description via the MCP (`jira_update_issue` with the `description` field).
> 3. **Verify the render with a UI screenshot from the user**, not the wiki source. Wiki source returned by `jira_get_issue` is unreliable as a render preview.
> 4. **If rendering is broken**, the most likely cause is unescaped curly braces somewhere in the description. Re-check the source for any `{...}` not preceded by a backslash.
> 5. **Last-resort fallback:** if the source is clean and the render is still broken, ask the user to re-paste the description through the Jira web UI's Description field. The UI normalizes whatever transport-level state the MCP introduced. This is rarely needed once the curly-brace rule is followed.
>
> **Other fields are safe via MCP.** Priority, summary, custom fields, status transitions, and labels write reliably through `jira_update_issue` because they don't go through the wiki-text renderer.
>
> **Comment body wiki-rendering quirks.** Comment bodies (added via `jira_add_comment` or `jira_transition_issue` with a `comment` parameter) also pass through the Jira-wiki renderer and have their own quirks separate from descriptions:
>
> - **Plus signs (`+`) get silently mangled.** The wiki renderer interprets `+` as inserted-text markup. A comment that says "Memgraph + OpenSearch" will render as "Memgraph  OpenSearch" with the `+` stripped. **Fix:** use plain English connectives like "and" or "plus" in comment bodies, or escape with a backslash if the literal `+` is essential. Verified on CTDC-1658 closure comment, 2026-05-06.
> - **Curly braces have the same macro-syntax issue as descriptions.** Apply the same `\{...\}` escaping rule.
>
> **Risk format (when a risk table is needed at all)**
>
> The lean epic template (7b-lean) has no Risks section: risks belong in the `.docx` epic summary (Section 5) or in a comment. When a table is needed anywhere in a Jira body, it renders as a three-column **table**, not a bullet list.
>
> **Format (Jira wiki table syntax — passes through Markdown unchanged):**
>
> ```
> ### ⚠️ **Risks & Mitigations**
> ||Risk||Impact||Mitigation||
> |Schema changes break generation scripts|Mock files become outdated|Implement automated generation triggered by schema version updates|
> |Mock data does not reflect real-world complexity|Submitters misinterpret requirements|Include edge-case examples and relational integrity scenarios|
> ```
>
> **Why Jira-wiki tables in a Markdown document?** The MCP's Markdown→Jira-wiki conversion does not handle GitHub-flavored Markdown tables (`| h | h |` / `|---|---|`) reliably for this MCP server's converter. Jira-wiki table syntax (`||h||` for headers and `|c|` for cells) passes through the converter unchanged and lands as a native Jira table. This is the one intentional exception to the "always author in Markdown" rule above. Verified on CTDC-1960, 2026-04-30 — the risk table was the only section that rendered correctly across all three pushes regardless of the surrounding Markdown/Jira-wiki confusion.
