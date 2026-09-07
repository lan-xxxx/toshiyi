# Product Agent Workflow

## Intake

1. Read the group feedback.
2. Classify it as bug, feature, question, PRD, or review.
3. Ask for missing blocking information only when needed.
4. Create or update a GitHub Issue.

## Labeling

- Type: `type/*`
- Priority: `priority/*`
- Status: `status/*`
- Module: `module/*`

## Review Loop

1. Draft PRD or answer.
2. Mark `status/in-review`.
3. If rejected, mark `status/need-info` or `status/prd-draft` and list rework items.
4. When accepted, mark `status/done` and notify the Octo group.

## Source-grounded Q&A

- Always cite file path and lines when answering code questions.
- If the source cannot verify a claim, say it is unverified.
