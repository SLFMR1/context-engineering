---
name: context-engineering
description: >
  Context window management for AI-assisted projects. Invoke this skill when:
  (1) Starting a new multi-phase project — to scaffold docs structure before the plan grows,
  (2) Existing plan/architecture doc exceeds ~200 lines — to split and restructure,
  (3) Context running low mid-session — to preserve state for the next session.
  Do NOT invoke for routine session starts — read session-state.md directly instead.
---

# Context Engineering

The context window is shared between system prompts, memory files, conversation history,
tool results, and actual work. Without discipline, planning alone can consume 60-80% of
available context before implementation begins.

## Modes

### Scaffold Mode (new project, no docs yet)
Ask: "Do you want me to set up the folder structure now, or wait until you have a plan?"
- **Now** → create skeleton: overview.md, session-state.md, plan.md, phases/ folder.
  Content goes into the right place from the start — no monolith to split later.
- **Wait** → do nothing. Come back when docs exceed ~200 lines.

### Restructure Mode (existing project, docs too large)
Assess the existing docs. If any file exceeds ~200 lines, propose splitting using the
strategies and templates below. Move content to phase-specific files, create session
state, replace verbose plan with compact checklist.

### Emergency Mode (context running low)
Immediately write session-state.md with current state, exact stopping point, and next
step. User starts fresh session from state file.

## When NOT to Use This Skill

- Routine session starts (just read session-state.md directly)
- Single-file tasks with no multi-phase structure
- Docs are already split and session state exists
- Quick questions or research tasks

## Self-Check (run before every file read)

1. Do I need this file, or do I already know enough?
2. Do I need the whole file, or just a section? (grep/offset+limit first)
3. Is this for the CURRENT phase or a different one?
4. Will this still matter 5 turns from now?
5. Is there a smaller file (session-state, overview) that gives the same answer?

If 2+ answers say "skip" — skip it.

## Core Rules

### 1. Single Source of Truth
Every piece of knowledge in exactly ONE file. Architecture doc = detail.
Plan file = compact checklist with links. Phase docs = phase-specific only.
Cross-cutting doc = content referenced by 2+ phases.

**Placement rule:** 1 phase uses it → phase doc. 2+ phases use it → cross-cutting.md.

### 2. Progressive Loading
Phase 3 work never loads Phase 1 details. Split large docs by phase.
Read overview first (~50 lines), then only the current phase doc.

### 3. Session State File
~30-line file that orients any new session instantly. Updated every session end.
Contains: current position, last completed, next steps, what to read, what NOT to read.

### 4. Memory = Index
MEMORY.md points to where information lives. Never contains specs or schemas.
Every line costs tokens across the entire session (auto-loaded every turn).

### 5. Tame Tool Output
- offset+limit on file reads (don't read 1000 lines when you need 50)
- grep for the specific section before reading the whole file
- head_limit on search results
- IDE diagnostics cost 2-8k tokens — ignore cosmetic warnings

## Session Protocols

### Start
1. Read session-state.md (~30 lines) — instant orientation
2. MEMORY.md auto-loaded (keep it lean)
3. Read ONLY the current phase doc
4. Do NOT read full architecture doc, plan file, or completed phase docs

### Mid-Session
- Run the Self-Check before every file read
- If context feels heavy: update session-state.md preemptively

### End
1. Update session-state.md: what was done, what's next, blockers
2. Update MEMORY.md only for NEW learnings (not routine progress)

---

## Strategies

### When to Apply

| Situation | Strategy | Effort |
|-----------|----------|--------|
| New multi-phase project | Scaffold folder structure upfront | Low |
| Architecture doc >200 lines | Split by phase | Medium (one-time) |
| Plan file duplicates architecture | Replace with compact checklist | Low |
| New sessions take 10+ min to orient | Add session-state.md | Low |
| MEMORY.md >100 lines | Audit and trim to index-only | Low |

### Splitting Large Docs

```
BEFORE:
docs/architecture.md                    # 1000 lines, always fully loaded

AFTER:
docs/overview.md                        # ~50 lines, always safe to load
docs/session-state.md                   # ~30 lines, read first every session
docs/plan.md                            # ~40 lines, compact checklist
docs/phases/
  phase-1-[name].md                     # Only loaded during Phase 1
  phase-2-[name].md                     # Only loaded during Phase 2
docs/cross-cutting.md                   # Shared: testing, env vars, monitoring
```

### Tool Output Costs

| Tool output | Typical cost | Mitigation |
|-------------|-------------|------------|
| Full file read (1000 lines) | ~4k tokens | offset+limit, read needed section |
| IDE diagnostics (lint) | ~2-8k tokens | Ignore cosmetic warnings |
| Grep results (many matches) | ~2-5k tokens | head_limit, narrow glob |
| Large git diffs | ~3-10k tokens | Diff specific files, not entire repo |

---

## Templates

### Session State (~30 lines)

```markdown
# Session State — [Project]
Last updated: [date]

## Current Position
Phase: [N] — [Name] | Step: [description] | Branch: [name]

## Last Completed
- [What was finished]

## Next Steps
1. [Exact next action with file path]
2. [Following action]

## Read These Files
- docs/phases/phase-N-[name].md
- src/[file being worked on]

## Do NOT Read
- Full architecture doc
- Completed phase docs

## Blockers
[Any blockers, or "None"]

## Key Context
[1-2 critical facts for the next session]
```

### Compact Plan (~40 lines)

```markdown
# Plan — [Project]
Status: Phase [N] [status]
Overview: [link to overview.md]

## Phases
- [x] Phase 1: [Name] ([link to phase doc])
- [ ] Phase 2: [Name] ([link])  <-- CURRENT
  - [x] Step description
  - [ ] Step description
- [ ] Phase 3: [Name]

## Decisions
- [date]: [Decision + rationale]
```

### Phase Detail Document

```markdown
# Phase [N]: [Name]
Status: [Pending | In Progress | Complete]
Prerequisite: Phase [N-1]

## Scope
[2-3 sentences]

## Steps
[Numbered, continuing from previous phase]

---
[Implementation detail — as long as needed, only loaded for this phase]
---

## Files to Create/Modify
- path/to/file.ts — [what it does]

## Verification
- [How to verify this phase is complete]
```
