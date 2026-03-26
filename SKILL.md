---
name: harness-pro
description: |
  Set up harness engineering in any repository. Creates agent-first infrastructure:
  AGENTS.md progressive disclosure map, architectural layer boundaries with mechanical
  enforcement via tests and lint rules, testing framework, CI pipeline, golden principles
  with auto-generated .instructions.md files, garbage collection scripts, pre-commit hooks,
  and agent lifecycle hooks. Extends charlesanim/harness-engineering with Copilot-native
  customization files and expanded docs structure.
  Use when asked to "set up harness engineering," "make the repo agent-ready,"
  "add architectural boundaries," or "adopt agent-first engineering."
---

# Harness Engineering Setup

You are an expert in OpenAI's harness engineering methodology. Your goal is to
transform any repository into an agent-first engineering environment where AI
agents can independently navigate, build, test, and validate code within
mechanically enforced boundaries.

Based on charlesanim/harness-engineering (MIT), extended with agent hooks,
Copilot-native customization files, and expanded documentation structure.

## Background

Harness engineering (coined by OpenAI) shifts human engineers from writing code
to designing environments. The "harness" channels powerful but unruly agents to
run safely together. Key principles:

1. **Engineers become environment designers** — define constraints, not implementations
2. **Give agents a map, not an encyclopedia** — progressive disclosure via AGENTS.md
3. **If agents can't see it, it doesn't exist** — all knowledge in the repo
4. **Enforce architecture mechanically** — linters and tests, not markdown instructions
5. **Boring technology wins** — composable, stable, well-trained-on APIs
6. **Entropy management is garbage collection** — recurring cleanup agents
7. **Throughput changes merge philosophy** — minimal blocking gates
8. **Agent-to-agent code review** — humans escalated only for judgment calls

---

## Phase 0: Discovery

Before generating anything, you MUST understand the repository. Run these steps:

### 0a. Detect the Stack

Analyze the repo to determine:
- **Language(s):** TypeScript, Python, Go, Rust, Ruby, Java, etc.
- **Framework(s):** React, Next.js, Django, FastAPI, Rails, Spring, etc.
- **Package manager:** npm, pnpm, yarn, pip, poetry, cargo, go mod, bundler
- **Build tool:** Vite, Webpack, tsc, setuptools, cargo, make
- **Test runner (if any):** Vitest, Jest, pytest, go test, RSpec, JUnit
- **Linter (if any):** ESLint, Ruff, pylint, golangci-lint, RuboCop

Use the agent's file search and read tools to inspect: package.json, pyproject.toml,
Cargo.toml, go.mod, Gemfile, etc.

### 0b. Map the Architecture

Discover the existing structure:
- Directory tree (max 3 levels deep, exclude node_modules/.git)
- Existing docs or config (AGENTS.md, CLAUDE.md, .cursorrules, copilot-instructions.md)
- Existing CI (.github/workflows/)
- Existing tests (*.test.*, *.spec.*, test_*, *_test.*)
- Existing lint config (.eslintrc*, eslint.config*, pyproject.toml, etc.)

### 0b-1. Determine Project Maturity

Based on discovery, classify the project:

| Maturity | Signal | Strategy |
|----------|--------|----------|
| **Mature** | Has business code, routes/models/services with real logic | Discover patterns from code |
| **Scaffold** | Has framework skeleton (create-next-app, cargo init, etc.) but little/no business code | Preset from framework conventions |
| **Empty** | No package.json/pyproject.toml/go.mod, or just initialized | Ask user for intended stack, then preset |

Set `PROJECT_MATURITY` = mature / scaffold / empty. This determines the strategy
for all subsequent phases:
- **Mature** → "Read before write" — discover real patterns from existing code
- **Scaffold** → "Preset from conventions" — use framework best practices as starting point
- **Empty** → "Ask and scaffold" — ask user for stack choice, scaffold first, then preset

### 0c. Identify Layers

Every codebase has implicit architectural layers. Make them explicit.

**Web Frontend (React/Vue/Svelte/Angular):**
```
types/        → No app imports (pure type definitions)
utils/        → No app imports (pure functions)
lib/          → types/ only (clients, configs, core utilities)
services/     → lib/, types/ (business logic, API wrappers)
hooks/states/ → lib/, services/, types/ (state management)
components/   → hooks/, lib/, types/ (UI layer)
pages/routes/ → components/, hooks/, lib/, types/ (route entry points)
```

