# Token Estimator

**A vibe coding project cost estimator — not a token counter.**

Token Estimator predicts how many tokens your project will consume *before you start coding*. Unlike simple token counters that tally what you've already spent, this skill analyzes your project scope, your experience level, your toolchain, and the model you're using to give you a realistic budget range. It learns from every completed project, getting more accurate over time.

Built for vibe coding users who want to know: *"How much will this actually cost me?"*

---

## What it does

- **Estimates total token consumption for a project before you start** — with three ranges: optimistic, normal, and pessimistic
- **Infers your coding experience level from conversation history** — no questionnaire, no self-assessment
- **Tracks actuals vs. estimates across projects** — the more you use it, the more accurate it gets

---

## How it works

Token Estimator combines 5 coefficients (soon to be 7) into a single projection:

### User experience coefficient (L1–L4, auto-inferred)

The skill analyzes how you describe your project — vocabulary, structure, level of detail — and maps you to one of four levels. No need to self-rate. If the inference is wrong, you can override it with a single sentence.

| Level | Coefficient | Who this is |
|-------|-------------|-------------|
| L1 · Explorer | 3.0–4.0x | First time using AI coding tools, no technical vocabulary |
| L2 · Practitioner | 2.0–2.8x | Some experience, can describe features but not architecture |
| L3 · Proficient | 1.2–1.8x | Clear specs, uses task management, understands the tech |
| L4 · Expert | 0.8–1.2x | Precise specs, uses multi-agent workflows, manages context strictly |

### Context inflation coefficient

How you manage your conversation context directly affects token usage. A user who runs `/compact` regularly and splits work across sessions spends far fewer tokens than someone who treats one conversation as a bottomless scratchpad. The skill detects your patterns and applies a multiplier (1.0x for strict management, up to 3.5x for no management).

### Tool assistance coefficient (auto-detected)

The skill scans your environment and detects efficiency tools automatically. Each tool reduces the estimate:

| Tool detected | Coefficient |
|---------------|-------------|
| CLAUDE.md (well-structured) | 0.90x |
| Hermes Agent | 0.75x |
| everything-claude-code | 0.85x |
| .cursorrules | 0.90x |
| Custom harness (hooks/agents) | 0.80x |

Coefficients stack multiplicatively, with a floor of 0.55x.

### Model intelligence coefficient

Not all models are equally efficient. The skill uses public benchmark data (SWE-bench Verified, LiveCodeBench, Terminal-Bench) to assign each model a tier that affects the estimate:

| Tier | Coefficient | Examples |
|------|-------------|----------|
| S | 0.80x | Claude Opus 4.6, DeepSeek V4 Pro, GPT-5.4 |
| A | 0.90x | Claude Sonnet 4.6, Gemini 3.1 Pro, Kimi K2.5 |
| B | 1.00x | DeepSeek V3.2, DeepSeek R1, MiniMax M2.5 |
| C | 1.25x | DeepSeek V3, GLM-5, Grok 3 |
| D | 1.50x | Llama 4 Maverick, GPT-oss |

Reasoning models (like DeepSeek R1) get an additional +0.15 for thinking token overhead.

### Prompt completeness coefficient

This is the single biggest lever. A complete spec from a thinking partner (Claude.ai, ChatGPT) can reduce execution-side token consumption to **30%** of what a vague description would cost. The skill estimates where your prompt falls on this spectrum:

| Level | Coefficient | Example |
|-------|-------------|---------|
| Vague | 1.0x | "Build me a blog" |
| Feature list | 0.7x | "Blog with login, CRUD, tags" |
| Structured doc | 0.5x | Has data models, API design, page structure |
| Complete spec | 0.3x | Thinking partner output — ready to implement |

---

## Installation

### Claude Code

```bash
git clone https://github.com/huanghong346-bot/token-estimator.git ~/.claude/skills/estimate
```

### Cursor

```bash
git clone https://github.com/huanghong346-bot/token-estimator.git ~/.cursor/skills/estimate
```

### Other tools that support SKILL.md

```bash
git clone https://github.com/huanghong346-bot/token-estimator.git ./estimate
```

Then point your tool to the `SKILL.md` file. The install directory must be named `estimate` for the `/estimate` slash command to work.

---

## Usage

- **Trigger:** describe a new project, mention "token", "cost", "budget", or type `/estimate`
- The skill asks about your project, then outputs a 3-tier estimate panel with cost projections
- Adjust scope, correct your level, or compare models — then re-estimate
- Type "start" or "execute" to begin implementation
- At project end: say **"project complete"** to log actuals and calibrate future estimates

---

## Data & Privacy

- All data stored locally at `~/.token-estimator/`
- Nothing is uploaded anywhere — no telemetry, no cloud, no analytics
- Delete `~/.token-estimator/` at any time to reset completely

---

## Supported models (pricing reference)

Pricing data is stored in `references/pricing.json`. Currently includes:

- Claude Opus 4
- Claude Sonnet 4
- Claude Haiku 4
- DeepSeek V4 Pro (CNY pricing, promotional discount active until 2026-05-31)
- DeepSeek V3
- DeepSeek R1
- GPT-4o
- GPT-4o-mini
- Gemini 2.5 Pro

To add or update a model, edit `references/pricing.json`.

---

## Limitations

Token Estimator is opinionated about what it estimates well — and what it won't estimate at all. See [LIMITATIONS.md](./LIMITATIONS.md) for known boundaries, the Token Black Hole pattern, and accuracy expectations.

---

## License

MIT
