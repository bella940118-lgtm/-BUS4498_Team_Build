# Permissions and boundaries plan

Written in plain words with the BUS 4498 Agent Builder Kit. Our AI assistant builds it following `runtime/permission-rules.md`. Where this plan and our task specifications disagree, this plan wins.

- Agent: ItemTrace analyzes campus lost-and-found reports to identify potential matches between lost and found items. It standardizes report information, compares relevant details, routes potential matches for human verification, and notifies users only after approval. (`our_team_agent`)
- Owner, who answers for this agent: Alyssa Lu
- Written on: November 7, 2026

## 1. May (on its own)

| # | Action |
|---|---|
| 1 | Read submitted lost-and-found reports and validate required fields using predefined rules. |
| 2 | Standardize report fields and retrieve candidate reports from the opposite report type. |
| 3 | Analyze candidate reports and generate potential-match recommendations for human review. |
| 4 | Record processing statuses and no-match outcomes without notifying users. |

## 2. May, with a limit

| # | What | The limit | Kind | Who can change it |
|---|---|---|---|---|
| 1 | Send one notification to the appropriate user for each human-approved potential match. | At most one notification per approved match; only after recorded human approval and confirmation that no notification was previously sent. | Fixed: only the builders change it | Only the builders, by changing the code |

## 3. Ask first (hold for a person)

| # | When | What the agent does instead |
|---|---|---|
| 1 | A likely match is identified between a lost and found report. | Hold for T5: Review Potential Match. An authorized human reviewer must approve, reject, or request clarification before any notification is sent. |
| 2 | Required report information is missing, a tool fails after allowed retries, or notification delivery is uncertain. | Pause and route to T7: Resolve Exception Case. An authorized human reviewer determines the next action; the agent must not assume success or automatically retry uncertain notifications. |

## 4. Never

| # | Never | Where it is enforced |
|---|---|---|
| 1 | Send a potential-match notification without recorded human approval. | Code and a database rule |
| 2 | Create duplicate match records or send duplicate notifications for the same approved match. | Code and a database rule |
| 3 | Automatically retry a notification when its delivery outcome is uncertain. | Code |

## 5. Who signs off

| Decision | Who | How |
|---|---|---|
| Potential matches identified by T4. | Authorized human reviewer. | The reviewer approves, rejects, or requests clarification in ItemTrace. The system records the reviewer ID, decision, reason, and timestamp. |
| Exception cases involving incomplete reports, failed tools, or uncertain notification outcomes. | Authorized human reviewer. | The reviewer records the resolution and next action in ItemTrace, including their ID, reason, and timestamp. The case remains paused until resolved. |

## The off switch

The designated ItemTrace owner can pause or resume the agent using an administrative switch in the application, with a recorded reason and timestamp. When paused, the agent must not process new reports, analyze matches, or send notifications. Only an authorized owner can resume operations.

## How mistakes are reported

Users and authorized reviewers can report incorrect matches, notifications, or processing errors through a Report a Problem option in ItemTrace. Reports are logged with the affected case ID, issue description, and timestamp. The designated owner reviews reported problems, investigates their causes, and documents corrective actions without assigning blame. Urgent issues involving incorrect notifications or data exposure trigger immediate human review and may require pausing the agent.

## Rules we keep

- Every permission starts off. The agent may do only what this plan lists under *May* and *May, with a limit*.
- Writing a permission down does not enforce it. Every limit and every *Never* is enforced in code or in a database rule, never in instructions alone.
- A limit a person may change is a setting with an owner and a reason, which the code reads on every run.
- Asking means holding, not guessing: the record waits with a plain reason that names what is missing.
- A sign-off is a click in the app, recorded with who and when. A chat message is not a sign-off.
- Test mode never acts: with `dry_run`, the agent decides but saves nothing.
