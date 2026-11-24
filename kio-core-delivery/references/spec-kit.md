# 📐 Kio Spec-Kit — Specification-Driven Development (SDD) Engine

Implements the **Specification-Driven Development (SDD)** methodology. Ensures every complex project or feature has a rigorous specification and explicit acceptance criteria before any production code is written.

---

## 🎯 When to Activate Spec-Kit

1. **Large / Complex Projects**: Monorepos, multi-service SaaS, microservices, fintech/healthcare systems, or branching domain logic.
2. **Explicit User Request**: *"use spec-kit"*, *"spec-driven"*, *"write the spec first"*, *"design the system before coding"*.
3. **Signature Files Detected**: The repository contains `.spec-kit/`, or `constitution.md`, `spec.md`, `specification.md`.
4. **Hybrid Cross-Skill Execution**: When architectural boundaries are defined via `kio-core-delivery` and implementation is delegated to `kio-frontend-craft`, `kio-backend-cloud`, `kio-wordpress-fse`, and verified via `kio-qa-security`.

---

## 🏛️ The 4-Phase Spec-Kit Pipeline

```
Phase 1: Constitution ──► Phase 2: Specification ──► Phase 3: Architectural Plan ──► Phase 4: Atomic Tasks
   (Project Guardrails)       (Domain Requirements)        (Technical Blueprint)         (2-5 Min Checkpoints)
```

### Phase 1: Project Constitution (`.spec-kit/constitution.md`)
Establishes non-negotiable project guardrails (template: [`../assets/templates/constitution-template.md`](../assets/templates/constitution-template.md)):
- **Architecture Boundaries**: Fixed tech stack, allowed/forbidden libraries (YAGNI).
- **Code Standards**: File size `< 700 lines`, 0 hardcoded credentials (100% `.env`).
- **Security Baseline**: OWASP Top 10, sanitization, RBAC, data privacy.
- **Verification Gates**: Minimum test coverage, Zero-False-Completion.

### Phase 2: Functional Specification (`.spec-kit/specs/{feature}/spec.md`)
Defines **WHAT** must be built without guesswork (template: [`../assets/templates/spec-template.md`](../assets/templates/spec-template.md)):
1. **User Stories & Actors**: Who does what and why.
2. **Domain Model & Entities**: Core entities and their relationships.
3. **Boundary Conditions & Edge Cases**: Exceptions, network failures, malformed input, race conditions.
4. **Functional Requirements**: Numbered requirements (`FR-01`, `FR-02`...).
5. **Acceptance Criteria (Gherkin Syntax)**:
   ```gherkin
   Scenario: User completes order checkout
     Given the user is signed in and has 2 items in the cart
     When the user submits the order with COD payment
     Then the system creates a new order with status "Pending"
     And sends an order confirmation email
     And clears the current cart
   ```

### Phase 3: Architectural Plan (`.spec-kit/specs/{feature}/plan.md`)
Defines **HOW** the system is built (template: [`../assets/templates/plan-template.md`](../assets/templates/plan-template.md)):
1. **System & Component Architecture**: Data-flow diagrams and module boundaries.
2. **Database Schemas & Migrations**: Table schemas, indexes, foreign keys (`snake_case`).
3. **API Contracts**: OpenAPI / JSON Schema / Typed Contracts (`/api/v1/...`).
4. **Risk & Mitigation Analysis**: Technical risks and rollback/mitigation strategies.

### Phase 4: Atomic Tasks Breakdown (`.spec-kit/specs/{feature}/tasks.md`)
Decomposes the plan into 2–5 minute atomic tasks (template: [`../assets/templates/tasks-template.md`](../assets/templates/tasks-template.md)):
- Each task specifies: **Task ID** (`TASK-01`...), **Target Files**, **Prerequisites**, and **Verification Command**.

---

## 🗂️ Spec-Kit Templates & Command Workflows

### 1. Core Philosophy
- Read [`spec-driven.md`](spec-driven.md) — *The Power Inversion: Code serves specifications, specifications don't serve code.*

### 2. Official Document Templates (`assets/templates/`)
- 📜 **Constitution**: [`../assets/templates/constitution-template.md`](../assets/templates/constitution-template.md)
- 📋 **Specification**: [`../assets/templates/spec-template.md`](../assets/templates/spec-template.md)
- 🏗️ **Architectural Plan**: [`../assets/templates/plan-template.md`](../assets/templates/plan-template.md)
- 📝 **Tasks Breakdown**: [`../assets/templates/tasks-template.md`](../assets/templates/tasks-template.md)
- ✅ **Quality Checklist**: [`../assets/templates/checklist-template.md`](../assets/templates/checklist-template.md)

### 3. Command Workflows (`assets/templates/commands/`)
- `/kio-spec-constitution`: [`../assets/templates/commands/constitution.md`](../assets/templates/commands/constitution.md)
- `/kio-spec-specify`: [`../assets/templates/commands/specify.md`](../assets/templates/commands/specify.md)
- `/kio-spec-clarify`: [`../assets/templates/commands/clarify.md`](../assets/templates/commands/clarify.md)
- `/kio-spec-plan`: [`../assets/templates/commands/plan.md`](../assets/templates/commands/plan.md)
- `/kio-spec-tasks`: [`../assets/templates/commands/tasks.md`](../assets/templates/commands/tasks.md)
- `/kio-spec-implement`: [`../assets/templates/commands/implement.md`](../assets/templates/commands/implement.md)
- `/kio-spec-analyze`: [`../assets/templates/commands/analyze.md`](../assets/templates/commands/analyze.md)
- `/kio-spec-checklist`: [`../assets/templates/commands/checklist.md`](../assets/templates/commands/checklist.md)
- `/kio-spec-converge`: [`../assets/templates/commands/converge.md`](../assets/templates/commands/converge.md)

> Installing Kio with `setup.sh` / `setup.ps1` adds the `/kio-spec-*` commands (Claude Code, Codex, Cursor, Gemini CLI). They run in **Kio mode**: no Spec-Kit CLI needed, the template's `scripts:` / `{SCRIPT}` / `.specify/` parts are skipped, templates come from `assets/templates/`, artifacts go to `specs/<NNN>-<name>/`.
