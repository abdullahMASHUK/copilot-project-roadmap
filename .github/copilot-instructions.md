# Copilot Instructions

This file provides global guidance for all Copilot interactions across the project. It establishes the foundation for consistent AI-assisted development across the team.

## Core Principles

1. **Context-Driven Development** — Always reference relevant context, memory, and specs
2. **Domain Expertise** — Use domain-specific instructions to maintain consistency
3. **Explicit Requirements** — Prefer clear specs over ambiguous requirements
4. **Documentation First** — Update docs alongside code changes
5. **Continuous Learning** — Feed lessons learned back into memory files

## For Humans: Understanding This Project

**👉 NEW TO PROJECT?** [docs/guides/PROJECT-STRUCTURE-GUIDE.md](docs/guides/PROJECT-STRUCTURE-GUIDE.md) — Complete guide to all file types in this project. Learn what every configuration file, context file, memory file, template, and guide does. Reference for creating and maintaining files correctly.

**👉 WANT TO COMMUNICATE WITH COPILOT?** [docs/guides/COMMUNICATION-GUIDE.md](docs/guides/COMMUNICATION-GUIDE.md) — How to give requirements to Copilot via SRS, ER diagrams, specs. Includes visual workflow diagram and real examples.

**👉 FIRST TIME WITH AGENTS?** [docs/guides/AGENT-REFERENCE.md](docs/guides/AGENT-REFERENCE.md) — Quick decision tree for which agent/tool to use and when.

## Understanding Project File Types

This project uses 11 different file types, each serving a specific purpose. If you need to create or update a file and aren't sure what to do:

| File Type | Purpose | Example |
|-----------|---------|---------|
| **INSTRUCTIONS** | Tell Copilot HOW to code | `.github/instructions/backend.instructions.md` |
| **CONTEXT** | Explain WHAT the system is | `docs/domains/backend/context.md` |
| **MEMORY** | Remember lessons learned | `docs/domains/backend/memory.md` |
| **AGENT** | Define specialized role | `.github/agents/roles/implementation/backend-api-specialist.agent.md` |
| **REGISTRY** | Index all agents | `.github/agents/roles/AGENT-REGISTRY.md` |
| **SELECTION** | Pick the right agent | `.github/agents/roles/AGENT-SELECTION-GUIDE.md` |
| **HANDOFF** | Agent workflow | `.github/agents/roles/HANDOFF-PROTOCOL.md` |
| **TEMPLATE** | Starting point document | `docs/templates/feature-spec.md` |
| **FEATURE SPEC** | What to build | `docs/features/payment-processing/spec.md` |
| **GUIDE** | How-to for humans | `docs/guides/COMMUNICATION-GUIDE.md` |
| **CHANGELOG** | What changed | `docs/changelog.md` |

**See [PROJECT-STRUCTURE-GUIDE.md](docs/guides/PROJECT-STRUCTURE-GUIDE.md) for complete reference**, including when to create/update each type, examples, and best practices.

## Quick Reference by Role

### Backend Development
- **Instructions**: [.github/instructions/backend.instructions.md](.github/instructions/backend.instructions.md)
- **Context**: [docs/domains/backend/context.md](docs/domains/backend/context.md)
- **Memory**: [docs/domains/backend/memory.md](docs/domains/backend/memory.md)
- **Use prompts**: `implement-from-spec`, `security-review`, `test-gen`

### Frontend Development
- **Instructions**: [.github/instructions/frontend.instructions.md](.github/instructions/frontend.instructions.md)
- **Context**: [docs/domains/frontend/context.md](docs/domains/frontend/context.md)
- **Memory**: [docs/domains/frontend/memory.md](docs/domains/frontend/memory.md)
- **Use prompts**: `implement-from-spec`, `refactor-slice`, `test-gen`
- **For feature development**: Use Orchestration System → [frontend-implementation.agent.md](.github/agents/orchestration/frontend-implementation.agent.md)

