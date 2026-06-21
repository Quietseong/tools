# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.

---

## 5. Skill Orchestration (multi-collection routing)

Four overlapping skill collections are installed: **OMC** (oh-my-claudecode), **Matt Pocock** (`~/.claude/skills`), **superpowers** (`/superpowers:` plugin, auto-activates via hooks), **gstack** (planning/review skills). They overlap heavily on the front end — **don't stack them; pick ONE per stage** using the rules below. Principle: **front-end (discover / spec / plan / govern) = external collections; back-end (execute / verify / clean / remember) = OMC.**

**Decided single sources of truth:**
- **GitHub Issues are the canonical work tracker.** Create them with Matt `to-issues`; OMC `ralph`/`team` consume those issues. Do NOT maintain a parallel `prd.json` as the source — derive it from the GitHub issue.
- Default planning tool: **superpowers `/superpowers:write-plan`**.
- Default execution backend: **OMC `team` / `ralph`**.

**Stage routing:**

| Stage | Default | When / alternative |
|---|---|---|
| Discover | gstack `office-hours` | product question ("should we build this, what's the wedge") |
|  | OMC `deep-interview` | technical requirements still vague |
|  | OMC `deep-dive` | root cause unknown (else Matt `diagnosing-bugs`) |
| Domain / decisions | Matt `domain-modeling` → CONTEXT.md + ADR | on any hard-to-reverse decision; `grill-with-docs` to interview + document together |
| Spec | Matt `to-prd` | synthesize conversation → PRD on GitHub |
| Plan | superpowers `/superpowers:write-plan` | OMC `plan --consensus` when you want multi-agent consensus |
| Plan governance | gstack `plan-eng-review` | feasibility/complexity gate; `plan-ceo-review` for scope expand/reduce |
| Decompose | Matt `to-issues` | vertical slices → GitHub issues (the single source of truth) |
| Execute | OMC `team` / `ralph` | superpowers `subagent-driven-development` for strict single-session per-task review; OMC `autopilot` for full idea→code |
| Implement discipline | Matt `tdd` / superpowers TDD + `codebase-design` | always test-first |
| Debug | Matt `diagnosing-bugs` | or OMC `tracer` / `debugger` / `deep-dive` |
| Review / QA | OMC `code-reviewer` + `security-reviewer` + `verify` + `ultraqa` | primary back-end |
| Cleanup | OMC `ai-slop-cleaner` + Matt `improve-codebase-architecture` | deslop, then deepen modules |
| Memory | Matt ADR + OMC `remember` / `skillify` | gstack `retro`/`learn` optional |

**Canonical feature loop:**
`office-hours`|`deep-interview` → `domain-modeling` (CONTEXT.md+ADR) → `to-prd` → `/superpowers:write-plan` → `plan-eng-review` (+`plan-ceo-review` if scope) → `to-issues` (GitHub) → **OMC `team`/`ralph` per issue** (TDD inside) → `diagnosing-bugs` if stuck → OMC `code-reviewer`+`security-reviewer`+`verify`+`ultraqa` → `ai-slop-cleaner`+`improve-codebase-architecture` → ADR/`remember`.

**Caveats:**
- gstack browser-dependent skills (`qa`, `review` browser parts, `browse`) are NOT installed — the gstack browser binary fails to build (Bun crash on Windows). Only `office-hours`, `plan-eng-review`, `plan-ceo-review` are available from gstack.
- superpowers skills auto-fire via plugin hooks; Matt/gstack skills are mostly user-invoked (`/<name>`).

<!-- OMC:IMPORT:START -->
@CLAUDE-omc.md
<!-- OMC:IMPORT:END -->
