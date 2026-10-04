# Notify User Task Specification

## Basic Information

- **Task ID:** T6
- **Task name:** Notify User
- **Task type:** Act
- **Task owner:** ItemTrace notification system

## 1. Task Description

Send a potential-match notice to the recipient identified in an approved human review decision. The notice states that the match is possible and explains the next step without asserting confirmed ownership or exposing the other reporter's private contact details. Notification is permitted only after T5 records an explicit approval.

## 2. Inputs

### Input 1

- **Input name:** Approved match decision
- **Contents and format:** Structured T5 record with `approved` decision, new and candidate report IDs, reviewer identity, decision ID, and intended recipient report ID.
- **Source:** T5: Review Potential Match

### Input 2

- **Input name:** Recipient contact record
- **Contents and format:** Contact channel and destination associated with the approved recipient report ID, retrieved from ItemTrace's restricted report database at send time.
- **Source:** ItemTrace report database.

- **If a required input is missing or invalid:** Record the missing or invalid approval/contact field and send the case to T7: Resolve Exception Case. Do not send a message.

## 3. Outputs

### Output 1

- **Output name:** Notification result
- **Contents and format:** Structured record with decision ID, recipient report ID, channel, send status, timestamp, and provider receipt or error. The result does not contain the recipient's contact address in broadly visible logs.
- **Next task or recipient:** ItemTrace workflow record when delivery is confirmed; T7: Resolve Exception Case if delivery fails or remains uncertain.
- **Complete when:** The provider confirms one notification for the approved decision and the receipt is stored, or an unresolved delivery status has been handed to a person without claiming success.

## 4. Planned Tools

### Tool 1

- **Tool name:** `send_match_notification`
- **Input:** Approved match decision; Recipient contact record
- **Output:** Notification result
- **Implementation Route:** Web API calls
- **Integration approach:** Direct integration
- **Role in this task:** Verify approval and recipient identity, send the potential-match notice through the approved channel, and store a delivery receipt. Use the decision ID as an idempotency key to prevent duplicate notices.
- **Task timeout:** 60 seconds per approved decision.
- **Maximum retries:** 1 additional attempt.
- **Retry only when:** The provider explicitly confirms that the first attempt was not accepted and the error is temporary; wait 5 seconds before retrying with the same decision ID. If delivery status is unknown, do not retry automatically.
- **On timeout, exhausted retries, or an error that cannot be retried:** Record `notification_failed` or `delivery_unknown` with the available receipt and send the case to T7: Resolve Exception Case. Do not mark the workflow run as successfully notified.
