# Review Potential Match Task Specification

## Basic Information

- **Task ID:** T5
- **Task name:** Review Potential Match
- **Task type:** Decide
- **Task owner:** Authorized ItemTrace human reviewer

## 1. Task Description

An authorized person reviews the match recommendation and its underlying report evidence. The reviewer approves a potential match for notification, rejects it, or asks for clarification. No automated assessment counts as approval. The reviewer records the decision and identifies the intended notification recipient when approving a match.

## 2. Inputs

### Input 1

- **Input name:** Match recommendation
- **Contents and format:** Structured T4 result with the new report ID, ranked candidate report IDs, supporting evidence, contradictions, uncertainty, and T4 status.
- **Source:** T4: Analyze Potential Matches

### Input 2

- **Input name:** Report details for review
- **Contents and format:** Original and standardized details for the new report and proposed candidate reports, identified by report ID. Contact details are shown only if needed by an authorized reviewer.
- **Source:** ItemTrace report records referenced by T4.

- **If a required input is missing or invalid:** Send the case to T7: Resolve Exception Case for missing evidence. Do not approve or send a notification.

## 3. Outputs

### Output 1

- **Output name:** Human match decision
- **Contents and format:** Structured record with new report ID, candidate report ID, reviewer identity, decision (`approved`, `rejected`, or `needs_clarification`), decision time, reason, and the intended recipient report ID if approved.
- **Next task or recipient:** T6: Notify User when approved; workflow record when rejected; T7: Resolve Exception Case when clarification is needed.
- **Complete when:** An authorized reviewer has recorded a decision with a reason, and only an approved decision is routed to T6.

## 4. Planned Tools

### Tool 1

- **Tool name:** `record_match_decision`
- **Input:** Match recommendation; Report details for review
- **Output:** Human match decision
- **Implementation Route:** File operations
- **Integration approach:** Direct integration
- **Role in this task:** Display the evidence to the authorized reviewer and record that person's explicit decision. The tool does not decide on the reviewer's behalf.
- **Task timeout:** Human response deadline of one business day after the case is assigned.
- **Maximum retries:** Not applicable — manual task.
- **Retry only when:** Not applicable.
- **On timeout, exhausted retries, or an error that cannot be retried:** Keep the case pending, record `review_overdue` or the recording error, and send it to T7: Resolve Exception Case. Do not treat silence or a failed save as approval.
