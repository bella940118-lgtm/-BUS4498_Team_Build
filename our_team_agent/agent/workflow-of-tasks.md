# Workflow of Tasks

## 1. Workflow Goal

This workflow supports the goal in our completed [team charter] https://github.com/bella940118-lgtm/-BUS4498_Team_Build/blob/main/README.md.

The workflow supports ItemTrace's goal of improving the success rate of returning reported lost items to their owners by analyzing lost and found reports, identifying likely matches, and notifying users of potential matches.

## 2. Workflow Trigger

The workflow begins when a new lost or found item report is submitted to ItemTrace.

## 3. Completion Condition at Runtime

One workflow run is successfully completed when the submitted report has been processed and either no likely match is identified, a human reviewer rejects the proposed match, or an approved potential-match notification is confirmed as delivered. A case awaiting exception review is paused, not successfully completed.
## 4. General Workflow

When a new lost or found item report is submitted, ItemTrace first checks the report for the information needed to evaluate potential matches. The system then standardizes the report details so that descriptions can be compared consistently. It retrieves relevant reports from the opposite report type and analyzes characteristics such as item type, description, location, and date to identify potential matches.

If required information is missing, the report is sent for human review rather than continuing with incomplete information. If the system identifies a likely match, a human reviewer verifies the potential match before any notification is sent. If the reviewer approves the match, ItemTrace notifies the appropriate user of the potential match. If the reviewer rejects the match, the report is recorded as having no confirmed match and the workflow ends. If a required automated tool fails after its allowed retries, the case is also sent for human review before the workflow resumes or stops.

## 5. Workflow Diagram

```mermaid
flowchart TD
    START([New lost or found report submitted]) --> T1["T1: Validate Report"]

    T1 -->|Validation tool failure| T7["T7: Resolve Exception Case"]
    T1 --> D1{"Required information complete?"}
    D1 -->|No| T7
    D1 -->|Yes| T2["T2: Standardize Report"]

    T2 -->|Tool failure after retries| T7
    T2 -->|Success| T3["T3: Retrieve Candidate Reports"]

    T3 -->|Tool failure after retries| T7
    T3 -->|Success, including an empty result| T4["T4: Analyze Potential Matches"]

    T4 -->|Tool failure or unresolved evidence| T7
    T4 --> D2{"Likely match identified?"}
    D2 -->|No| END1([Complete: No likely match identified])
    D2 -->|Yes| T5["T5: Review Potential Match"]

    T5 --> D3{"Human review decision"}
    D3 -->|Approved| T6["T6: Notify User"]
    D3 -->|Rejected| END2([Complete: No confirmed match])
    D3 -->|Needs clarification| T7

    T6 -->|Delivery confirmed| END3([Complete: User notified])
    T6 -->|Failed or delivery uncertain| T7

    T7 --> D4{"Human resolution"}
    D4 -->|Report corrected| T1
    D4 -->|Retry standardization| T2
    D4 -->|Retry retrieval| T3
    D4 -->|Retry analysis| T4
    D4 -->|Verified not sent; retry notification| T6
    D4 -->|Prior notification confirmed| END3
    D4 -->|Cannot resolve now| END4([Stopped: Human follow-up required])
```
