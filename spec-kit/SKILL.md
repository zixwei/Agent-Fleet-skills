---
name: spec-kit
description: Spec-Driven Development (SDD): 规格是源头, 代码是规格的生成物. /speckit.specify 把想法变成带验收标准的结构化规格, /speckit.plan 生成合宪的技术方案、数据模型与契约, /speckit.tasks 导出可并行执行的任务清单; 宪法九条与模板门禁保证规格精确、可测、可生成. Use when building a feature or a new app with an AI coding agent and the spec, not the code, should be the source of truth.
license: MIT
metadata:
  author: github
  source: github/spec-kit
  category: 工程方法
---

# Spec-Driven Development with spec-kit

SDD inverts the usual order: specifications don't serve code, code serves specifications. The PRD is not a guide for implementation, it is the source that generates implementation. Technical plans are precise definitions that produce code. Maintaining software means evolving specifications; debugging means fixing the specification and the plan that generated the wrong code. Code is the last-mile expression of the spec in a particular language and framework. Full source: [spec-driven.md](references/spec-driven.md).

## When to use it

Use SDD when building a feature or a new app with an AI coding agent, when the work is big enough that a vague prompt will drift, when several people (or several sessions) must build against the same intent, or when requirements will keep changing and you want pivots to be regenerations, not manual rewrites. Do not use the full SDD flow for a one-line fix or a trivial change; use it when the spec is worth writing down.

## The workflow

```
idea -> /speckit.specify -> /speckit.clarify -> /speckit.plan -> /speckit.tasks -> /speckit.implement
                                  (constitution gates)      (checklist / analyze / converge as needed)
```

1. **Specify.** Turn a vague idea into a structured specification with user stories, acceptance criteria and measurable success criteria. The spec says WHAT users need and WHY. It never says HOW (no tech stack, no APIs, no code structure). Anything not specified is marked `[NEEDS CLARIFICATION: specific question]`, never guessed.
2. **Clarify.** Ask the user the open questions until the ambiguities are resolved. Do not invent plausible answers.
3. **Plan.** Turn the spec into an implementation plan: technical architecture, data model, API contracts, research notes, quickstart validation scenarios. Every technology choice carries documented rationale and traces back to a requirement.
4. **Tasks.** Derive an executable task list from the plan, contracts and scenarios. Mark independent tasks `[P]` and group safe parallel batches. Tasks are ordered: contracts first, then tests (contract, integration, e2e, unit), then source code to make the tests pass.
5. **Implement.** Execute the tasks. If implementation contradicts the spec, fix the spec (and the plan), not just the code.

Supporting commands: `/speckit.constitution` (write or amend the project constitution), `/speckit.checklist` (generate a quality checklist for a spec or plan), `/speckit.analyze` (cross-check spec, plan and tasks for consistency), `/speckit.converge` (reconcile divergent specs or plans). Command definitions are in [references/commands/](references/commands/specify.md).

## The three core commands

### /speckit.specify — feature description to specification

- Scans existing specs and assigns the next feature number (001, 002, ...).
- Creates a branch named from the description and the directory `specs/<nnn>-<name>/`.
- Copies the spec template and fills it with the requirements: user stories, acceptance criteria, success criteria, edge cases.
- Output: `specs/<nnn>-<name>/spec.md`.

### /speckit.plan — specification to implementation plan

- Reads the spec, checks it against the project constitution, translates business requirements into technical decisions.
- Output: `specs/<nnn>-<name>/plan.md`, plus `research.md` (library and approach comparisons), `data-model.md` (entities and schemas), `contracts/` (API and event contracts), `quickstart.md` (key validation scenarios).

### /speckit.tasks — plan to executable task list

- Reads `plan.md` (required) and, if present, `data-model.md`, `contracts/`, `research.md`.
- Converts contracts, entities and scenarios into concrete tasks, marks `[P]` where independent, and writes `specs/<nnn>-<name>/tasks.md`.

## The constitution: nine articles

Every project keeps a constitution at `memory/constitution.md`. It is the architectural DNA; implementation plans must pass its gates or document justified exceptions in Complexity Tracking. The built-in articles:

- **I. Library-first.** Every feature begins as a standalone library, never directly in application code.
- **II. CLI interface.** Every library exposes its functionality through a CLI: text in (stdin, arguments, files), text out (stdout), JSON for structured data.
- **III. Test-first (non-negotiable).** No implementation code before tests are written, approved by the user, and confirmed FAILING (red phase).
- **IV–VI. Project-defined.** Each project fills these with its own non-negotiables (e.g. integration testing, observability, versioning, breaking changes). The template provides placeholder slots.
- **VII. Simplicity.** Minimal project structure: at most 3 projects for initial implementation; more needs documented justification. No future-proofing.
- **VIII. Anti-abstraction.** Use framework features directly instead of wrapping them. Single model representation.
- **IX. Integration-first testing.** Prefer real databases over mocks, real service instances over stubs; contract tests are mandatory before implementation.

Amendments need documented rationale, maintainer review and a backwards-compatibility assessment. Principles are immutable; their application evolves.

## Template discipline: how structure constrains the LLM

The templates are the quality mechanism. Follow them literally; they are in [references/](references/spec-template.md).

- **WHAT, not HOW.** The spec template forbids implementation details. This keeps specs stable when the stack changes.
- **Mark uncertainty, don't guess.** Every ambiguity becomes `[NEEDS CLARIFICATION: ...]`. A login system without a stated auth method is a clarification question, not an assumption of email/password.
- **Checklists are unit tests for the spec.** Requirement completeness (no `[NEEDS CLARIFICATION]` left, requirements testable and unambiguous, success criteria measurable), no speculative or "might need" features, every phase with clear prerequisites and deliverables.
- **Gates before implementation (Phase -1).** Simplicity gate (≤3 projects, no future-proofing), anti-abstraction gate (framework used directly, single model), integration-first gate (contracts defined, contract tests written). A failed gate must be justified in Complexity Tracking.
- **Test-first file order.** Contracts, then test files (contract, integration, e2e, unit), then source files.
- **Hierarchical detail.** The plan stays high-level and readable; code samples, detailed algorithms and long specs go into `implementation-details/` files, not the plan.
- **No speculative features.** Every feature traces back to a concrete user story with acceptance criteria.

## File layout

```
specs/<nnn>-<name>/
  spec.md          # WHAT + WHY, acceptance criteria, [NEEDS CLARIFICATION] markers
  plan.md          # HOW at architecture level, decisions with rationale, Phase -1 gates
  research.md      # library and approach comparisons
  data-model.md    # entities, schemas, relationships
  contracts/       # API specs, event contracts
  quickstart.md    # key validation scenarios
  tasks.md         # executable tasks, [P] marks independent work
memory/
  constitution.md  # the nine articles, project-filled IV-VI, dated amendments
```

One feature, one branch, one `specs/` directory. Specs are versioned and reviewed like code; a product-manager change to acceptance criteria flags the affected technical decisions in the plan.

## Working notes

- Early code generation may be exploratory: generate as soon as spec and plan are stable enough, not only when "complete". Domain concepts become data models, user stories become API endpoints, acceptance scenarios become tests.
- Production feedback updates the spec: a performance bottleneck becomes a new non-functional requirement, a security incident becomes a constraint on all future generations.
- Branching for exploration: generate several implementation approaches from the same spec to compare performance, maintainability, cost or UX.
- Research agents gather context during specification: library compatibility, benchmarks, security implications, organizational constraints (database standards, auth, deployment policy) integrate into the spec automatically.

## References (verbatim upstream)

- [references/spec-driven.md](references/spec-driven.md) — the full SDD methodology (power inversion, workflow, commands, constitution, templates).
- Templates: [spec-template.md](references/spec-template.md), [plan-template.md](references/plan-template.md), [tasks-template.md](references/tasks-template.md), [constitution-template.md](references/constitution-template.md), [checklist-template.md](references/checklist-template.md).
- Command definitions: [specify](references/commands/specify.md), [clarify](references/commands/clarify.md), [plan](references/commands/plan.md), [tasks](references/commands/tasks.md), [implement](references/commands/implement.md), [constitution](references/commands/constitution.md), [checklist](references/commands/checklist.md), [analyze](references/commands/analyze.md), [converge](references/commands/converge.md).

Source: [github/spec-kit](https://github.com/github/spec-kit) (MIT). This SKILL.md was composed from the upstream method document `spec-driven.md` and its templates, because upstream ships no SKILL.md; the method text above is a faithful condensation, and the references are verbatim upstream files.