### DevOps / Actions
- **Instructions**: [.github/instructions/actions.instructions.md](.github/instructions/actions.instructions.md)
- **Prompts**: `implement-from-spec`

### Architectural Decisions (NOT Feature Development)
- **Agent**: [.github/agents/plan.agent.md](.github/agents/plan.agent.md) → For architectural decisions and ADRs only
- **Context**: [docs/_global/context.md](docs/_global/context.md)
- **Memory**: [docs/_global/memory.md](docs/_global/memory.md)
- **Use When**: Making system-wide tech decisions, planning migrations, creating ADRs
- **Do NOT use for**: Feature development → Use Orchestration System instead

## Agent Orchestration System (NEW!)

For **complete feature development from SRS to production**, use the orchestration workflow:

```
SRS (Product Owner)
  ↓
Project Planner Agent (transforms SRS → specifications & diagrams)
  ↓
Backend Implementation Agent + Frontend Implementation Agent (parallel)
  ↓
Review/QA Agent (verifies against specs)
  ↓
Merge & Deploy (human approval)
```

**Start here**: [.github/agents/orchestration/WORKFLOW.md](.github/agents/orchestration/WORKFLOW.md) — See also: [Communication Guide](docs/guides/COMMUNICATION-GUIDE.md) with visual workflow

**Key Documents**:
- **Workflow**: [WORKFLOW.md](.github/agents/orchestration/WORKFLOW.md) — Complete step-by-step guide
- **Handoff Protocol**: [HANDOFF-PROTOCOL.md](.github/agents/orchestration/HANDOFF-PROTOCOL.md) — How work flows between agents
- **Agents**:
  - [project-planner.agent.md](.github/agents/orchestration/project-planner.agent.md) — Transforms SRS into specs
  - [backend-implementation.agent.md](.github/agents/orchestration/backend-implementation.agent.md) — Implements backend
  - [frontend-implementation.agent.md](.github/agents/orchestration/frontend-implementation.agent.md) — Implements frontend
  - [review-qa.agent.md](.github/agents/orchestration/review-qa.agent.md) — Verifies quality & specs
- **Templates**:
  - [SRS Input Template](.github/templates/handoffs/srs-input-template.md) — Write requirements
  - [Orchestration README](.github/agents/orchestration/README.md) — System overview

**When to Use**:
- Developing a new feature from scratch
- Complex features needing formal specifications
- Team workflows requiring clear handoffs
- Want explicit quality gates between phases

## Role-Based Agent System (RECOMMENDED!)

**This is the recommended way to work with Copilot for all features.**

### What Is It?

A sophisticated multi-specialist agent system where 9 specialized agents work together sequentially on features. Each agent has deep expertise in their role (Database Design, API Design, Backend Implementation, Frontend, Security, Documentation, etc.).

### How It Works

1. You provide requirements (SRS, spec, or quick description)
2. Copilot reads [AGENT-REGISTRY.md](.github/agents/roles/AGENT-REGISTRY.md) to see available agents
3. Copilot uses [AGENT-SELECTION-GUIDE.md](.github/agents/roles/AGENT-SELECTION-GUIDE.md) to pick right agents
4. Copilot invokes agents sequentially per [HANDOFF-PROTOCOL.md](.github/agents/roles/HANDOFF-PROTOCOL.md)
5. Each agent hands off to next with validation gates
6. Result: Complete feature with code, tests, docs, security audit

### The 9 Agents

**Planning:**
- Project Manager → Breaks down requirements into features
- Architect → Designs system architecture

**Implementation:**
- API Designer → Creates OpenAPI specifications
- Backend API Specialist → Implements Express routes & services
- Frontend Specialist → Builds React components & UI
- Data Analyst → Designs database schema & migrations
- Security Expert → Audits code for vulnerabilities

**Support:**
- Documentation Specialist → Writes API docs & guides
- Review/QA → Verifies everything meets spec

