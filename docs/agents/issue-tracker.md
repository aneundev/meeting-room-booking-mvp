# Issue Tracker Configuration

## Overview

This project uses **local markdown** for issue tracking. All issues, tasks, and PRD content live as markdown files in the `.scratch/` directory, checked into version control.

## Workflow

- **Create an issue:** Use `to-issues` skill to convert a plan or spec into `.scratch/<feature>/issue.md`
- **Triage:** Use `triage` skill to evaluate issues and add labels (stored as frontmatter)
- **Pick up work:** Agent skills read from `.scratch/` to find `ready-for-agent` items

## File structure

```
.scratch/
├── <feature-1>/
│   ├── issue.md          # Issue definition
│   └── subtasks.md       # Optional breakdown
├── <feature-2>/
│   └── issue.md
└── ...
```

## Benefits

- No external dependency on GitHub, GitLab, or Jira
- Full history in git
- Works for solo projects and teams
- Simple, human-readable format