**Backend API (Express/FastAPI/Rails/Spring):**
```
types/models/ → No app imports (data definitions)
config/       → types/ only (configuration)
db/repo/      → config/, types/ (data access layer)
services/     → db/, config/, types/ (business logic)
middleware/   → services/, config/, types/ (request processing)
routes/       → services/, middleware/, types/ (HTTP handlers)
```

**Full-Stack (Next.js/Nuxt/SvelteKit):**
```
types/        → No app imports
lib/          → types/ only (shared utilities)
db/           → lib/, types/ (database layer)
services/     → db/, lib/, types/ (business logic)
components/   → lib/, types/ (UI primitives)
features/     → components/, services/, lib/, types/ (feature modules)
app/pages/    → features/, components/, lib/, types/ (routes)
```

**Monorepo (Turborepo/Nx/Lerna):**
```
packages/types/   → No internal imports
packages/config/  → types/
packages/db/      → config/, types/
packages/api/     → db/, config/, types/
packages/ui/      → types/ only
packages/web/     → ui/, api/, types/
```

Adapt the layers to match the ACTUAL directory structure. Don't force a structure
that doesn't fit. Read the import patterns in the codebase to discover the real
dependency graph.

### 0d. Ask Clarifying Questions

Before proceeding, confirm with the user:
- Which directories should be treated as which layers?
- Are there any special import relationships to preserve?
- What testing framework preference do they have?
- Do they want the full setup or specific phases?

**If scaffold/empty project, also ask:**
- What is the intended architecture? (monolith / modular / microservices)
- What are the first 2-3 features to build? (helps pre-populate exec-plans)
- Any existing design docs or PRD to reference? (import into docs/product-specs)

### 0e. Collect Foundational Knowledge (Scaffold/Empty Only)

New projects have no codebase to read. We need *some* input — but it can be
anything from a formal PRD to a one-sentence description in chat.

Ask the user:

> "Do you have any existing docs for this project? PRD, architecture notes,
> API specs, design docs — anything at all. Files, URLs, or just describe
> what you want to build."

Accept whatever they provide. Common inputs and how to use them:

| If they provide... | Place in | Extract |
|---------------------|----------|----------|
| PRD / requirements doc | `docs/product-specs/` | Feature list, acceptance criteria → DRAFT spec.yaml candidates |
| Technical architecture doc | `docs/architecture/` | Layers, tech stack, deployment model → seed LAYERS.md + AGENTS.md |
| Implementation plan / roadmap | `docs/exec-plans/active/` | Milestones → active plan files |
| API specs (OpenAPI/Swagger/etc.) | `docs/references/` | Endpoints → LLM-friendly *-llms.txt |
| Design docs / RFC | `docs/architecture/` | Design decisions, API contracts |
| Security requirements | `docs/security/` | Auth/access rules |
| UI/UX mockups or specs | `docs/product-specs/` | Page flow, component breakdown |
| **Just a verbal description** | `docs/architecture/OVERVIEW.md` | Capture it as-is — this is the starting knowledge base |

**Key principle: use what exists, skip what doesn't.**

- Got a PRD only? Great — extract features, skip security and API docs.
- Got nothing but an idea? Write OVERVIEW.md from the conversation and move on.
- Got a full doc suite? Process each into the appropriate directory.

Do NOT block progress waiting for "complete" documentation. Something is
always better than nothing. The docs/ structure will grow organically as the
project evolves.

---

## Phase 1: AGENTS.md — The Map

Create `AGENTS.md` at the repo root. This is the MOST important file. It must be:
- **~100 lines** (not an encyclopedia)
- **A table of contents** pointing to docs/ subdirectories
- **The entry point** for any agent working in the repo

### Template

```markdown
# {Project Name} — Agent Orientation Map

> {One-line description of what this project does.}

## Stack

| Layer | Tech |
|-------|------|
| {Language} | {version} |
| {Framework} | {version} |
| {Database} | {type} |
| {Other} | {details} |

## Architecture Layers

Dependency flows **downward only**. Never import upward.

{Generate the layer diagram from Phase 0c discovery}

## Key Conventions

- {Convention 1 — brief, with pointer to docs/golden-principles/ for details}
- {Convention 2}
- {Convention 3}

## Commands

\`\`\`sh
{build command}
{test command}
{lint command}
{dev command}
\`\`\`

## Documentation Map

\`\`\`
docs/
├── architecture/         Dependency graph, layer rules, tech design
├── guides/               Setup, testing, deployment how-tos
├── golden-principles/    Canonical patterns (DO/DON'T examples)
├── exec-plans/           Execution plans (active + completed)
├── product-specs/        PRD, feature specs, UI/UX specs
├── references/           External API docs (LLM-friendly format)
└── security/             Auth, access control, security policies
\`\`\`

## Where to Look First

| Task | Start here |
|------|-----------|
| {common task 1} | {directory/file} |
| {common task 2} | {directory/file} |
| {common task 3} | {directory/file} |
```

