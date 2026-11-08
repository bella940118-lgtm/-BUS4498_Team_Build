# Hosting plan

Written in plain words with the BUS 4498 Agent Builder Kit. Our AI assistant turns it into a GitHub Pages website and a Supabase backend, following `runtime/hosting-rules.md`. Where this plan and our task specifications disagree, this plan wins.

## Our project

- GitHub repository: https://github.com/bella940118-lgtm/-BUS4498_Team_Build
- Our agent’s folder: `our_team_agent` (our plans are in `our_team_agent/runtime/`)
- Supabase project: https://vymksytzzgoxonfvvgzj.supabase.co
- Owner of the repository and the Supabase project: Bella Chu
- Teammate: Bella Chu, a collaborator on GitHub and a Developer in the Supabase organization
- We say “I’m working on it” and “pushed” in: Our team text message group chat. One of us works at a time.

## What our agent does

ItemTrace analyzes campus lost-and-found reports to identify potential matches between lost and found items. It standardizes report information, compares relevant details, routes potential matches for human verification, and notifies users only after approval.

## Our tasks

Use the tasks in our task specifications: `our_team_agent/agent/workflow-of-tasks.md` and `our_team_agent/agent/task-specs/`.

## Who can see or change what

Students can submit lost or found item reports and view their own report information and notifications. The ItemTrace system processes report details to identify potential matches but does not disclose other users' private contact information. Authorized human reviewers can access the relevant reports and matching evidence needed to approve, reject, or investigate potential matches. Only authorized system administrators can manage system settings, permissions, and stored records. Notifications are sent only after human approval.

## External tools and applications

| # | Tool or application | What the agent uses it for | What it does with it | Most sensitive data it touches | How it connects |
|---|---|---|---|---|---|
| 1 | Superbase | Stores and retrieves lost-and-found reports, candidate matches, human review decisions, and workflow records. | Read and write authorized records | Report details, user contact information, reviewer decisions |Supabase API with restricted credentials and row-level security|
| 2 | Email notification service | Sends approved potential-match notifications to the appropriate user and records delivery results. | Send messages and record delivery status | Recipient email address and approved match details |Authenticated email API; provider to be selected|

## Files people upload

Lost-and-found report details, including item descriptions and optional photos of reported items.
