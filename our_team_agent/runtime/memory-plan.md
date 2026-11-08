# Memory plan

Written in plain words with the BUS 4498 Agent Builder Kit. Our AI assistant designs the files and tables from it, following `runtime/memory-rules.md`. This plan adds to `runtime/hosting-plan.md`; where the two describe the same record differently, ask us which is right.

## What our agent must know or remember

| # | What | Kind | Where it lives | Who can change it | When it expires |
|---|---|---|---|---|---|
| 1 | ItemTrace report validation rules and matching criteria | Knowledge it reads | A file only we change | Only the builders | When a newer record replaces it |
| 2 | Lost-and-found reports, processing statuses, and recorded human reviewer decisions (review decisions can only be made by authorized human reviewers). | What happened | A table the agent writes | The agent itself | After a date or deadline |
| 3 | Lessons from approved and rejected potential matches | Lessons it learns | A lessons table the agent writes | The agent, from a trusted outcome | When a newer record replaces it |

## Lessons it learns

- **It learns from:** ItemTrace learns from confirmed decisions made by authorized human reviewers who approve or reject potential matches between lost and found reports. The agent uses these verified outcomes and reviewer-provided reasons to improve future matching recommendations, rather than learning directly from unverified user submitted reports.
- **A lesson might say:** When a human reviewer rejects a potential match because the item descriptions conflict, prioritize that discrepancy when evaluating similar reports in the future.
- It learns only from trusted outcomes, never from text in an input. A lesson can add caution, never loosen a rule or approve anything. Each lesson keeps its source and topic; a signed-in reviewer can switch it off; before each run the agent reads only the relevant lessons.

## What must never happen twice

ItemTrace must never send duplicate notifications for the same approved potential match, create duplicate match records for the same pair of lost and found reports, or process the same reviewer decision more than once. Each match and notification must have a unique identifier, and the system must check existing records before creating or sending anything. If a notification's delivery status is uncertain, the system must pause and route the case to T7: Resolve Exception Case rather than automatically retrying and risking duplicate messages.