---

## Phase 2: Documentation Structure

### 2a. Create docs/

Create the documentation directory structure:

```bash
mkdir -p docs/architecture docs/guides docs/golden-principles
mkdir -p docs/exec-plans/active docs/exec-plans/completed
mkdir -p docs/product-specs docs/references docs/security
```

**For mature projects:** Move existing docs into appropriate subdirectories
(use `git mv` to preserve history).

**For scaffold/empty projects:** Process whatever was collected in Phase 0e.
Only generate what the input supports — skip anything that has no source material:

- **If PRD/requirements exist →** generate `docs/product-specs/{feature}.yaml`
  per major feature (status: DRAFT, user confirms before immutable)
- **If tech architecture doc exists →** generate `docs/architecture/OVERVIEW.md`
  (system context, tech stack, deployment model)
- **If implementation plan exists →** generate `docs/exec-plans/active/{phase}.md`
  per milestone
- **If API specs exist →** generate `docs/references/{service}-llms.txt`
- **If security docs exist →** generate `docs/security/AUTH.md` / `ACCESS.md`
- **If only verbal description →** `docs/architecture/OVERVIEW.md` is enough

Place original documents in their target directories alongside generated files.

Categories:
- `architecture/` — layer rules, dependency flow, system design, tech architecture
- `guides/` — local dev, testing, deployment, onboarding
- `golden-principles/` — canonical coding patterns
- `exec-plans/` — execution plans: `active/` for in-progress, `completed/` for done
- `product-specs/` — PRD, feature specs, UI/UX specs (source of truth for /writing-spec)
- `references/` — external API docs, third-party references (LLM-friendly *-llms.txt)
- `security/` — auth, access control, security policies
- `historical/` — completed work, migration notes (if applicable)

Only create subdirectories that make sense for the repo's scale.

### 2b. Create docs/architecture/LAYERS.md

This is the definitive reference for the layer hierarchy. Include:

1. **Layer diagram** — ASCII diagram with allowed dependency directions
2. **Hard rules** — violations that cause CI failure
3. **What each layer contains** — responsibility and key files
4. **Remediation guide** — for each common violation, explain how to fix it

Every error message in CI should point to this file.

### 2c. Create Golden Principles

Create 3-5 golden principles docs in `docs/golden-principles/`. Each should be
30-60 lines with DO and DON'T examples. Common candidates:

- **IMPORTS.md** — path aliases, import ordering, no deep relative imports
- **NAMING.md** — file naming, export conventions, variable naming
- **ERROR_HANDLING.md** — how to handle and report errors
- **LOGGING.md** — logging conventions (if a custom logger exists)
- **DATA_FETCHING.md** — how to fetch and cache data (frontend)
- **TESTING.md** — how to write tests, what to test, patterns to follow

**Strategy by maturity:**
- **Mature** → Read the actual codebase patterns. Don't guess — discover.
- **Scaffold/Empty** → Use framework community best practices as starting point.
  For example, Next.js projects get: IMPORTS.md (path aliases via @/), NAMING.md
  (pages/ kebab-case, components/ PascalCase), DATA_FETCHING.md (Server Components
  vs client fetch). Mark these as `status: PRESET` — the team should review and
  customize them as the codebase grows.

### 2d. Generate .instructions.md from Golden Principles

For each golden principle, generate a corresponding `.instructions.md` file
that Copilot will automatically inject into context when editing matching files.

Place them in `.github/instructions/` with `applyTo` glob patterns:

```markdown
---
name: 'Import Conventions'
description: 'Enforce import ordering and path alias rules'
applyTo: '**/*.{ts,tsx,js,jsx}'
---
{Content extracted from docs/golden-principles/IMPORTS.md — condensed to key rules}
```

This ensures golden principles are **mechanically enforced via agent context**,
not just documented in docs/ waiting to be discovered.

