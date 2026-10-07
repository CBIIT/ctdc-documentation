### 7l. 🛠️ DevOps Task Template (Drafted v1)

> **Use this template for every CTDC DevOps task**: infrastructure changes, engine or platform upgrades, environment variable changes, bucket or access setup, pipeline and Jenkins work, and security remediation on infrastructure. The canonical example is **CTDC-2261 (DevOps: Update Amazon Aurora RDS Database)**, a compliance-driven engine upgrade across all four tiers with STAGE and PROD routed through CloudOne.
>
> **Not for release deployments.** Promoting a software release candidate to Stage or Production uses the Stage and Prod deploy task templates in `release-ticket-templates.md`. This template covers everything else DevOps does.

**Why this template**

DevOps work has two traits no other CTDC template captures:

1. **It touches tiers unevenly.** A change may land in DEV and QA this sprint and in STAGE and PROD next month, or only in the upper tiers (a compliance upgrade). The ticket has to show, at a glance, which tiers are in scope and how far each one has gotten.
2. **The upper tiers are not ours to touch.** The team works directly in DEV and QA, but STAGE and PROD are operated by the CBIIT CloudOne team. Every upper-tier change goes through an NCI ServiceNow request (https://service.cancer.gov/ncisp). A DevOps ticket without a record of those requests leaves the TPM unable to answer "where is this stuck?"

Environment variables get their own section because they are the most common thing to drift between tiers: set in QA, forgotten in PROD, and the app breaks only after release.

**Summary format**

`DevOps: <imperative description>`, for example `DevOps: Update Amazon Aurora RDS Database`. Same prefix pattern as `Design: ...` tasks.

**Section order (7 sections, exactly this sequence)**

Each header is a Jira wiki `h3.` heading using the emoji + bold title format shown. Don't omit, reorder, or merge sections. If a section has no content, say "None at this time" rather than dropping the header.

1. `h3. 🎯 *Task Summary*`: Two to three sentences: what is changing, on which system, and why. Follow with labeled bullets for the facts that drive scheduling:
   * `* *System*:` the resource(s) affected (instance, bucket, service, pipeline)
   * `* *Deadline*:` a hard date if one exists (compliance, vendor end of support); otherwise omit the bullet

2. `h3. 🧭 *Scope*`: Two labeled bullets: `* *In scope*:` and `* *Out of scope*:`. Out of scope matters most for upgrades, where "while we're in there" changes creep in.

3. `h3. 🌐 *Environments*`: One row per tier, always all four, so a skipped tier is a visible decision and not an oversight.

   ```
   ||Tier||In Scope||Access Path||Status||
   |DEV| | Direct (DevOps) | |
   |QA| | Direct (DevOps) | |
   |STAGE| | ServiceNow to CloudOne | |
   |PROD| | ServiceNow to CloudOne | |
   ```

   * *In Scope*: Yes or No.
   * *Status*: Not started, In progress, Done, or N/A.

4. `h3. 🔑 *Environment Variables*`: One row per variable created, changed, or removed. The tier columns record **state**, never values.

   ```
   ||Variable||Service||Change||Secret||Value Source||DEV||QA||STAGE||PROD||
   |{{VARIABLE_NAME}}| backend | Add | Yes | Secrets Manager: <path> | | | | |
   ```

   * *Variable*: wrap the name in `{{ }}` monospace so underscores don't open italics.
   * *Change*: Add, Update, or Remove.
   * *Secret*: Yes if the value is a password, token, key, or connection string with credentials.
   * *Value Source*: where the value lives (AWS Secrets Manager path, SSM Parameter Store path, or "Plain config" for non-secrets).
   * *Tier cells*: Set, Pending, or N/A.

   If the task changes no variables, state "None at this time".

5. `h3. 🎫 *ServiceNow Requests*`: One row per upper-tier request filed with CloudOne. Mirrors the Tracking table on the deploy task templates so the two read the same.

   ```
   ||Tier||ServiceNow Ticket||URL||CloudOne Assignee||Scheduled Date||Notes||
   |STAGE| | | | | |
   |PROD| | | | | |
   ```

   If no upper tier is in scope, state "None at this time".

6. `h3. 🚦 *Workflow*`: Numbered steps in tier order. The standard spine (adapt the middle steps to the task):
   1. Make and verify the change in each in-scope lower tier (DEV, then QA).
   2. File the NCI ServiceNow request for STAGE at https://service.cancer.gov/ncisp, add the TPMs (`kuffelgr`, `singletonss`) as watchers, and record it in ServiceNow Requests.
   3. CloudOne applies the change to STAGE in the scheduled window; verify and update Environments.
   4. Repeat for PROD only after STAGE is verified.
   5. Update the tables as each step completes and notify the TPM.

7. `h3. ✅ *Definition of Done*`: Labeled bullets, the standard set:
   * `* *Tiers*:` every in-scope tier marked Done in Environments
   * `* *Variables*:` every variable shows Set (or N/A) in every in-scope tier
   * `* *ServiceNow*:` every request closed by CloudOne
   * `* *Verification*:` the application is confirmed working in each changed tier
   * `* *Documentation*:` any runbook, README, or deployment config reflecting the change is updated
   * `* *Handoff*:` TPM notified

**Standing emoji set (7 entries)**

| Section | Emoji |
|---|---|
| Task Summary | 🎯 |
| Scope | 🧭 |
| Environments | 🌐 *(unique to DevOps task)* |
| Environment Variables | 🔑 *(unique to DevOps task)* |
| ServiceNow Requests | 🎫 *(unique to DevOps task)* |
| Workflow | 🚦 |
| Definition of Done | ✅ |

**Required content rules (DevOps Task specific: universal rules in 7b-shared also apply)**

* *Never put a secret value in Jira*: not in the description, comments, or attachments. Jira is readable by far more people than the secret should be, and its history keeps every old version forever. Record the variable name and where the value lives; hand the value to CloudOne through the ServiceNow request or the secret store itself.
* *All four tiers in Environments*: even when only one is in scope.
* *Upper tiers go through ServiceNow*: no STAGE or PROD row moves to Done without a matching ServiceNow Requests row.
* *Issue type is Task*: not Story or Subtask.
* *Body is Jira wiki markup*: Markdown translation is disabled on the connector (Sep 2026). Use `h3.`, `||header||` tables, and `* *Label*: content` bullets; escape literal curly braces as `\{` and `\}`.
* *No Jira ticket keys in the body*: related work goes in Jira's issue links.
* *Colons, not em dashes*.
* *Left unassigned on creation*: unless the TPM directs otherwise. CTDC DevOps engineers are Charles Ngu (`nguca`, DevOps Lead) and Michael Fleming (`flemingme`).
* *Lower tiers first*: when a change can be made in DEV and QA, make it there before filing the STAGE request, so STAGE is never the first place a change has run.

**When to trim**

* *Lower-tier only work* (a DEV Jenkins job, a QA bucket): ServiceNow Requests says "None at this time"; keep the header.
* *No variable changes* (an engine upgrade): Environment Variables says "None at this time"; keep the header.
* *Release promotions*: don't use this template; clone the Stage or Prod deploy task.

**Changelog**

* *v1 (2026-10-07)*: Drafted with CTDC-2261 as the canonical ticket. ID 7l (not 7k, which is the legacy crosswalk letter for DO-DBGAP and is never reused).
