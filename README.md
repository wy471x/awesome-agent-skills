# Awesome Agent Skills

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

A curated, categorized catalog of AI coding agent skills — reusable prompt templates designed for agents like Claude Code, Cursor, GitHub Copilot, and Codex CLI. Each skill includes best practice guidance so you can create, adapt, and combine them with confidence.

## Contents

- [Introduction](#introduction)
- [Quick Start](#quick-start)
- [Best Practices](#best-practices)
  - [Skill Design Principles](#skill-design-principles)
  - [YAML Frontmatter](#yaml-frontmatter)
  - [Naming Conventions](#naming-conventions)
  - [Prompt Engineering](#prompt-engineering)
  - [Scope & Boundaries](#scope--boundaries)
- [Project Initialization & Scaffolding](#project-initialization--scaffolding)
- [Architecture & Design](#architecture--design)
- [Code Generation & Editing](#code-generation--editing)
- [API Development & Integration](#api-development--integration)
- [Frontend & UI Development](#frontend--ui-development)
- [Database Design & Management](#database-design--management)
- [Configuration & Environment](#configuration--environment)
- [Testing](#testing)
- [Code Review](#code-review)
- [Debugging & Troubleshooting](#debugging--troubleshooting)
- [Performance Analysis & Optimization](#performance-analysis--optimization)
- [Security Hardening](#security-hardening)
- [Compliance & Licensing](#compliance--licensing)
- [Git & Version Control](#git--version-control)
- [CI/CD & DevOps](#cicd--devops)
- [Documentation](#documentation)
- [Data Science & Machine Learning](#data-science--machine-learning)
- [Mobile Development](#mobile-development)
- [Automation & Scripting](#automation--scripting)
- [Logging, Monitoring & Observability](#logging-monitoring--observability)
- [Agent Platform Configuration](#agent-platform-configuration)
- [Contributing](#contributing)
- [License](#license)

## Introduction

AI coding agents (Claude Code, Cursor, GitHub Copilot, Codex CLI, etc.) can execute complex multi-step tasks when given well-structured prompts called **skills**. A skill is a reusable prompt template — often with YAML frontmatter — that tells the agent exactly what to do, which tools to use, and what "done" looks like.

This repository catalogs skills by category following the software development lifecycle. Each entry describes what the skill does, how to invoke it, and the best practices that make it effective. Use this list to:

- **Discover** skills you didn't know existed
- **Learn** what makes a skill well-designed by reading the best practice notes
- **Copy and adapt** skills for your own projects and agent platforms

## Quick Start

Skills live in agent-specific directories and are invoked through each platform's native mechanism:

| Agent | Skill Directory | Invocation |
|---|---|---|
| Claude Code | `.claude/commands/` | `/skill-name` |
| Cursor | `.cursor/skills/` | `@skill-name` in chat |
| GitHub Copilot | `.github/skills/` | Natural language or `gh skill install` |
| Codex CLI | `.codex/skills/` | Natural language |
| Gemini CLI | `.gemini/skills/` | Natural language |
| Windsurf | `.windsurf/skills/` | Auto-detected rules |
| Cline | `.clinerules/` | Auto-loaded context |
| Aider | `.aider.conf.yml` | `/skill-name` |

A minimal skill file looks like this:

```markdown
---
description: Review staged changes for correctness and style
allowed-tools: Bash(git:*), Read, Grep
argument-hint: [base-branch]
---

## Instructions
- Run `git diff <base-branch>...HEAD` to get all changes
- For each file, check: logic correctness, edge cases, naming, error handling
- Report issues grouped by severity (critical, warning, suggestion)
- Output in markdown with file paths and line numbers
```

---

## Best Practices

### Skill Design Principles

1. **Single responsibility** — Each skill does exactly one thing. If a description contains "and also", split it.
2. **Imperative mood** — Write instructions as direct commands: "Analyze the git log and generate a changelog." Not "You should analyze..."
3. **Define success criteria** — Tell the agent explicitly what "done" looks like. Without this, agents over-scope or under-deliver.
4. **Handle failure modes** — Include instructions for when things go wrong: "If tests fail, parse the output, fix each failure, then re-run."
5. **Restrict tools** — Use `allowed-tools` to limit the skill to only the tools it needs. This prevents overreach and improves safety.
6. **Be idempotent** — Running a skill twice should either produce the same result or be a safe no-op.

### YAML Frontmatter

```yaml
---
description: 40-60 char summary shown in /help output
allowed-tools: Bash(git:*), Read, Grep    # be specific with subcommands
argument-hint: [base-branch]              # use [optional] and <required>
model: sonnet                             # only set when the task demands it
---
```

| Field | Guidance |
|---|---|
| `description` | Keep under 60 characters. Self-explanatory. |
| `allowed-tools` | Be granular: `Bash(git:*)` not `Bash(*)`. List only what's needed. |
| `argument-hint` | Always provide. Use `[optional]` and `<required>` brackets. |
| `model` | Omit unless the task requires a specific model family. |
| `context: fork` | Use for long-running independent tasks that shouldn't block the main conversation. |

### Naming Conventions

- **kebab-case**: `review-pr`, `test-generate`, `db-migrate-create`
- **Thematic prefixes** for discoverability:
  - `init-` — project scaffolding
  - `review-` — code review variants
  - `test-` — test generation and analysis
  - `debug-` — debugging and troubleshooting
  - `generate-` — code/doc generation
  - `audit-` — compliance/security/quality audits
  - `deploy-` — deployment and release
  - `db-` — database operations
  - `setup-` — configuration and environment setup
- Avoid abbreviations that aren't universally understood
- Keep names under 30 characters

### Prompt Engineering

- **Show expected output format.** "Output in Keep a Changelog format" is clearer than "Output a changelog."
- **Reference project context.** Tell the agent to read existing configs, CLAUDE.md, or conventions before acting.
- **Number complex steps.** For tasks with 3+ phases, use numbered instructions so the agent tracks progress.
- **Specify file paths.** Use project-relative paths explicitly: "Write to `src/utils/validation.ts`."
- **Include examples.** For generation tasks, show a before/after or an example of the target output.

### Scope & Boundaries

- **Skills are on-demand; CLAUDE.md is always-on.** Project conventions and build commands belong in CLAUDE.md. Skills handle infrequent, structured tasks.
- **Don't duplicate.** If the same instruction appears in both a skill and CLAUDE.md, consolidate.
- **Write for portability.** Skills should work across Claude Code, Cursor, Copilot, and Codex CLI where possible. When an agent lacks a required capability, note it explicitly.

---

## Project Initialization & Scaffolding

Skills that bootstrap new projects with best-practice structure, tooling, and conventions.

### `init-project`

Generate a complete project skeleton from a stack description — directory structure, config files, tooling setup, and CI template.

**Usage:** `/init-project <stack-description>`

**Best practices:**
- Always scaffold a `.gitignore` and `README.md` alongside source files
- Use the latest stable versions of tools and frameworks
- Generate CI config (GitHub Actions) as part of the scaffold

### `init-python`

Scaffold a Python project with `pyproject.toml`, `src` layout, pytest, Ruff, and pre-commit configuration.

**Usage:** `/init-python [project-name]`

**Best practices:**
- Use `src` layout to prevent accidental imports of the package during development
- Pin minimum Python version in `pyproject.toml` `requires-python`
- Include `pre-commit-config.yaml` with Ruff, mypy, and trailing-whitespace hooks

### `init-node`

Scaffold a Node.js/TypeScript project with `package.json`, `tsconfig.json`, ESLint, Prettier, and Vitest.

**Usage:** `/init-node [project-name]`

**Best practices:**
- Use ES modules (`"type": "module"`) by default
- Configure `tsconfig.json` with `strict: true`
- Include `lint-staged` + Husky for pre-commit quality checks

### `init-react`

Initialize a React project with routing, state management, component directory structure, and testing setup.

**Usage:** `/init-react [project-name]`

**Best practices:**
- Scaffold a `components/`, `hooks/`, `pages/`, and `lib/` directory structure
- Include React Router and a state management choice (Context + useReducer or Zustand)
- Set up Vitest + React Testing Library

### `init-monorepo`

Initialize a monorepo with Turborepo/Nx/pnpm workspaces, shared config packages, and consistent tooling across packages.

**Usage:** `/init-monorepo <strategy>`

**Best practices:**
- Use a shared config package for ESLint, TypeScript, and Prettier
- Configure build caching and parallel execution
- Add a root-level `CONTRIBUTING.md` and `DEVELOPMENT.md`

### `init-rust`

Scaffold a Rust project with Cargo workspace, clippy, rustfmt, and CI templates.

**Usage:** `/init-rust [project-name]`

### `gitignore-generator`

Generate a `.gitignore` tailored to the project's technology stack and target OS/editor.

**Usage:** `/gitignore-generator <stack>`

**Best practices:**
- Always include OS files (`.DS_Store`, `Thumbs.db`) and editor directories (`.vscode/`, `.idea/`)
- Add environment files (`.env*`) unless explicitly requested otherwise

### `editorconfig-setup`

Generate `.editorconfig` with consistent indentation, charset, and line-ending rules.

**Usage:** `/editorconfig-setup`

---

## Architecture & Design

Skills for creating architecture decision records, system designs, and technical specifications.

### `adr-create`

Draft an Architecture Decision Record following the standard format: title, status, context, decision, consequences.

**Usage:** `/adr-create <decision-title>`

**Best practices:**
- Store ADRs in `docs/adr/` with sequential numbering (`0001-title.md`)
- Include both positive and negative consequences
- Cross-reference related ADRs in the context section

### `system-design-doc`

Generate a system design document with architecture overview, data flow, component diagram, and technology rationale.

**Usage:** `/system-design-doc <feature-description>`

**Best practices:**
- Use C4 model levels (Context, Container, Component) for the architecture description
- Include non-functional requirements (performance, security, scalability)
- Document trade-offs explicitly — every architecture decision has costs

### `api-spec-design`

Design an OpenAPI 3.x specification from natural-language feature descriptions.

**Usage:** `/api-spec-design <feature-description>`

**Best practices:**
- Start with the request/response schemas before defining paths
- Include error response schemas (400, 401, 404, 500) from the start
- Use `$ref` for reusable schemas to keep the spec DRY

### `data-model-design`

Design entity-relationship diagrams, schema definitions, and data flow documentation.

**Usage:** `/data-model-design <domain-description>`

**Best practices:**
- Define relationships and cardinality before adding columns
- Document indexing strategy alongside the schema
- Include data retention and archival considerations

### `error-strategy-design`

Design an error handling and resilience strategy including retries, fallbacks, circuit breakers, and error boundaries.

**Usage:** `/error-strategy-design <service-context>`

### `migration-plan`

Create a phased migration plan for upgrading libraries, frameworks, or refactoring large code sections.

**Usage:** `/migration-plan <target-state>`

**Best practices:**
- Break the migration into reversible steps
- Include rollback procedures for each phase
- Estimate risk and impact per step

### `tech-debt-catalog`

Catalog existing technical debt, prioritize by impact/effort, and suggest remediation plans.

**Usage:** `/tech-debt-catalog [scope]`

---

## Code Generation & Editing

Skills for generating, refactoring, and transforming code across languages and patterns.

### `generate-function`

Generate a well-typed function from a natural-language specification with input/output contracts.

**Usage:** `/generate-function <specification>`

**Best practices:**
- Always include type annotations and return type
- Generate parameter validation for public API boundaries
- Include docstring with parameter descriptions and examples

### `generate-mock`

Generate mock objects, test doubles, or fixture data from interface/type definitions.

**Usage:** `/generate-mock <type-or-interface>`

**Best practices:**
- Generate realistic (not random) test data — use libraries like Faker
- Cover edge cases: null, empty, boundary values, and error states
- Don't mock what you don't own; use fakes or in-memory adapters for external dependencies

### `refactor-extract`

Extract a block of code into a well-named function, module, or class.

**Usage:** `/refactor-extract <code-block>`

**Best practices:**
- Preserve existing behavior exactly — verify with existing tests before and after
- Choose a name that describes WHAT the function does, not HOW
- Keep the extracted unit's scope small (prefer many small functions over one large one)

### `refactor-rename`

Rename a symbol across the project, updating all references without breakage.

**Usage:** `/refactor-rename <old-name> <new-name>`

**Best practices:**
- Use the language server's rename capability when available (more reliable than regex)
- Update string literals and comments only when they refer to the symbol
- Run the full test suite after the rename

### `refactor-optimize`

Restructure code for readability and performance without changing external behavior.

**Usage:** `/refactor-optimize <target-file-or-function>`

### `codemod-generator`

Generate a codemod script (jscodeshift, ast-grep, comby) for automated large-scale code transformations.

**Usage:** `/codemod-generator <transformation-description>`

### `boilerplate-reducer`

Analyze a codebase for repeated patterns and generate abstractions to eliminate boilerplate.

**Usage:** `/boilerplate-reducer [directory]`

**Best practices:**
- Only abstract patterns that appear 3+ times
- Verify that the abstraction actually reduces total lines of code
- Keep abstractions narrow — a bad abstraction is worse than duplication

### `type-generator`

Generate TypeScript/Python type stubs from runtime code, JSON samples, or database schemas.

**Usage:** `/type-generator <source>`

---

## API Development & Integration

Skills for designing, building, and consuming APIs across REST, GraphQL, and gRPC.

### `create-rest-endpoint`

Generate a REST endpoint with request validation, error handling, and response formatting.

**Usage:** `/create-rest-endpoint <method> <path> <description>`

**Best practices:**
- Validate input at the boundary before any business logic runs
- Return consistent error shapes (`{ error: string, details?: ... }`)
- Include appropriate HTTP status codes for all outcomes

### `create-graphql-resolver`

Generate a GraphQL resolver with data fetching, authorization, and error handling.

**Usage:** `/create-graphql-resolver <type> <field>`

**Best practices:**
- Use DataLoader for batching to avoid N+1 queries
- Handle authorization at the field level, not just the top-level resolver
- Return GraphQL errors (not throw) for user-facing validation failures

### `api-client-generate`

Generate a typed API client from an OpenAPI spec or GraphQL schema.

**Usage:** `/api-client-generate <spec-url-or-file>`

**Best practices:**
- Use code generation tools (openapi-generator, graphql-codegen) rather than hand-writing clients
- Generate error types so callers can handle failures precisely
- Include request/response interceptors for auth, logging, and retry logic

### `webhook-handler`

Generate a webhook endpoint with signature verification, idempotency, and retry handling.

**Usage:** `/webhook-handler <provider-name>`

**Best practices:**
- Always verify webhook signatures before processing the payload
- Use idempotency keys to handle duplicate deliveries
- Return 200 quickly; process the event asynchronously for long-running handlers

### `api-error-handling`

Audit and standardize error responses across all API endpoints.

**Usage:** `/api-error-handling`

**Best practices:**
- Define a project-wide error shape and use it everywhere
- Map internal exceptions to appropriate HTTP status codes at a single layer
- Never leak stack traces or internal details in production error responses

### `rate-limiting`

Add rate limiting to API endpoints with appropriate thresholds, response headers, and user feedback.

**Usage:** `/rate-limiting <endpoint>`

**Best practices:**
- Use token bucket or sliding window algorithms for accuracy
- Include `X-RateLimit-*` headers in responses
- Return 429 with a `Retry-After` header

### `api-versioning`

Implement API versioning strategy (URL path, header, content negotiation) for existing endpoints.

**Usage:** `/api-versioning <strategy>`

---

## Frontend & UI Development

Skills for generating and auditing UI components, styling, and accessibility.

### `generate-component`

Generate a React/Vue/Svelte component from a description of its props, behavior, and appearance.

**Usage:** `/generate-component <description>`

**Best practices:**
- Generate a component that handles loading, empty, error, and success states
- Include TypeScript prop types or interface definitions
- Add accessibility attributes (ARIA roles, labels, keyboard handlers) from the start

### `component-audit`

Audit a frontend component for accessibility, performance, reusability, and type safety.

**Usage:** `/component-audit <component-file>`

**Best practices:**
- Check keyboard navigation, screen reader support, and color contrast
- Verify that the component doesn't re-render unnecessarily
- Flag props that could be split into separate, focused components

### `a11y-audit`

Audit a page or component tree for WCAG 2.1 AA compliance.

**Usage:** `/a11y-audit [file-or-directory]`

**Best practices:**
- Check semantic HTML, heading hierarchy, and landmark regions
- Verify all interactive elements are keyboard-accessible
- Ensure `alt` text is meaningful (not just present)

### `form-generator`

Generate an accessible form with validation, error states, loading indicators, and submission handling.

**Usage:** `/form-generator <field-descriptions>`

**Best practices:**
- Associate labels with inputs using `htmlFor`/`for` or wrapping
- Show validation errors adjacent to the relevant field with `aria-describedby`
- Disable the submit button during submission to prevent double-submit

### `state-management`

Design and implement state management (Context, Redux, Zustand, Pinia) for a feature.

**Usage:** `/state-management <feature-description>`

**Best practices:**
- Start with the simplest solution (useState/Context); add libraries only when the pain is real
- Separate server state (React Query/SWR) from client state (Zustand/Redux)
- Keep state as close to where it's used as possible

### `storybook-generate`

Generate Storybook stories covering all component states (loading, empty, error, edge cases).

**Usage:** `/storybook-generate <component-file>`

### `tailwind-generate`

Generate Tailwind CSS classes and component styles following project design tokens.

**Usage:** `/tailwind-generate <visual-description>`

### `responsive-review`

Review and fix responsive layout issues across breakpoints.

**Usage:** `/responsive-review [page-or-component]`

### `animation-setup`

Add animations and transitions following accessibility `prefers-reduced-motion` preferences.

**Usage:** `/animation-setup <element-description>`

---

## Database Design & Management

Skills for schema design, migrations, query optimization, and data operations.

### `schema-design`

Design a database schema from entity descriptions — types, constraints, indexes, and relationships.

**Usage:** `/schema-design <domain-description>`

**Best practices:**
- Normalize to 3NF by default; denormalize only with a measured performance reason
- Add foreign key constraints and check constraints at the database level
- Document the indexing strategy: which queries each index supports

### `migration-create`

Generate a database migration script with forward and rollback logic.

**Usage:** `/migration-create <change-description>`

**Best practices:**
- Always include a reversible `down` migration
- For adding NOT NULL columns, provide a default or backfill strategy
- Test migrations against a copy of production data before deploying

### `query-optimize`

Analyze a slow query and suggest improvements: indexes, query rewriting, pagination, or caching.

**Usage:** `/query-optimize <query-or-file>`

**Best practices:**
- Run `EXPLAIN`/`EXPLAIN ANALYZE` and read the query plan before suggesting changes
- Consider covering indexes for frequently accessed column combinations
- Add pagination (cursor-based, not offset-based) for unbounded result sets

### `seed-generator`

Generate realistic seed data scripts for development and testing environments.

**Usage:** `/seed-generator <schema>`

**Best practices:**
- Use deterministic seeds (PRNG with fixed seed) for reproducibility
- Generate data that exercises edge cases, not just happy paths
- Keep seed scripts idempotent (use `UPSERT` or `INSERT ... ON CONFLICT`)

### `index-audit`

Audit existing indexes for redundancy, missing coverage, and write-performance impact.

**Usage:** `/index-audit`

### `n-plus-one-detector`

Scan codebase for N+1 query patterns and suggest eager loading or batch strategies.

**Usage:** `/n-plus-one-detector [directory]`

### `data-migration-plan`

Plan a data backfill or transformation migration for production data.

**Usage:** `/data-migration-plan <transformation-description>`

**Best practices:**
- Process data in batches to avoid long-running transactions
- Include a dry-run mode and validation queries to verify correctness
- Schedule during low-traffic windows and have a rollback plan

### `orm-usage`

Generate ORM models and queries (Prisma, Drizzle, SQLAlchemy, TypeORM) following project patterns.

**Usage:** `/orm-usage <orm-name> <schema-description>`

---

## Configuration & Environment

Skills for managing environment variables, tool configuration, and secrets.

### `env-setup`

Generate a `.env.example` file with all required variables, types, defaults, and documentation.

**Usage:** `/env-setup`

**Best practices:**
- Document each variable: what it does, valid values, and where to obtain it
- Mark variables as required or optional with a clear convention
- Never include real secrets or default credentials

### `env-validate`

Validate that the current environment matches what the codebase expects — report missing, unused, and mistyped variables.

**Usage:** `/env-validate`

### `config-sync`

Synchronize configuration across tools (ESLint ↔ Prettier ↔ TypeScript ↔ EditorConfig).

**Usage:** `/config-sync`

**Best practices:**
- Resolve conflicts between tools by choosing the stricter option
- Verify that editor integrations (VS Code settings) match CLI configurations

### `toolconfig-generate`

Generate tool configuration files (`eslint.config.js`, `tsconfig.json`, `pyproject.toml`, `Cargo.toml`) for a project.

**Usage:** `/toolconfig-generate <tool-name>`

### `secrets-audit`

Scan the codebase for accidentally committed secrets, API keys, tokens, and credentials.

**Usage:** `/secrets-audit [directory]`

**Best practices:**
- Use tools like `gitleaks`, `trufflehog`, or `detect-secrets` for automated scanning
- Check git history, not just the current working tree
- Treat every finding as a compromised secret — rotate it immediately

---

## Testing

Skills for generating, auditing, and fixing tests across all levels of the testing pyramid.

### `test-generate`

Generate unit tests for a function — cover edge cases, error paths, and happy paths.

**Usage:** `/test-generate <function-or-file>`

**Best practices:**
- Structure tests as Arrange-Act-Assert (AAA)
- Test behavior, not implementation — refactoring shouldn't break tests
- One assertion per test (logically), one concept per test case

### `test-component`

Generate component tests (React Testing Library, Vue Test Utils) covering rendering, interactions, and accessibility.

**Usage:** `/test-component <component-file>`

**Best practices:**
- Test from the user's perspective: find by label/role/text, not by class or test ID
- Verify that ARIA attributes reflect the current state
- Test keyboard interactions alongside mouse interactions

### `test-e2e`

Generate end-to-end tests (Playwright, Cypress) for critical user flows.

**Usage:** `/test-e2e <user-flow-description>`

**Best practices:**
- Test the highest-value flows first (signup, login, checkout, core CRUD)
- Use data attributes (`data-testid`) only when semantic selectors aren't viable
- Add explicit waits for network idle, not arbitrary timeouts

### `test-integration`

Generate integration tests for API endpoints, database interactions, and service contracts.

**Usage:** `/test-integration <endpoint-or-service>`

**Best practices:**
- Test against real dependencies (real database, real message broker) — not mocks
- Clean up test data in `afterEach`/`afterAll` to keep tests independent
- Use contract testing (Pact) for cross-service boundaries

### `test-coverage-audit`

Audit test coverage, identify untested code paths, and suggest specific tests.

**Usage:** `/test-coverage-audit [threshold]`

**Best practices:**
- Focus on branch coverage over line coverage — lines can be covered but untested
- Don't chase 100% coverage; target coverage on business logic, not getters/setters

### `test-fix-flaky`

Diagnose and fix flaky tests — timing issues, shared state, ordering dependencies, external stubs.

**Usage:** `/test-fix-flaky <test-file>`

**Best practices:**
- Run the suspicious test in a loop (50–100x) to confirm the fix
- Common root causes: shared mutable state, unordered async operations, implicit time dependencies
- Use fake timers instead of real time wherever possible

### `test-mock-strategy`

Design a mocking strategy: which layers to mock, which to test in full, and what fixtures to use.

**Usage:** `/test-mock-strategy <service-boundary>`

### `property-based-test`

Generate property-based tests (fast-check, Hypothesis, QuickCheck) from type definitions and invariants.

**Usage:** `/property-based-test <function-signature>`

### `fuzz-generate`

Generate a fuzz testing harness for input-parsing functions.

**Usage:** `/fuzz-generate <function>`

---

## Code Review

Skills for reviewing code changes with different focuses — correctness, security, performance, style.

### `review-pr`

Review a pull request for correctness, style, performance, and adherence to project conventions.

**Usage:** `/review-pr <pr-url-or-number>`

**Best practices:**
- Start with the big picture (architecture, approach) before nitpicking syntax
- Be specific: reference file paths and line numbers for every finding
- Distinguish blocking issues from suggestions and praise

### `review-security`

Security-focused code review covering OWASP Top 10, input validation, auth, and data exposure.

**Usage:** `/review-security [pr-url-or-branch]`

**Best practices:**
- Focus on the trust boundary: every place user input enters the system
- Check authN and authZ on every endpoint, not just the obvious ones
- Flag any use of dangerous functions (`eval`, `exec`, raw SQL concatenation)

### `review-performance`

Performance-focused review identifying bottlenecks, unnecessary allocations, and optimization opportunities.

**Usage:** `/review-performance [pr-url-or-branch]`

### `review-diff`

Review uncommitted changes (working tree diff) for issues before committing.

**Usage:** `/review-diff`

**Best practices:**
- Check for leftover debug code, console.log, commented-out blocks
- Verify no unintended file changes are included
- Confirm all new code is covered by tests

### `review-dependencies`

Review dependency additions/updates for licensing, vulnerabilities, bundle size, and necessity.

**Usage:** `/review-dependencies`

### `review-migration`

Review database migration scripts for safety, reversibility, and performance on large tables.

**Usage:** `/review-migration <migration-file>`

### `review-accessibility`

Code review focused on accessibility — ARIA, keyboard navigation, semantic HTML, color contrast.

**Usage:** `/review-accessibility [file-or-directory]`

### `review-api-change`

Review API changes for backward compatibility, documentation accuracy, and breaking change detection.

**Usage:** `/review-api-change [pr-url]`

---

## Debugging & Troubleshooting

Skills for analyzing errors, reading logs, reproducing bugs, and finding root causes.

### `debug-error`

Analyze an error message or stack trace, identify root cause, and suggest a fix.

**Usage:** `/debug-error <error-output>`

**Best practices:**
- Read the stack trace bottom-up — the root cause is often deeper than the top frame
- Check for recent changes (git log, last deploy) as potential triggers
- Reproduce the issue before attempting a fix

### `debug-log-analysis`

Analyze log files to identify patterns, anomalies, and root causes.

**Usage:** `/debug-log-analysis <log-file>`

**Best practices:**
- Look for spikes in error frequency and correlation with deployments or config changes
- Group similar errors to distinguish systemic issues from one-off failures
- Identify the first occurrence — the initial error often reveals the root cause

### `debug-reproduce`

Create a minimal reproduction script for a reported bug.

**Usage:** `/debug-reproduce <bug-description>`

**Best practices:**
- Strip away everything not needed to trigger the bug
- The repro should be self-contained and runnable with a single command
- A good repro either passes or fails — no "sometimes" behavior

### `debug-performance-regression`

Debug a performance regression by comparing profiles, traces, or benchmarks before/after.

**Usage:** `/debug-performance-regression <commit-range>`

### `debug-network`

Analyze network request/response logs to debug API issues, latency, or data corruption.

**Usage:** `/debug-network <har-file-or-curl-output>`

### `debug-memory`

Debug memory leaks or excessive usage using heap snapshots, allocation profiles, or Valgrind.

**Usage:** `/debug-memory <target-process>`

### `debug-concurrent`

Debug race conditions, deadlocks, or data races in concurrent/parallel code.

**Usage:** `/debug-concurrent <symptom-description>`

**Best practices:**
- Use thread sanitizers (`-fsanitize=thread`, `RUSTFLAGS="-Z sanitizer=thread"`) before manual analysis
- Check for shared mutable state without synchronization
- Look for lock ordering violations when deadlocks are suspected

### `debug-crash`

Debug a crash dump or core dump, identifying the failing code path and memory state.

**Usage:** `/debug-crash <dump-file>`

---

## Performance Analysis & Optimization

Skills for profiling, measuring, and improving application performance.

### `profile-cpu`

Profile CPU usage and identify hot paths for optimization.

**Usage:** `/profile-cpu <target>`

**Best practices:**
- Profile before optimizing — never guess where the bottleneck is
- Use sampling profilers for production; instrumenting profilers for development
- Focus on the functions that account for >5% of total CPU time

### `bundle-analyze`

Analyze bundle size, identify large dependencies, and suggest tree-shaking or code splitting.

**Usage:** `/bundle-analyze [build-tool]`

**Best practices:**
- Visualize the bundle (webpack-bundle-analyzer, rollup-plugin-visualizer) to spot outliers
- Check for duplicated dependencies across chunks
- Lazy-load routes and heavy components below the fold

### `lighthouse-audit`

Run and interpret Lighthouse audits, implementing specific performance recommendations.

**Usage:** `/lighthouse-audit <url>`

### `cache-strategy`

Design caching strategy (HTTP, CDN, in-memory, Redis) for API endpoints and static assets.

**Usage:** `/cache-strategy <endpoint-or-resource>`

**Best practices:**
- Use `Cache-Control` headers with explicit `max-age` and `stale-while-revalidate`
- Cache at the highest layer possible (CDN > application > database)
- Invalidate carefully — prefer key-based invalidation over purging

### `image-optimize`

Suggest image optimization strategies: format (WebP/AVIF), compression, lazy loading, responsive sizes.

**Usage:** `/image-optimize [directory]`

### `lazy-load-strategy`

Design lazy loading and code splitting strategy for frontend applications.

**Usage:** `/lazy-load-strategy [route-or-component]`

### `render-optimize`

Optimize component rendering — memoization, virtualization, avoiding unnecessary re-renders.

**Usage:** `/render-optimize <component>`

### `profile-memory`

Profile memory allocation and identify leaks or excessive allocation patterns.

**Usage:** `/profile-memory <target>`

---

## Security Hardening

Skills for finding, fixing, and preventing security vulnerabilities.

### `security-audit`

Full security audit covering OWASP Top 10, dependency vulnerabilities, and configuration weaknesses.

**Usage:** `/security-audit [scope]`

**Best practices:**
- Start with the OWASP Top 10 checklist as a baseline
- Check authentication, authorization, input validation, and output encoding
- Review dependency tree for known CVEs (npm audit, pip audit, cargo audit)

### `vulnerability-scan`

Run and interpret dependency vulnerability scanners, triaging findings by exploitability and impact.

**Usage:** `/vulnerability-scan`

**Best practices:**
- Distinguish build-time vs. runtime dependencies — runtime CVEs are higher priority
- Check if a fix version exists and is compatible before recommending an upgrade
- For unresolved CVEs, assess whether the vulnerable code path is actually reachable

### `auth-audit`

Review authentication and authorization implementation: JWT handling, session management, RBAC, OAuth flows.

**Usage:** `/auth-audit [directory]`

**Best practices:**
- Verify password storage uses adaptive hashing (bcrypt, argon2) — never SHA or MD5
- Check JWT validation: signature, expiration, issuer, audience
- Ensure authorization checks happen on every request, not just at login

### `input-validation`

Audit and harden input validation across all user-facing entry points.

**Usage:** `/input-validation [directory]`

**Best practices:**
- Validate at the boundary (API layer), not deep in business logic
- Whitelist valid inputs; don't blacklist invalid ones
- Validate type, length, format, and range for every input field

### `secrets-detect`

Scan for hardcoded secrets, API keys, tokens, and credentials across the entire codebase.

**Usage:** `/secrets-detect [directory]`

### `xss-prevent`

Scan for cross-site scripting vulnerabilities and recommend Content-Security-Policy and output encoding.

**Usage:** `/xss-prevent [directory]`

### `sql-injection-audit`

Scan for SQL injection vectors, especially in dynamic query construction and raw SQL.

**Usage:** `/sql-injection-audit [directory]`

### `csrf-protection`

Audit CSRF protection measures and ensure proper token handling.

**Usage:** `/csrf-protection [directory]`

### `dependency-pin`

Pin or lock dependencies with hash verification to prevent supply chain attacks.

**Usage:** `/dependency-pin`

---

## Compliance & Licensing

Skills for license management, copyright, data privacy, and regulatory compliance.

### `license-audit`

Audit all dependencies for license compatibility with the project's license.

**Usage:** `/license-audit`

**Best practices:**
- Flag copyleft licenses (GPL, AGPL) for proprietary projects
- Check for license conflicts between direct and transitive dependencies
- Use tools like `license-checker`, `fossa-cli`, or `dash-licenses`

### `license-header`

Add or update license header comments across all source files consistently.

**Usage:** `/license-header <license-type>`

### `gdpr-check`

Audit code for GDPR compliance: data collection, consent, retention, and right-to-deletion support.

**Usage:** `/gdpr-check [directory]`

**Best practices:**
- Identify all PII collection points and verify they have a lawful basis
- Check that data deletion/export APIs exist and function correctly
- Verify that logs and backups don't retain data beyond the stated retention period

### `accessibility-compliance`

Generate a WCAG 2.1 AA conformance report and remediation plan.

**Usage:** `/accessibility-compliance [url-or-component]`

---

## Git & Version Control

Skills for commit management, branching, merging, and changelog generation.

### `commit-generate`

Generate a conventional commit message from staged changes following Conventional Commits.

**Usage:** `/commit-generate`

**Best practices:**
- Use `type(scope): description` format (feat, fix, refactor, docs, test, chore)
- Keep the summary under 72 characters
- Include the WHY in the body, not just the WHAT

### `changelog-generate`

Generate or update CHANGELOG.md from git history following Keep a Changelog format.

**Usage:** `/changelog-generate [version]`

**Best practices:**
- Group entries under Added, Changed, Deprecated, Removed, Fixed, Security
- Link to the full diff for each version
- Only include user-facing changes; internal refactors go in the commit history

### `pr-description`

Generate a structured pull request description: summary, changes, test plan, screenshots.

**Usage:** `/pr-description [base-branch]`

**Best practices:**
- Lead with a one-sentence summary of what this PR does and why
- Include a concrete test plan: "Run `npm test`, then visit `/dashboard` and verify..."
- Attach screenshots or screen recordings for UI changes

### `merge-conflict-resolve`

Resolve merge conflicts by analyzing both branches' intent and producing a clean merge.

**Usage:** `/merge-conflict-resolve <target-branch>`

**Best practices:**
- Understand the intent of both sides before resolving — don't just pick one
- Run tests after resolution to catch semantic (not just textual) conflicts
- When in doubt, favor the more recent change and flag for the author to review

### `rebase-plan`

Plan an interactive rebase: which commits to squash, reword, drop, or reorder.

**Usage:** `/rebase-plan`

### `branch-cleanup`

Identify stale local and remote branches that are safe to delete.

**Usage:** `/branch-cleanup`

### `git-hook-setup`

Set up git hooks (pre-commit, commit-msg, pre-push) with linting, formatting, and testing.

**Usage:** `/git-hook-setup`

**Best practices:**
- Use a hook manager (Husky, pre-commit, lefthook) rather than raw `.git/hooks`
- Keep hooks fast — pre-commit should run in under 10 seconds
- Run linters on staged files only, not the entire project

### `repo-sync`

Sync a fork with upstream, handling divergent branches gracefully.

**Usage:** `/repo-sync <upstream-remote>`

---

## CI/CD & DevOps

Skills for pipeline configuration, containerization, infrastructure-as-code, and deployment.

### `ci-setup`

Generate CI pipeline configuration (GitHub Actions, GitLab CI) for the project's tech stack.

**Usage:** `/ci-setup [ci-platform]`

**Best practices:**
- Separate lint, test, build, and deploy into distinct jobs with clear dependencies
- Cache dependencies between runs for faster feedback
- Run on every push to PR branches; deploy only on merge to main

### `dockerfile-generate`

Generate an optimized multi-stage Dockerfile with layer caching and security best practices.

**Usage:** `/dockerfile-generate <app-type>`

**Best practices:**
- Use multi-stage builds to keep the final image small (build in one stage, copy artifacts to another)
- Run as non-root user in the final stage
- Pin base image digests (not tags) for reproducible builds

### `docker-compose-setup`

Generate docker-compose configuration for local development with required services.

**Usage:** `/docker-compose-setup <services-list>`

**Best practices:**
- Use health checks so services start in dependency order
- Mount source code as volumes for hot reload
- Use named volumes for database data to persist across restarts

### `iac-generate`

Generate Infrastructure-as-Code (Terraform, Pulumi, CloudFormation) for cloud resources.

**Usage:** `/iac-generate <resource-description>`

**Best practices:**
- Use remote state storage (S3 + DynamoDB, Terraform Cloud) — never commit state files
- Tag all resources with owner, environment, and cost center
- Use variables for environment-specific values; never hardcode region or account IDs

### `deploy-strategy`

Design a deployment strategy (blue-green, canary, rolling) with rollback plan.

**Usage:** `/deploy-strategy <service-description>`

### `release-notes`

Generate release notes from git log between tags, organized by type.

**Usage:** `/release-notes <from-tag> <to-tag>`

### `kubernetes-manifest`

Generate Kubernetes manifests (Deployment, Service, Ingress, ConfigMap, Secret).

**Usage:** `/kubernetes-manifest <app-description>`

### `helm-chart`

Generate or update a Helm chart for Kubernetes deployment with proper templating.

**Usage:** `/helm-chart <chart-name>`

---

## Documentation

Skills for generating and maintaining project documentation.

### `readme-generate`

Generate a comprehensive README.md from project structure and code analysis.

**Usage:** `/readme-generate`

**Best practices:**
- Include: project name + description, quick start, usage, configuration, contributing
- Keep the quick start under 5 commands — the reader should be running in under 2 minutes
- Add badges (CI status, version, license) at the top

### `api-docs`

Generate API documentation from OpenAPI specs, JSDoc, or docstrings.

**Usage:** `/api-docs [source]`

**Best practices:**
- Include request/response examples for every endpoint
- Document error codes and their meanings
- Auto-generate from source annotations rather than hand-maintaining

### `code-comment`

Add or improve inline code documentation following project conventions (JSDoc, pydoc, rustdoc).

**Usage:** `/code-comment <file>`

**Best practices:**
- Document WHY, not WHAT — the code already shows what it does
- Document public APIs thoroughly; keep internal comments sparse
- Include `@throws`, `@returns`, and `@example` tags where relevant

### `migration-guide`

Write a migration guide for a breaking change: before/after examples and upgrade steps.

**Usage:** `/migration-guide <change-description>`

### `contributing-guide`

Generate or update CONTRIBUTING.md based on project setup, conventions, and CI process.

**Usage:** `/contributing-guide`

### `architecture-docs`

Generate architecture documentation from code structure, module boundaries, and existing ADRs.

**Usage:** `/architecture-docs`

### `tutorial-generate`

Generate a step-by-step tutorial from example code and natural-language description.

**Usage:** `/tutorial-generate <topic>`

---

## Data Science & Machine Learning

Skills for ML pipelines, data exploration, model training, and evaluation.

### `data-exploration`

Generate an exploratory data analysis notebook from a dataset description or file.

**Usage:** `/data-exploration <dataset>`

**Best practices:**
- Start with summary statistics and distributions before running models
- Visualize missing data patterns — they often carry signal
- Check for data leakage between train/validation/test splits

### `model-training`

Generate a model training script with data loading, preprocessing, training loop, and evaluation.

**Usage:** `/model-training <task-description>`

**Best practices:**
- Use a configuration system (YAML, dataclass) for all hyperparameters — never hardcode
- Log metrics, parameters, and artifacts to an experiment tracker (MLflow, W&B)
- Save the model with its preprocessing pipeline as a single artifact

### `ml-pipeline-setup`

Set up ML project structure with experiment tracking, dataset versioning, and model registry.

**Usage:** `/ml-pipeline-setup [framework]`

### `hyperparameter-tuning`

Generate hyperparameter optimization script (Optuna, Ray Tune) for a given model architecture.

**Usage:** `/hyperparameter-tuning <model-description>`

### `evaluation-report`

Generate an evaluation report comparing model metrics across experiments.

**Usage:** `/evaluation-report <experiment-ids>`

### `feature-pipeline`

Design and implement a feature engineering pipeline with transformations and encodings.

**Usage:** `/feature-pipeline <data-description>`

### `data-validation`

Generate data validation checks (Great Expectations, Pandera) for data quality monitoring.

**Usage:** `/data-validation <schema>`

### `model-serving`

Set up a model serving API (FastAPI, BentoML, TorchServe) with batching and caching.

**Usage:** `/model-serving <model-artifact>`

---

## Mobile Development

Skills for iOS, Android, and cross-platform mobile development.

### `ios-project-setup`

Scaffold an iOS project with Swift Package Manager, Info.plist, and recommended architecture.

**Usage:** `/ios-project-setup [project-name]`

### `android-project-setup`

Scaffold an Android project with Gradle, manifest, and MVVM architecture.

**Usage:** `/android-project-setup [project-name]`

### `react-native-component`

Generate a React Native component with platform-specific code, navigation, and styling.

**Usage:** `/react-native-component <description>`

### `flutter-widget`

Generate a Flutter widget with state management, theming, and platform channel integration.

**Usage:** `/flutter-widget <description>`

### `mobile-ci-setup`

Set up CI/CD for mobile apps (Fastlane, GitHub Actions with code signing).

**Usage:** `/mobile-ci-setup [platform]`

### `app-store-prep`

Prepare app metadata (descriptions, screenshots, privacy policy) for store submission.

**Usage:** `/app-store-prep [platform]`

---

## Automation & Scripting

Skills for writing scripts, task runners, and batch processing pipelines.

### `shell-script-generate`

Generate a shell script from a natural-language description with error handling and logging.

**Usage:** `/shell-script-generate <description>`

**Best practices:**
- Use `set -euo pipefail` at the top of every script
- Include a `--help` flag and usage function
- Add dry-run mode (`--dry-run`) for non-destructive testing

### `makefile-generate`

Generate a Makefile with common targets: `build`, `test`, `lint`, `clean`, `install`, `dev`.

**Usage:** `/makefile-generate`

**Best practices:**
- Mark phony targets with `.PHONY: build test lint clean`
- Use variables for tool names so they're easy to override
- Each target should produce a single, predictable outcome

### `cron-setup`

Generate cron/crontab entries or scheduled job configurations with error notification.

**Usage:** `/cron-setup <schedule-description>`

### `batch-process`

Generate a batch processing script for large-scale file or data operations.

**Usage:** `/batch-process <description>`

**Best practices:**
- Process in configurable batch sizes with progress reporting
- Include checkpoint/resume capability for long-running batches
- Handle rate limits and transient failures with exponential backoff

### `backup-script`

Generate backup and restore scripts with rotation, encryption, and verification.

**Usage:** `/backup-script <target>`

### `data-pipeline`

Generate a data pipeline script (ETL/ELT) with error handling, logging, and idempotency.

**Usage:** `/data-pipeline <description>`

---

## Logging, Monitoring & Observability

Skills for structured logging, metrics, tracing, dashboards, and alerting.

### `logging-setup`

Set up structured logging with levels, correlation IDs, and log aggregation format (JSON).

**Usage:** `/logging-setup [language]`

**Best practices:**
- Use structured (JSON) logging in production — never log unstructured text
- Include a correlation/trace ID in every log line for request tracing
- Log at the appropriate level: DEBUG for local dev, INFO for key events, ERROR for actionable failures

### `metrics-instrument`

Instrument code with metrics (counters, histograms, gauges) for key operations.

**Usage:** `/metrics-instrument <service>`

**Best practices:**
- Use the RED method: Rate, Errors, Duration for every service endpoint
- Add a counter for every error type, not a single "errors" counter
- Keep metric cardinality bounded — don't put user IDs or request IDs as label values

### `tracing-setup`

Set up distributed tracing (OpenTelemetry) across service boundaries.

**Usage:** `/tracing-setup <service>`

### `dashboard-generate`

Generate monitoring dashboards (Grafana, Datadog, CloudWatch) from service metrics.

**Usage:** `/dashboard-generate <service>`

**Best practices:**
- Organize dashboards in rows: golden signals first, then resource metrics, then business metrics
- Use consistent units, colors, and time ranges across panels
- Include SLO-based burn rate panels for critical services

### `alert-rules`

Define alert rules (Prometheus, Datadog) with thresholds, evaluation intervals, and notification routing.

**Usage:** `/alert-rules <service>`

**Best practices:**
- Alert on symptoms (high error rate), not causes (high CPU)
- Set appropriate `for` durations to avoid flapping (5m minimum for pages)
- Include a runbook link in every alert description

### `sli-slo-design`

Design SLI/SLO framework: define indicators, targets, and error budgets for a service.

**Usage:** `/sli-slo-design <service>`

### `incident-postmortem`

Generate an incident postmortem: timeline, root cause, impact, action items, follow-ups.

**Usage:** `/incident-postmortem <incident-summary>`

---

## Agent Platform Configuration

Skills for managing the agent platform itself — settings, memory, MCP servers, and custom commands.

### `agent-init`

Initialize agent configuration for the project: create CLAUDE.md, settings, and skill directory.

**Usage:** `/agent-init [platform]`

**Best practices:**
- CLAUDE.md should document project conventions, build commands, and architecture — not be a catch-all
- Set up a `.claude/commands/` directory for project-specific skills
- Add environment variable configuration in `settings.local.json`

### `skill-create`

Scaffold a new skill with YAML frontmatter template and prompt body.

**Usage:** `/skill-create <skill-name>`

**Best practices:**
- Follow the naming conventions and frontmatter guidelines in this document
- Test the skill on a small task before adding it to the project
- Iterate on the prompt based on observed agent behavior

### `settings-manage`

Manage agent settings: configure permissions, environment variables, hooks, and tool access.

**Usage:** `/settings-manage <setting> <value>`

### `mcp-server-setup`

Set up an MCP (Model Context Protocol) server and register it with the agent.

**Usage:** `/mcp-server-setup <server-name>`

### `agent-memory`

Manage agent memory: add project facts, conventions, or preferences to persistent memory.

**Usage:** `/agent-memory <fact-or-preference>`

**Best practices:**
- Store project-level facts (why decisions were made, what patterns to follow)
- Don't store information derivable from the codebase or git history
- Review and prune stale memories periodically

### `hook-setup`

Set up agent hooks (pre-command, post-command, on-error) for automated workflows.

**Usage:** `/hook-setup <hook-type> <command>`

### `model-tuning`

Configure model selection, effort level, and behavior parameters for different task types.

**Usage:** `/model-tuning <task-type> <setting>`

---

## Contributing

Contributions are welcome. Please follow these guidelines:

1. **One skill per PR.** Keep changes focused and reviewable.
2. **Use the established format.** Follow the skill entry template — name, description, usage, best practices.
3. **Only add skills you've used.** This list values practical experience over theoretical completeness.
4. **Include best practice notes.** The "why" is as important as the "what."
5. **Check for duplicates.** Search existing categories before adding a new entry.
6. **Propose new categories sparingly.** Most skills fit within the existing 21 categories.

### Skill Entry Template

```markdown
### `skill-name`

One-sentence description of what the skill does and when to use it.

**Usage:** `/skill-name <required-arg> [optional-arg]`

**Best practices:**
- Concrete, actionable guidance
- Non-obvious tips that come from experience
- Pitfalls to avoid
```

## License

[MIT](LICENSE)