### 2e. Create exec-plans README

Create `docs/exec-plans/README.md`:

```markdown
# Execution Plans

Persistent record of development tasks across agent sessions.

## Active Plans
See `active/` — plans currently in progress.

## Completed Plans
See `completed/` — finished plans, kept for historical reference.

## Plan Template
Use `/writing-plan` to generate a plan from a confirmed spec.
```

### 2f. Create product-specs README

Create `docs/product-specs/README.md`:

```markdown
# Product Specifications

Feature specifications. Once CONFIRMED, specs are immutable.

Use `/writing-spec` to create a new spec interactively.
```

---

## Phase 3: Testing Infrastructure

### 3a. Choose the Right Test Runner

| Stack | Test Runner | Install |
|-------|------------|---------|
| Vite/React/Vue | Vitest | `npm i -D vitest @testing-library/react jsdom` |
| Next.js | Vitest or Jest | `npm i -D vitest @testing-library/react jsdom` |
| Node.js/Express | Vitest or Jest | `npm i -D vitest` |
| Python | pytest | `pip install pytest pytest-cov` |
| Go | go test (built-in) | No install needed |
| Rust | cargo test (built-in) | No install needed |
| Ruby/Rails | RSpec or Minitest | `gem install rspec` |

### 3b. Create Test Configuration

Set up the test runner with:
- Path alias resolution matching the main build config
- Coverage reporting
- Test file patterns
- Setup files for common test utilities

### 3c. Create Test Utilities

Create common test helpers:
- **Mock factories** — for database clients, API clients, auth contexts
- **Render helpers** — (frontend) wrap components with providers for testing
- **Fixture factories** — generate test data

### 3d. Create the Architecture Boundary Test

This is the MECHANICAL ENFORCEMENT — the most critical test. It:

1. Scans all source files in the project
2. Parses import/require statements
3. Determines which layer each file belongs to
4. Validates that imports respect the layer rules
5. Fails with descriptive, actionable error messages

**Key design decisions:**
- Use the language's file I/O to scan (node:fs, os.walk, filepath.Walk)
- Parse imports with regex (good enough — no need for AST)
- Report violations with: `"VIOLATION: {file} imports from {target} — {layer} cannot import from {target_layer}. {remediation}. See docs/architecture/LAYERS.md"`
- Maintain a `KNOWN_VIOLATIONS` list that acts as a ratchet — you can only remove entries, never add without review
- The test passes if all violations are in the known list and fails if new ones appear

**For scaffold/empty projects:** Still create the boundary test. It will pass
immediately (no imports to violate). The value is **preventive** — any new code
the agent writes will be checked from day one. This is the key advantage of
setting up harness engineering early: boundaries are enforced before bad patterns
have a chance to form.

### 3e. Write Example Tests

**Strategy by maturity:**
- **Mature** → Write 3-5 tests demonstrating patterns from existing code:
  a pure utility test, a config validation test, a component/handler test, the
  architecture boundary test.
- **Scaffold/Empty** → Write 2-3 seed tests as patterns for future code:
  a test runner smoke test (trivial assert to verify test infra works),
  the architecture boundary test (passes immediately, guards future code),
  and optionally a config validation test (e.g. env vars present).

---

## Phase 4: Linting & Boundary Enforcement

### 4a. Add Import Restriction Rules

Use the linter's native capabilities to enforce boundaries. Every error message
MUST include remediation instructions — the error output IS agent context.

**ESLint (JavaScript/TypeScript):**
Use `no-restricted-imports` with `patterns` in separate config objects per layer:
```javascript
{
  files: ['src/lib/**/*.{ts,tsx}'],
  rules: {
    'no-restricted-imports': ['error', {
      patterns: [{
        group: ['@/services/*', '@/hooks/*', '@/components/*', '@/pages/*'],
        message: 'lib/ is Layer 3 — cannot import from higher layers. See docs/architecture/LAYERS.md'
      }]
    }]
  }
}
```

**Ruff/pylint (Python):**
Use `banned-api` or custom checks via `[tool.ruff.lint.per-file-ignores]`.

**Go:**
Use `depguard` via golangci-lint.

**Rust:**
Use `clippy` restrictions or workspace dependency rules in Cargo.toml.

### 4b. Add Import Ordering (if applicable)

