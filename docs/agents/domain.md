# Domain Documentation Configuration

## Layout

This project uses **single-context** layout:

- `CONTEXT.md` — domain language, key concepts, architecture overview (at repo root)
- `docs/adr/` — architecture decision records (at repo root)

## How skills use this

Skills like `improve-codebase-architecture`, `diagnose`, and `tdd` read `CONTEXT.md` to:
- Learn the project's domain vocabulary
- Understand key constraints and trade-offs
- Avoid suggesting architectures that violate documented decisions

## CONTEXT.md

The `CONTEXT.md` file should cover:

- **Domain model** — key entities, relationships, constraints
- **Architecture** — high-level structure, how pieces fit together
- **Key decisions** — why the project is organized this way
- **Vocabulary** — domain-specific terms used consistently in code and docs

This file is NOT a code walkthrough; it's about *understanding* the project.

## docs/adr/

Architecture Decision Records (ADRs) live in `docs/adr/NNNN-title.md` format:

- `docs/adr/0001-choice-of-framework.md`
- `docs/adr/0002-monorepo-vs-multirepo.md`
- etc.

Each ADR records a decision, its context, and consequences. Skills read these to understand past choices and avoid undoing them.

## Multi-context projects

If this project becomes a monorepo with separate frontend/backend/etc. contexts, convert to multi-context layout:

1. Create `CONTEXT-MAP.md` at the root
2. Move `CONTEXT.md` → `frontend/CONTEXT.md`, `backend/CONTEXT.md`, etc.
3. Move `docs/adr/` → `frontend/docs/adr/`, `backend/docs/adr/`, etc.
4. List all contexts in `CONTEXT-MAP.md`

(This can be done later; start with single-context.)
