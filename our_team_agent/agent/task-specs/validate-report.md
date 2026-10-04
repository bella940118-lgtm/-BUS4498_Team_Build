# Validate Report Task Specification

## Basic Information

- **Task ID:** T1
- **Task name:** Validate Report
- **Task type:** Verify
- **Task owner:** ItemTrace intake system

## 1. Task Description

Check a newly submitted lost or found report before matching begins. A deterministic validation checks that the report has a report ID, report type, item type, description, location, date, and submitter reference in usable formats. Validation reports missing or invalid fields; it does not fill them in or decide whether the report matches another report.

## 2. Inputs

### Input 1

- **Input name:** Submitted report
- **Contents and format:** A structured report containing a unique report ID, lost or found status, item type, free-text description, location, date, and a submitter reference. The submitter reference is retained for later authorized notification but is not used to assess a match.
- **Source:** Person submitting a lost or found report through ItemTrace.

- **If a required input is missing or invalid:** Record each missing or invalid field. Route the report and validation result to T7: Resolve Exception Case; do not send it to T2 until corrected and revalidated.

## 3. Outputs

### Output 1

- **Output name:** Validation result
- **Contents and format:** Structured record with report ID, `valid` or `needs_review` status, checked fields, and field-level errors. For a valid report, include the unchanged submitted report.
- **Next task or recipient:** T2: Standardize Report when valid; T7: Resolve Exception Case when review is needed.
- **Complete when:** Every required field has been checked, the result is stored with the report ID, and the report has been routed according to its status.

## 4. Planned Tools

### Tool 1

- **Tool name:** `validate_report`
- **Input:** Submitted report
- **Output:** Validation result
- **Implementation Route:** Functions/scripts
- **Integration approach:** Direct integration
- **Role in this task:** Check required fields and formats, return field-level errors, and assign the validation status. The tool does not alter submitted values.
- **Task timeout:** 30 seconds per report.
- **Maximum retries:** 1 additional attempt.
- **Retry only when:** A temporary processing or storage error occurs; wait 2 seconds before retrying. Validation failures caused by report content are not retried automatically. Use the report ID to avoid creating duplicate validation records.
- **On timeout, exhausted retries, or an error that cannot be retried:** Record `validation_error` and the available error details, then send the report to T7: Resolve Exception Case. Do not continue to T2.
