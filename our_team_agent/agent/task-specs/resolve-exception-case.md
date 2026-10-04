# Resolve Exception Case Task Specification

## Basic Information

- **Task ID:** T7
- **Task name:** Resolve Exception Case
- **Task type:** Decide
- **Task owner:** Authorized ItemTrace exception reviewer

## 1. Task Description

An authorized person reviews a case that cannot safely continue through the normal workflow. The case may involve missing report information, an automated tool failure, unresolved match evidence, a request for clarification, or failed or uncertain notification delivery. The reviewer records what is known and decides whether to request a correction, authorize a later retry, close the case, or keep it pending. T7 does not automatically resume the workflow or assume a notification was delivered.

## 2. Inputs

### Input 1

- **Input name:** Exception case
- **Contents and format:** Structured record with report ID, source task ID, exception type, status, available error or evidence details, attempts already made, and the point where processing stopped. For notification exceptions, include the approval decision ID and available delivery receipt or error.
- **Source:** T1: Validate Report; T2: Standardize Report; T3: Retrieve Candidate Reports; T4: Analyze Potential Matches; T5: Review Potential Match; or T6: Notify User.

### Input 2

- **Input name:** Relevant report and task records
- **Contents and format:** Original report, prior task outputs, and related candidate or notification records needed to understand the exception. Access is limited to the case under review.
- **Source:** ItemTrace case records.

- **If a required input is missing or invalid:** Keep the case pending, record what is missing, and ask the responsible report submitter or system owner for the information. Do not authorize a retry or close the case without enough evidence.

## 3. Outputs

### Output 1

- **Output name:** Human exception resolution
- **Contents and format:** Structured record with report ID, source task ID, reviewer identity, review time, reason, and one status: `correction_requested`, `retry_authorized`, `closed`, or `pending`. If a retry is authorized, identify the task and the evidence supporting a safe retry. If notification delivery is uncertain, record the verified delivery state or leave the case pending.
- **Next task or recipient:** ItemTrace workflow record and the person or system owner responsible for the recorded follow-up. A later resume requires the recorded correction or authorization.
- **Complete when:** The reviewer has recorded a supported decision and the case is visibly paused, closed, or assigned for follow-up; no automated task continues solely because T7 was opened.

## 4. Planned Tools

### Tool 1

- **Tool name:** `record_exception_resolution`
- **Input:** Exception case; Relevant report and task records
- **Output:** Human exception resolution
- **Implementation Route:** File operations
- **Integration approach:** Direct integration
- **Role in this task:** Present the case evidence to the authorized reviewer and record that person's decision and follow-up owner. It does not make the decision automatically.
- **Task timeout:** Human response deadline of one business day after assignment.
- **Maximum retries:** Not applicable — manual task.
- **Retry only when:** Not applicable.
- **On timeout, exhausted retries, or an error that cannot be retried:** Keep the case `pending`, record `exception_review_overdue` or the save error, and alert the ItemTrace system owner. Do not restart processing or treat an uncertain notification as delivered.
