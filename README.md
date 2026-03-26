# harness-pro

An enhanced agent skill based on [charlesanim/harness-engineering](https://github.com/charlesanim/harness-engineering) that sets up [OpenAI's harness engineering methodology](https://openai.com/index/harness-engineering/) in any repository — new or existing. Works with any AI coding agent: Copilot, Codex, Claude, Cursor, or others.

## What It Does

Transforms any repo into an agent-first engineering environment:

- **AGENTS.md** — ~100-line progressive disclosure map (not an encyclopedia)
- **Architecture layers** — mechanically enforced dependency boundaries
- **Golden principles** — canonical DO/DON'T patterns, auto-synced to `.instructions.md`
- **Testing infrastructure** — test runner, utilities, architecture boundary tests
- **CI pipeline** — lint, typecheck, test, build on every PR
- **Garbage collection** — scheduled scans for anti-pattern drift
- **Pre-commit hooks** — catch violations before they reach CI
- **Agent hooks** — test gate on Stop event, blocks incomplete work
- **Spec-TDD workflow** — `/writing-spec` → `/writing-plan` → `/make-something`

## What's New Over charlesanim

| Feature | charlesanim | harness-pro |
|---------|-------------|-------------|
| Phase 0-7 infrastructure | ✅ | ✅ (inherited) |
| New project support (empty repo) | ❌ | ✅ Maturity detection + foundational doc intake |
| Agent Hooks (Phase 8) | ❌ | ✅ Stop test-gate (bash + PowerShell) |
| Spec-Driven TDD workflow | ❌ | ✅ writing-spec → writing-plan → make-something |
| `.instructions.md` auto-generation | ❌ | ✅ From golden principles |
| `docs/` extended categories | basic | exec-plans, product-specs, references, security |
| Copilot prompt files | ❌ | ✅ `.prompt.md` for spec and plan writing |

## Supported Stacks

Works with any stack. The skill auto-detects and adapts:

| Stack | Test Runner | Linter |
|-------|------------|--------|
| React/Vue/Svelte (Vite) | Vitest | ESLint |
| Next.js/Nuxt | Vitest/Jest | ESLint |
| Node.js/Express | Vitest/Jest | ESLint |
| Python/Django/FastAPI | pytest | Ruff |
| Go | go test | golangci-lint |
| Rust | cargo test | clippy |
| Ruby/Rails | RSpec | RuboCop |

## Install

```bash
npx skills add 0xFivis/harness-pro
```

Or install globally (available in all repos):

```bash
npx skills add 0xFivis/harness-pro -g
```

## Usage

### Set Up Harness Engineering (Phase 0-8)

Once installed, just ask your agent:

> "Set up harness engineering in this repo"

The skill will walk through 9 phases: discovery → AGENTS.md → docs → testing → linting → CI → GC → pre-commit → agent hooks.

For **new projects** (empty or scaffold), the skill detects maturity and adapts — it can accept a PRD, architecture doc, or just a verbal description to get started.

### Develop Features (Spec-TDD Workflow)

After harness setup, use the three prompt files to build features:

**Step 1: Write a Spec**
```
/writing-spec
```
Interactive conversation → generates `docs/product-specs/{feature}.yaml`.
Once CONFIRMED, the spec is immutable — this is the contract.

**Step 2: Write a Plan**
```
/writing-plan
```
Reads the confirmed spec → generates `docs/exec-plans/active/{feature}.md`
with tasks mapped to requirements.

**Step 3: Build It**
```
/make-something
```
Reads spec + plan → writes tests (all red) → writes implementation (all green) → cleanup.
Tests are immutable — if a test is wrong, the spec is wrong.

## File Structure

```
harness-pro/
├── SKILL.md                              # Main skill: Phase 0-8 setup
├── README.md
├── skills/
│   └── make-something/SKILL.md           # Spec-Driven TDD implementation
├── prompts/
│   ├── writing-spec.prompt.md            # Interactive spec writing
│   └── writing-plan.prompt.md            # Plan generation from spec
└── instructions/
    └── spec-guard.instructions.md        # Auto-protects confirmed specs
```

## Compatibility

| Agent | Support |
|-------|---------|
| VS Code Copilot | ✅ Full (hooks, skills, prompts, instructions) |
| Claude Code | ✅ Full |
| GitHub Codex | ✅ Skill standard |
| Cursor | ✅ Basic |

## Credits

Core Phase 0-7 design from [charlesanim/harness-engineering](https://github.com/charlesanim/harness-engineering) (MIT).
Based on [OpenAI's harness engineering methodology](https://openai.com/index/harness-engineering/).

## License

MIT
