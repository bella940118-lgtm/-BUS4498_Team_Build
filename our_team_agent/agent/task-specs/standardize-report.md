# Standardize Report Task Specification

## Basic Information

- **Task ID:** T2
- **Task name:** Standardize Report
- **Task type:** Reason
- **Task owner:** ItemTrace report processing system

## 1. Task Description

Convert a validated report into a consistent structure using predefined formatting and item-category mapping rules. Normalize capitalization, spacing, and known category labels while preserving the original submitted values. If a value has no defined mapping, keep it unchanged and mark it as uncertain. Do not infer missing item attributes.

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
- **Implementation Route:** Functions/scripts
- **Integration approach:** Direct integration
- **Role in this task:** Apply predefined formatting and category-mapping rules to the validated report. Return structured standardized fields and uncertainty flags, then validate the result before saving it.
- **Task timeout:** 60 seconds per report.
- **Maximum retries:** 1 additional attempt.
- **Retry only when:** A temporary processing or storage error occurs; wait 3 seconds before retrying. Do not retry an unmapped or conflicting value automatically. Save the result under the report ID so a retry cannot create duplicate standardized reports.
- **On timeout, exhausted retries, or an error that cannot be retried:** Record `standardization_error`, retain the original report and error details, and send the case to T7: Resolve Exception Case. Do not continue to T3.
