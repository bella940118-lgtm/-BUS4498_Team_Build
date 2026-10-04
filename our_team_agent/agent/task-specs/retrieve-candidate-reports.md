# Retrieve Candidate Reports Task Specification

## Basic Information

- **Task ID:** T3
- **Task name:** Retrieve Candidate Reports
- **Task type:** Retrieve
- **Task owner:** ItemTrace report retrieval system

## 1. Task Description

Retrieve existing reports of the opposite type for comparison with the new report. Select reports with the same or a predefined compatible item type. Use available date and location information to order candidates; do not exclude a report solely because its date or location is missing. Record the retrieval criteria used for each candidate. This task does not decide whether a candidate is a match.

## 2. Inputs

### Input 1

- **Input name:** Standardized report
- **Contents and format:** Structured record with report ID, lost or found status, normalized item type and description terms, location, and date.
- **Source:** T2: Standardize Report

### Input 2

- **Input name:** Stored opposite-type reports
- **Contents and format:** Searchable report records with report IDs, report type, item type, description, location, date, and record status. Contact information is excluded from the result.
- **Source:** ItemTrace report database.

- **If a required input is missing or invalid:** Record the missing field or database access error and send the case to T7: Resolve Exception Case. Do not send an unverified empty result to T4.

## 3. Outputs

### Output 1

- **Output name:** Candidate report list
- **Contents and format:** Structured list containing the new report ID and each candidate's report ID, report type, item type, description, location, date, retrieval basis, and indicators for missing comparison fields. An empty list is valid only after a successful query. Include query status and result count.
- **Next task or recipient:** T4: Analyze Potential Matches.
- **Complete when:** The database query succeeds, returned records are opposite-type reports, and the list or verified empty result is available to T4.

## 4. Planned Tools

### Tool 1

- **Tool name:** `retrieve_candidate_reports`
- **Input:** Standardized report; Stored opposite-type reports
- **Output:** Candidate report list
- **Implementation Route:** Database queries
- **Integration approach:** Direct integration
- **Role in this task:** Query opposite-type reports with the same or a predefined compatible item type. Order candidates using available date and location information, and return report IDs, comparison fields, retrieval basis, and missing-field indicators without contact details or database changes.
- **Task timeout:** 30 seconds per report.
- **Maximum retries:** 1 additional attempt.
- **Retry only when:** A database connection or query times out temporarily; wait 2 seconds before retrying. An empty successful result is not an error and is not retried.
- **On timeout, exhausted retries, or an error that cannot be retried:** Record `retrieval_error` and the query status, then send the case to T7: Resolve Exception Case. Do not claim there are no candidate reports.
