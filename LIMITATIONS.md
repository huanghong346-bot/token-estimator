# Limitations

Token Estimator is designed for typical vibe coding projects. This document explains what it handles well, what it handles poorly, and when you should not rely on it at all.

---

## What can be estimated reliably

The following work types produce useful estimates — typically within 2x of actual consumption. Type definitions match the `work_types` field in `references/base_tokens.json`.

| Work Type | Examples | Notes |
|-----------|----------|-------|
| Standard feature development | CRUD pages, auth systems, REST APIs, UI components | `modification_factor` applies — building on existing code costs less than building from scratch |
| Code modification / refactoring | Changing existing logic, structure, interfaces | `modification_factor` applies |
| Skill / Plugin development | SKILL.md, MCP servers, AI tool plugins | `modification_factor` applies |
| Research-based content creation | Documentation, benchmark data files, analysis reports | Always estimated as full creation cost. `modification_factor` does **not** apply because research work scales with scope, not with how much prior code exists |
| Mixed projects | Contains both development and research/content work | **Must be split by type and estimated separately, then summed.** Applying a single `modification_factor` to a mixed project produces misleading results — see our V2 calibration data for a real example |

---

## What cannot be estimated reliably

The following types can produce estimates that are off by 3x or more. These estimates are provided as rough reference only — do not budget against them.

- **Complex system architecture**: microservices, distributed systems, custom infrastructure. Unknowns compound quickly. A seemingly small change in one service can cascade across the system. Expect 3x+ variance from even the pessimistic estimate.
- **Debugging-heavy projects**: unknown bugs, dependency conflicts, environment issues. Each debug loop is its own mini-project with an unpredictable token cost. `modification_factor` does not apply.
- **Vague requirements / shifting direction**: if the goal changes mid-project, the estimate resets each time. `modification_factor` does not apply. The tool can estimate the *current* scope — it cannot predict how many times you'll change your mind.

---

## The Token Black Hole — when estimation breaks down entirely

Some combinations of factors create a scenario where no meaningful estimate is possible — and more importantly, where the project itself may be impossible to complete with vibe coding, regardless of how many tokens you spend.

### The black hole pattern — all five conditions present simultaneously:

1. **User experience level: L1** — no prior coding knowledge, first time using AI tools
2. **Project scope: complex system, or dominated by debugging / vague requirements** — the hardest project types
3. **Requirements: vague, not broken down into steps** — prompt completeness coefficient ≥ 0.7x
4. **No supporting tools** — no CLAUDE.md, no task management, no harness configuration
5. **No phase planning** — treating the entire project as one continuous conversation

When all five conditions are present, the estimator outputs a warning panel instead of a number. A token budget cannot fix a structural problem.

### What to do instead:

1. **Use a thinking partner to break the project into phases** (Claude.ai, ChatGPT, etc.). This is the single highest-leverage action you can take. A good spec from a thinking partner can cut execution-side token consumption by 70%.
2. **Create a CLAUDE.md with project rules** before writing any code. Even a simple one reduces ambiguity and backtracking.
3. **Start with a smaller scoped practice project** to build familiarity with vibe coding tools and workflows.
4. **Re-run `/estimate` after completing the above steps.** The estimate panel will reflect the improved conditions.

This pattern was identified from real experience: projects that consumed significant tokens without reaching a working state — not because the model was inadequate, but because the preconditions for successful vibe coding were not in place.

---

## Accuracy expectations

| Scenario | Expected accuracy |
|----------|------------------|
| Complete spec from thinking partner + experienced user (L3–L4) | Within 2x of actual |
| Structured requirements + intermediate user (L2) | Within 3–4x |
| Vague description + beginner user (L1) | 5x or more |
| Black hole pattern (all 5 conditions) | Not estimated |

**Estimates improve over time.** After 3 or more completed and logged projects, the skill calibrates its coefficients to your specific working style. The calibration uses a weighted average of your most recent 5 projects, with newer projects weighted more heavily.

---

## What this tool does not do

- **Does not count tokens in real time** — it estimates project totals before or during work, not per-message usage. Use your API dashboard for live monitoring.
- **Does not replace API dashboard monitoring during execution** — check your usage periodically, especially for long-running projects.
- **Does not account for tokens spent in thinking-partner sessions** — conversations on Claude.ai web, ChatGPT web, etc. are typically subscription-based and not billed per token. This tool only estimates execution-side consumption (Claude Code, Cursor, API, etc.).
- **Does not guarantee a project will be completable within the estimated range** — unexpected complexity, requirement changes, and model behavior can all cause drift.
- **Does not update model pricing automatically** — check `references/pricing.json` and update it manually when model prices change (recommended every 1–2 months).
