# Analyze Potential Matches Task Specification

```yaml
task_id: "T4"
task_name: "Analyze Potential Matches"
task_owner: "ItemTrace matching agent"
```

## 1. Task Goal

- **Objective:** Analyze the new report against candidate reports and provide a ranked, evidence-based list of plausible matches for human review, or document that no supplied candidate is plausible.

## 2. Inbound Inputs

### Input 1

- **Input name:** Standardized new report
- **What it contains:** Report ID, lost or found status, normalized item type and description terms, location, date, original submitted values, and uncertainty flags.
- **Source:** T2: Standardize Report

### Input 2

- **Input name:** Candidate report list
- **What it contains:** Opposite-type report IDs and their item type, description, location, date, retrieval basis, and query status. A verified empty list is permitted.
- **Source:** T3: Retrieve Candidate Reports

## 3. Tool Permissions and Boundaries

### Task-Wide Limits

- **Total task timeout:** 90 seconds per run, including tool calls and retries.
- **Maximum tool calls:** 8 across all tools; retries count.

### Tool 1

- **Tool name:** `compare_report_features`
- **Tool type:** Planned Python comparison script.
- **Supports these permitted subtasks:** `compare_candidate_features` and `rank_plausible_matches`.
- **Allowed use:** Read only the new report and supplied candidate reports. Return supporting details, contradictions, evidence gaps, and a ranked list.
- **Prohibited use:** Change reports, search unrelated records, contact users, approve ownership, or send notifications.
- **Approval required:** None within the allowed use.
- **Timeout per call:** 15 seconds.
- **Maximum retries per call:** 1.
- **Retry conditions and failure response:** Retry once after a temporary processing error or timeout. If the retry fails, stop and hand off the case with the error and any usable evidence.

### Tool 2

- **Tool name:** `assess_ambiguous_match`
- **Tool type:** Planned language-model call.
- **Supports these permitted subtasks:** `examine_ambiguous_descriptions`.
- **Allowed use:** Examine only supplied report details when two descriptions could refer to the same item but use different wording. Return a structured explanation of supporting evidence, contradictions, and unknowns.
- **Prohibited use:** Invent facts, access other records, change report data, approve a match, or notify users.
- **Approval required:** None within the allowed use.
- **Timeout per call:** 20 seconds.
- **Maximum retries per call:** 1.
- **Retry conditions and failure response:** Retry once after a temporary service error or timeout. If still unavailable, use other evidence only when it supports a result; otherwise hand off as undetermined.

## 4. How the Agent Should Reason

### Permitted Subtask 1

- **Subtask name:** `compare_candidate_features`
- **Subtask description:** Compare item type, description, location, and date for the new report and each supplied candidate. Record both agreements and contradictions.
- **Subtask boundary:** Use supplied report data only. Missing information is not evidence of a match.
- **Retry limits:** 1 additional attempt after a temporary tool failure.

### Permitted Subtask 2

- **Subtask name:** `examine_ambiguous_descriptions`
- **Subtask description:** Examine wording differences when descriptions could plausibly refer to the same item. Identify supported, contradicted, and unknown details.
- **Subtask boundary:** Use the model only for ambiguity. Its assessment cannot replace report evidence or human approval.
- **Retry limits:** 1 additional attempt after a temporary tool failure.

### Permitted Subtask 3

- **Subtask name:** `rank_plausible_matches`
- **Subtask description:** Rank candidates by the strength of their evidence, with contradictions and evidence gaps shown beside each recommendation.
- **Subtask boundary:** Recommend a candidate for review only when its item type is compatible, at least one descriptive characteristic supports the match, and available date and location information do not directly rule it out. Do not confirm ownership.
- **Retry limits:** 0.

- **Decision guidance:** After each subtask, choose the permitted subtask most likely to resolve the most important remaining uncertainty. Skip the model call when report evidence is clear. Stop when no permitted subtask can make useful progress or a task limit is reached.

## 5. When to Stop or Hand Off to a Human

- **Stop successfully when:** T4 produces either a ranked list of plausible matches with evidence, contradictions, and unknowns, or a documented finding that no supplied candidate is plausible.
- **Hand off early when:** Required data is missing, material contradictions cannot be resolved, a tool fails after its allowed retry, or a task-wide limit is reached before a supported result is produced.
- **Hand off to:** T7: Resolve Exception Case, assigned to the ItemTrace exception reviewer.

Stop at the first applicable limit or handoff condition. While awaiting review, take no further autonomous action.

## 6. Outbound Deliverable

- **Status:** Completed or escalated to human.
- **Result or recommendation:** Ranked plausible matches, no plausible match, or undetermined.
- **Evidence summary:** Report IDs and the item type, description, location, and date evidence supporting or contradicting each candidate.
- **Subtasks performed:** Permitted subtasks completed, including repeated attempts.
- **Unresolved issues:** Missing or conflicting facts; write “none” only when none remain.
- **Handoff note:** Reason for escalation and what the reviewer needs to decide; write “Not applicable” when completed.
- **Next task or recipient:** T5: Review Potential Match for plausible matches; workflow completion when no plausible match is found; T7: Resolve Exception Case for unresolved cases.