Enforce consistent import ordering:
- **ESLint:** `eslint-plugin-import` with `import/order`
- **Python:** `isort` via Ruff
- **Go:** `goimports` (built-in)
- **Rust:** `rustfmt` (built-in)

---

## Phase 5: CI/CD Pipeline

### 5a. Create CI Workflow

Create `.github/workflows/ci.yml` with parallel jobs:

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup {language}
      - run: {install_command}
      - run: {lint_command}

  typecheck:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup {language}
      - run: {install_command}
      - run: {typecheck_command}

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup {language}
      - run: {install_command}
      - run: {test_command}

  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup {language}
      - run: {install_command}
      - run: {build_command}
```

Adapt the jobs to the stack. Not every stack needs all 4 jobs. Python may not
need a separate `build` job. Go may combine `typecheck` into `build`.

---

## Phase 6: Garbage Collection

**For scaffold/empty projects:** Still set up GC scripts and the GitHub Action.
They won't find anything yet, but the infrastructure is ready. As the codebase
grows, GC starts catching drift automatically — no second setup needed.

### 6a. Create GC Check Scripts

Write simple scripts (in the repo's primary language or shell) that scan for
common violations of golden principles. Each script should:

1. Scan the source directory
2. Look for a specific anti-pattern
3. Report violations with file:line format
4. Exit 0 if clean, exit 1 if violations found

Common GC checks (pick the ones relevant to the stack):
- **Raw console/print statements** — should use a logger
- **Default exports** — should use named exports (JS/TS)
- **Inline magic numbers** — should use named constants
- **Large files** — files exceeding size limits (300 warn, 500 error)
- **TODO/FIXME/HACK comments** — track tech debt
- **Unused imports** — dead code detection
- **Missing type annotations** — (Python/TS)

### 6b. Create GC Runner

A single script that runs all GC checks and produces a summary:

```bash
npm run gc        # or
python scripts/gc_run_all.py  # or
make gc
```

### 6c. Create Scheduled GitHub Action

```yaml
name: Garbage Collection

on:
  schedule:
    - cron: '0 9 * * 1'  # Every Monday 9am UTC
  workflow_dispatch:

permissions:
  contents: read
  issues: write

jobs:
  gc-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup
        # Setup language
      - name: Run GC
        run: {gc_command} > gc-report.txt 2>&1 || true
      - name: Create or update issue
        if: always()
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const report = fs.readFileSync('gc-report.txt', 'utf8');
            const title = '🧹 Weekly Garbage Collection Report';
            const issues = await github.rest.issues.listForRepo({
              owner: context.repo.owner, repo: context.repo.repo,
              state: 'open', labels: 'garbage-collection', per_page: 1
            });
            const body = `## GC Scan — ${new Date().toISOString()}\n\n\`\`\`\n${report}\n\`\`\``;
            if (issues.data.length > 0) {
              await github.rest.issues.createComment({
                owner: context.repo.owner, repo: context.repo.repo,
                issue_number: issues.data[0].number, body
              });
            } else {
              await github.rest.issues.create({
                owner: context.repo.owner, repo: context.repo.repo,
                title, body, labels: ['garbage-collection']
              });
            }
```

---

## Phase 7: Pre-commit Hooks (Optional but Recommended)

For human developers committing code directly:

### JavaScript/TypeScript:
```bash
npm install -D husky lint-staged
npx husky init
# .husky/pre-commit: npx lint-staged
# package.json: "lint-staged": { "*.{ts,tsx}": ["eslint --fix"] }
```

### Python:
```bash
pip install pre-commit
# .pre-commit-config.yaml with ruff, mypy, pytest hooks
pre-commit install
```

### Go:
```bash
# Use golangci-lint as a pre-commit hook
# Or use the pre-commit framework with Go hooks
```

---

## Phase 8: Agent Hooks

Create agent lifecycle hooks for AI coding agents (Copilot, Claude Code, etc.).
These are different from git pre-commit hooks — they run during agent sessions.

### 8a. Stop Hook — Test Gate

Create `.github/hooks/test-gate.json`:

```json
{
  "hooks": {
    "Stop": [
      {
        "type": "command",
        "command": "bash scripts/test-gate.sh",
        "windows": "powershell -ExecutionPolicy Bypass -File scripts/test-gate.ps1",
        "timeout": 120
      }
    ]
  }
}
```

Create `scripts/test-gate.sh`:

```bash
#!/bin/bash
# Stop Hook: run full test suite before agent finishes
# exit 0 = pass, exit 2 = block and retry

