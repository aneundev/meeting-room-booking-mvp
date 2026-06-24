# Triage Labels Configuration

## Standard labels

This project uses the five canonical triage labels:

| Label | Meaning |
|-------|---------|
| `needs-triage` | Maintainer needs to evaluate the issue |
| `needs-info` | Waiting on reporter/user for more details |
| `ready-for-agent` | Fully specified; an agent can pick it up with no human context |
| `ready-for-human` | Needs human implementation; agent cannot do it alone |
| `wontfix` | Will not be actioned; closed/rejected |

## Implementation

Labels are stored in issue frontmatter as YAML:

```markdown
---
title: Fix login bug
labels:
  - needs-triage
---

Issue description...
```

## State machine

Issues flow through states:
1. Created with no label
2. → `needs-triage` (initial state)
3. → `needs-info` (if more details needed) or skip to 4
4. → `ready-for-agent` or `ready-for-human` (fully specified)
5. → `wontfix` (if rejected)
