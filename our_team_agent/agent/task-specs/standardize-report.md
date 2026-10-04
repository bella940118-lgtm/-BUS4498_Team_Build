# Standardize Report Task Specification

## Basic Information

- **Task ID:** T2
- **Task name:** Standardize Report
- **Task type:** Reason
- **Task owner:** ItemTrace report processing system

## 1. Task Description

Convert a validated report into a consistent structure for comparing lost and found reports. A fixed model-supported operation normalizes wording and item categories while retaining the original values. It may clarify equivalent terms but must not invent color, brand, date, location, or other facts absent from the report. Uncertain interpretations remain marked as uncertain.

## 2. Inputs

### Input 1

- **Input name:** Validated report
- **Contents and format:** The original structured report, its unique report ID, and a `valid` validation result with no unresolved required-field errors.
- **Source:** T1: Validate Report

- **If a required input is missing or invalid:** Send the report to T7: Resolve Exception Case with the missing field or invalid validation status. Do not infer the missing value or continue to T3.

## 3. Outputs

### Output 1

- **Output name:** Standardized report
- **Contents and format:** Structured record with report ID, lost or found status, normalized item type, normalized description terms, location, date, original submitted values, and uncertainty flags. The report ID and original values remain unchanged.
- **Next task or recipient:** T3: Retrieve Candidate Reports. T4: Analyze Potential Matches also receives this record after T3 supplies candidates.
- **Complete when:** The normalized record is stored, required fields remain traceable to the original report, and unsupported or uncertain interpretations are marked rather than presented as facts.

## 4. Planned Tools

### Tool 1

- **Tool name:** `standardize_report`
- **Input:** Validated report
- **Output:** Standardized report
- **Implementation Route:** Web API calls
- **Integration approach:** Direct integration
- **Role in this task:** Apply one fixed model-supported standardization operation and return structured normalized fields with uncertainty flags. Validate the returned structure before saving it.
- **Task timeout:** 60 seconds per report.
- **Maximum retries:** 1 additional attempt.
- **Retry only when:** The service times out or returns a temporary error; wait 3 seconds before retrying. Do not retry a content conflict or unsupported interpretation. Save the result under the report ID so a retry cannot create duplicate standardized reports.
- **On timeout, exhausted retries, or an error that cannot be retried:** Record `standardization_error`, retain the original report and error details, and send the case to T7: Resolve Exception Case. Do not continue to T3.