output=$({test_command} 2>&1)
exit_code=$?

if [ $exit_code -eq 0 ]; then
  exit 0
fi

# Only show failures, not passing tests
echo "$output" | grep -E "(FAIL|Error|error|✕|✗|FAILED)"
echo ""
echo "Tests failed. Fix the errors above."
exit 2
```

Create `scripts/test-gate.ps1` (Windows equivalent):

```powershell
# Stop Hook: run full test suite before agent finishes
$output = & {test_command} 2>&1 | Out-String
$exitCode = $LASTEXITCODE

if ($exitCode -eq 0) { exit 0 }

$output -split "`n" | Where-Object { $_ -match '(FAIL|Error|error|FAILED)' }
Write-Output ""
Write-Output "Tests failed. Fix the errors above."
exit 2
```

Replace `{test_command}` with the actual test command detected in Phase 0.

### 8b. Why No PostToolUse Lint Hook

Modern AI agents (Copilot, Claude) automatically detect lint/type errors after
each edit and self-correct. Adding a PostToolUse lint hook adds latency to every
file edit with minimal value. The Stop hook is sufficient — it catches any
remaining issues before the agent declares completion.

---

## Execution Order

When the user says "set up harness engineering," execute in this order:

1. **Phase 0** — Discovery (ALWAYS do this first, NEVER skip)
2. **Phase 1** — AGENTS.md
3. **Phase 2** — Docs structure + LAYERS.md + golden principles + .instructions.md
4. **Phase 3** — Testing infrastructure + architecture boundary test
5. **Phase 4** — Linter boundary enforcement rules
6. **Phase 5** — CI pipeline
7. **Phase 6** — Garbage collection scripts + workflow
8. **Phase 7** — Pre-commit hooks (for human developers)
9. **Phase 8** — Agent hooks (for AI coding agents)

**Maturity-based adjustments:**

| Phase | Mature | Scaffold | Empty |
|-------|--------|----------|-------|
| Phase 0 | Discover from code | Detect framework | Ask user for stack |
| Phase 1 | Generate from real structure | Generate from framework conventions | Generate skeleton with intended structure |
| Phase 2 | Extract patterns from code | Preset from framework best practices | Preset minimal + ask user |
| Phase 3 | Tests with real examples | Seed tests + boundary test | Smoke test + boundary test |
| Phase 4 | Lint rules (warn on existing violations) | Lint rules (error from start — no legacy) | Lint rules (error from start) |
| Phase 5 | Full CI | Full CI | Full CI |
| Phase 6 | Full GC | Set up scripts (will find nothing yet) | Set up scripts |
| Phase 7 | Full hooks | Full hooks | Full hooks |
| Phase 8 | Full agent hooks | Full agent hooks | Full agent hooks |

**Key insight for new projects:** Phases 3-8 are actually MORE valuable when
set up early. Boundaries enforced from day one prevent architectural debt from
forming. Mature projects must deal with `KNOWN_VIOLATIONS` ratchets; new
projects start clean.

Ask the user before starting: "Should I set up all phases, or specific ones?"

Work on a feature branch (`feat/harness-engineering`) in a new git worktree if
the user requests it.

---

## Important Rules

1. **Never hardcode project-specific details.** This skill works for ANY repo.
   Discover the stack, don't assume it.
2. **Read before you write (mature projects).** Always read existing files before
   generating new ones. Match the repo's existing code style.
3. **Preset from conventions (new projects).** When there's no code to read, use
   framework community best practices. Mark presets as `status: PRESET` so the
   team knows to review them.
4. **Use git mv for doc restructuring.** Preserve git history.
5. **Every error message is agent context.** Remediation instructions go in the
   error output, not just in docs.
6. **The architecture test is a ratchet.** Known violations list can only shrink,
   never grow without explicit review.
7. **Don't break existing behavior.** New lint rules should warn, not error, if
   there are pre-existing violations across the codebase. New projects start with
   error level — no legacy to protect.
8. **Test your work.** Run the test suite, linter, and GC scripts after setup to
   verify everything works.
9. **Golden principles → .instructions.md.** Every golden principle doc should
   have a corresponding .instructions.md so agents get it injected automatically.
10. **New projects benefit most from early setup.** Enforcing boundaries on day
    one is cheaper than retrofitting them later. This is the strongest argument
    for running harness engineering on scaffold/empty projects.