### Example: Payment Feature

```
Requirements → Project Manager (scope breakdown)
           ↓
Architect (technical design)
           ↓
[API Designer + Data Analyst + Backend Specialist + Frontend Specialist in parallel]
           ↓
Security Expert (vulnerability audit)
           ↓
Documentation Specialist (API docs)
           ↓
✅ Complete, tested, documented, secure feature
```

**Timeline**: 14-16 days for complex feature

### Quick Start

1. Tell Copilot your feature
2. Include: What needs to be built (requirements, wireframes, etc)
3. Copilot handles agent selection and orchestration
4. Each agent delivers their part
5. Integration happens automatically

**See [.github/agents/roles/README.md](.github/agents/roles/README.md) for complete guide.**

## Older Orchestration System

The previous orchestration system is still available for simpler workflows:

**Start here**: [.github/agents/orchestration/WORKFLOW.md](.github/agents/orchestration/WORKFLOW.md)

This system works for quick features if you prefer simplified orchestration.

---

## Global Context & Decision History

### Always Available
- **Architecture**: [docs/_global/context.md](docs/_global/context.md) — High-level system design, tech stack, conventions
- **Decisions**: [docs/_global/memory.md](docs/_global/memory.md) — Why we made certain choices, constraints, patterns
- **Changelog**: [docs/changelog.md](docs/changelog.md) — Recent updates to specs, context, and decisions

### Project-Specific Context
- **All Projects**: [docs/projects/README.md](docs/projects/README.md) — Project index and discovery guide
- **backend-api**: [docs/projects/backend-api/context.md](docs/projects/backend-api/context.md) + [memory.md](docs/projects/backend-api/memory.md)
- **frontend-web**: [docs/projects/frontend-web/context.md](docs/projects/frontend-web/context.md) + [memory.md](docs/projects/frontend-web/memory.md)
- **Your Project?** Use the [project template](docs/projects/project-template/) to create project-specific context

### Feature-Specific
- Navigate to [docs/features/](docs/features/) for specific feature specs, context, and gotchas
- For project-specific features: [docs/projects/{project-name}/features/](docs/projects/)

## Path-Specific Instructions

Different directories have specialized guidance for that code type. When working in a specific path, combine both your domain instructions AND the applicable path instructions:

| Path | Instructions |
|------|--------------|
| `src/handlers/`, `src/routes/` | [API Endpoints](./instructions/paths/api.instructions.md) |
| `src/components/` | [React Components](./instructions/paths/components.instructions.md) |
| `src/services/` | [Business Logic Services](./instructions/paths/services.instructions.md) |
| `tests/`, `**/__tests__/` | [Testing](./instructions/paths/testing.instructions.md) |
| `migrations/` | [Database Migrations](./instructions/paths/database.instructions.md) |

**Example**: Building a React form component
- Read domain instruction: [frontend.instructions.md](./instructions/frontend.instructions.md)
- Read path instruction: [components.instructions.md](./instructions/paths/components.instructions.md)
- Combine guidance from both

See [paths/README.md](./instructions/paths/README.md) for complete index and how path instructions override domain instructions.

## Workflow Prompts

Use these reusable prompts by including them in your Copilot chat:

- **implement-from-spec** — Convert requirements into production code with tests
- **security-review** — Audit code for vulnerabilities and best practices
- **refactor-slice** — Refactor a specific code section with zero functionality change
- **test-gen** — Generate comprehensive test coverage

See [.github/prompts/](.github/prompts/) for full prompt details.

## Getting Started

**New to the team?** Start with [COPILOT-USAGE.md](COPILOT-USAGE.md) for onboarding.

**Updating instructions?** Follow the template at [.github/templates/instruction-template.md](.github/templates/instruction-template.md).

**Starting a new feature?** Use the template at [.github/templates/feature-spec.md](.github/templates/feature-spec.md).

---

**Last Updated**: February 23, 2026
