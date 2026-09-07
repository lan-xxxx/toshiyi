# Toshiyi / Octo-server Product Agent Pool

AINOL Agent exam product board for `octo-server`.

This repository is a writable product-management pool for collecting questions, bugs, feature requests, PRD drafts, review records, and status updates around the read-only upstream repository:

- Upstream source: https://github.com/Mininglamp-OSS/octo-server
- This repo: issue pool / PRD workflow / Agent operation records

## Goals

1. Triage feedback from Octo group chats into GitHub Issues.
2. Maintain labels for type, priority, status, and module.
3. Draft PRD or reproduction notes before review.
4. Track review comments, rework, and completion status.
5. Support scheduled scans and group progress notifications.
6. Keep source-code Q&A grounded with file paths and line numbers.

## Workflow

```text
Group feedback
  -> classify as bug / feature / question
  -> create or update GitHub Issue
  -> apply labels
  -> add PRD / reproduction / answer draft
  -> request review
  -> revise if rejected
  -> mark done and notify group
```

## Safety Rules

- Do not commit GitHub tokens, API keys, cookies, or private credentials.
- Do not modify the upstream `Mininglamp-OSS/octo-server` repository.
- Source-code answers must include verifiable file paths and line references.
- If evidence is missing, say so instead of inventing details.
