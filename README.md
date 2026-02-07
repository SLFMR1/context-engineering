# Context Engineering

An agent skill for managing AI context windows in multi-phase projects.

## The Problem

When AI agents work on complex, multi-phase projects, planning documents alone can consume
60-80% of available context before implementation begins. A 1,000-line architecture doc gets
loaded every session, leaving little room for actual work.

## What This Skill Does

Teaches agents to treat the context window as a scarce shared resource:

- **Split** large docs into phase-specific files (only load what's needed now)
- **Session state** file (~30 lines) for instant orientation on every session start
- **Compact plans** as checklists with links, not duplicated content
- **Self-check** protocol: 5 questions before every file read

## Real-World Results

In our first use — restructuring a 1,017-line architecture doc for a 6-phase project:

| Metric | Before | After |
|--------|--------|-------|
| Lines loaded to start Phase 1 | 1,017 | 340 (overview + phase doc) |
| Context reduction | — | 66% |
| Session orientation time | 4+ file reads (~16k tokens) | 1 read (~200 tokens) |
| Duplication across files | 6 repeated sections | 0 (single source of truth) |

## Install

```bash
npx skills add SLFMR1/context-engineering@context-engineering
```

## Three Modes

1. **Scaffold** — New project, no docs yet. Sets up folder structure before the plan grows.
2. **Restructure** — Existing docs exceed ~200 lines. Splits by phase, creates session state.
3. **Emergency** — Context running low. Immediately saves state for the next session.

## Safety

This skill **never deletes or modifies your existing files**. It only creates new files
alongside them. Your original plan/architecture doc remains untouched as a backup.

## When NOT to Use

- Routine session starts (just read START-HERE.md)
- Single-file tasks with no multi-phase structure
- Docs already split and session state exists
- Quick questions or research tasks

## Structure After Restructuring

```
docs/
  overview.md              # ~50 lines, always safe to load
  START-HERE.md         # ~30 lines, read first every session
  plan.md                  # ~40 lines, compact checklist with links
  phases/
    phase-1-[name].md      # Only loaded during Phase 1
    phase-2-[name].md      # Only loaded during Phase 2
  cross-cutting.md         # Shared concerns (testing, env vars)
```

## License

MIT
