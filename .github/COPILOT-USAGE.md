# Copilot Usage Guide

This document explains how to effectively use Copilot within this project structure. It's designed for team members new to the workflow or those needing a refresher.

## The 5-Minute Overview

This project uses a **hierarchical context system** to guide Copilot:

```
Global Context (all team members)
    ├── Architecture & Tech Stack (docs/_global/context.md)
    ├── Decisions & Constraints (docs/_global/memory.md)
    │
    ├── Project-Specific Context (docs/projects/{project-name}/)
    │        ├── Project Architecture & Tech Stack
    │        ├── Project Decisions & Learnings
    │        │
    │        ├── Domain-Specific Context (docs/domains/{backend|frontend}/)
    │        │        ├── Tech Details & Conventions
    │        │        ├── Known Issues & Gotchas
    │        │        │
    │        │        └── Path-Specific Context (src/handlers/, src/components/, etc.)
    │        │                ├── API Endpoint Patterns
    │        │                ├── Component Patterns
    │        │                ├── Test Patterns
    │        │                └── Database Migration Patterns
    │        │
    │        └── Project Feature Specs (docs/projects/{project-name}/features/)
    │                ├── Requirements & Acceptance Criteria
    │                ├── API Contracts
    │                └── Implementation Notes
    │
    └── Global Feature Specs (docs/features/)
            ├── Cross-Project Features
            ├── Requirements
            └── Implementation Notes
```

**The Rule**: When working on anything, Copilot should have access to:
1. Global context (always)
2. Project context (if working in a specific project)
3. Domain context (if backend/frontend within that project)
4. Path context (if your file is in a specialized directory)
5. Feature specs (if implementing a feature)

## How to Use Copilot

### Scenario 0: Complete Feature Development (Recommended Workflow)

**Task**: Develop a new feature end-to-end (from requirements to production)

**Use the Orchestration System**:

1. **Write SRS** (5-10 days)
   - Product Owner writes Software Requirements Specification
   - Template: [.github/templates/handoffs/srs-input-template.md](.github/templates/handoffs/srs-input-template.md)
   - Output: `docs/projects/{project}/srs/{feature}/SRS.md`

2. **Plan Feature** (5-10 days)
   - Use Project Planner Agent: [.github/agents/orchestration/project-planner.agent.md](.github/agents/orchestration/project-planner.agent.md)
   - Transforms SRS → Feature spec, diagrams, implementation guides
   - Output: Complete feature directory with specs, diagrams, acceptance criteria

3. **Implement Backend** (7-15 days, parallel)
   - Use Backend Implementation Agent: [.github/agents/orchestration/backend-implementation.agent.md](.github/agents/orchestration/backend-implementation.agent.md)
   - Creates API endpoints, services, tests
   - Output: PR ready for review

4. **Implement Frontend** (7-15 days, parallel)
   - Use Frontend Implementation Agent: [.github/agents/orchestration/frontend-implementation.agent.md](.github/agents/orchestration/frontend-implementation.agent.md)
   - Creates pages, components, tests, accessibility
   - Output: PR ready for review

5. **Review & Verify** (3-5 days)
   - Use Review/QA Agent: [.github/agents/orchestration/review-qa.agent.md](.github/agents/orchestration/review-qa.agent.md)
   - Verifies all specs met, quality standards met
   - Output: APPROVED or CHANGES REQUESTED

6. **Deploy** (1-2 days)
   - Human approval and merge
   - CI/CD pipeline → Production

**Complete Workflow Guide**: [.github/agents/orchestration/WORKFLOW.md](.github/agents/orchestration/WORKFLOW.md)

**Handoff Protocol** (how agents pass work): [.github/agents/orchestration/HANDOFF-PROTOCOL.md](.github/agents/orchestration/HANDOFF-PROTOCOL.md)

**Total Time**: 28-56 days from SRS to production

---

### Scenario 1: Implementing a Feature (Existing Spec)

**Task**: Build the authentication flow

**Steps**:
1. Read the feature spec: `docs/features/auth/spec.md`
2. Review domain context: `docs/domains/backend/context.md` + `docs/domains/backend/memory.md`
3. In Copilot, instruct:
   > "Implement the auth service according to [docs/features/auth/spec.md](docs/features/auth/spec.md). Follow the patterns in [docs/domains/backend/context.md](docs/domains/backend/context.md). Watch out for issues noted in [docs/domains/backend/memory.md](docs/domains/backend/memory.md)."

4. Use the prompt: **implement-from-spec**
5. After implementation, update `docs/domains/backend/memory.md` with any lessons learned

### Scenario 2: Code Review / Refactoring

**Task**: Improve the UserService

**Steps**:
1. Use the prompt: **refactor-slice** or **security-review**
2. Reference domain conventions: `docs/domains/backend/context.md`
3. Copilot will ensure changes align with team standards

### Scenario 3: Writing Tests

**Task**: Test coverage for the Auth module

**Steps**:
1. Use the prompt: **test-gen**
2. Reference the feature spec and backend context
3. Copilot generates tests matching project patterns

### Scenario 4: Planning an Architecture Change

**Task**: Decide on caching strategy

**Steps**:
1. Use the agent: [.github/agents/plan.agent.md](.github/agents/plan.agent.md)
2. Reference global context: `docs/_global/context.md`
3. Document decision in: `docs/_global/memory.md` with date and rationale

## Key Files to Know

| File | When to Use |
|------|------------|
| `copilot-instructions.md` | Reference guide (this file's parent) |
| `docs/_global/context.md` | Understanding overall architecture |
| `docs/_global/memory.md` | Why we made past decisions |
| `docs/projects/README.md` | Find which project you're working on |
| `docs/projects/{project}/context.md` | **Project-specific tech decisions** (NEW!) |
| `docs/projects/{project}/memory.md` | **Project-specific gotchas & learnings** (NEW!) |
| `docs/domains/{domain}/context.md` | Domain-specific tech decisions |
| `docs/domains/{domain}/memory.md` | Domain-specific gotchas & patterns |
| `.github/instructions/paths/*.md` | Path-specific guidance |
| `docs/projects/{project}/features/` | Project-specific feature specs |
| `docs/features/{feature}/spec.md` | Cross-project feature specifications |
| `.github/prompts/*.prompt.md` | Reusable instructions for common tasks |
| `.github/instructions/{role}.instructions.md` | Role-specific guidelines |

## Path-Specific Instructions (When to Use)

When your file is in a specific directory, reference the corresponding path-specific instructions:

| Your File Location | Reference This |
|--------------------|----------------|
| `src/handlers/user.handler.ts` | [API Endpoints](./instructions/paths/api.instructions.md) |
| `src/components/Button/Button.tsx` | [Components](./instructions/paths/components.instructions.md) |
| `src/services/user.service.ts` | [Services](./instructions/paths/services.instructions.md) |
| `tests/user.service.test.ts` | [Testing](./instructions/paths/testing.instructions.md) |
| `migrations/2026-02-23-create-users.js` | [Database](./instructions/paths/database.instructions.md) |

**Example**: You're building `src/components/LoginForm/LoginForm.tsx`

Copilot instructions should be:
> "I'm building a React login form component. Reference:
> - [frontend domain context](docs/domains/frontend/context.md)
> - [frontend domain memory](docs/domains/frontend/memory.md)
> - [component path instructions](./instructions/paths/components.instructions.md)
> - [login feature spec](docs/features/auth/spec.md)
>
> Build the component following all patterns."

This gives Copilot:
1. **Global context** (implicit)
2. **Domain context** (frontend patterns, styling, state management)
3. **Path context** (component file structure, hooks patterns, testing)
4. **Feature context** (login requirements, API contract, error cases)

## Common Workflows

### Adding a Project-Specific Feature (NEW!)
1. Create `docs/projects/{project-name}/features/{feature-name}/spec.md` 
2. Reference your project context: `docs/projects/{project}/context.md`
3. In Copilot, provide both spec and project context
4. Build following project-specific patterns and tech stack
5. Document learnings in `docs/projects/{project}/memory.md`

### Adding a New Cross-Project Feature
1. Create `docs/features/{feature-name}/spec.md` using template
2. Optionally add feature-specific `context.md` and `memory.md`
3. Reference global context for team-wide decisions
4. Reference in Copilot instructions when building

### Adding a New Feature
1. Read: [API path instructions](./instructions/paths/api.instructions.md)
2. Read: [backend domain context](docs/domains/backend/context.md)
3. Reference: Feature spec for endpoint requirements
4. Instruct Copilot with all three references

### Creating a React Component
1. Read: [Components path instructions](./instructions/paths/components.instructions.md)
2. Read: [frontend domain context](docs/domains/frontend/context.md)
3. Reference: Feature spec for component requirements
4. Instruct Copilot with all three references
5. After building, write tests using [testing path instructions](./instructions/paths/testing.instructions.md)

### Writing Tests
1. Reference: [testing path instructions](./instructions/paths/testing.instructions.md)
2. Reference: Domain context for patterns (backend or frontend)
3. Reference: Code being tested and feature spec
4. Instruct Copilot to generate tests with specified patterns

### Creating a Database Migration
1. Reference: [database path instructions](./instructions/paths/database.instructions.md)
2. Reference: Feature spec for data requirements
3. Reference: [backend domain context](docs/domains/backend/context.md) for naming conventions
4. Instruct Copilot to create migration with proper patterns

### Updating Architecture Decisions
1. Document in `docs/_global/memory.md` with date
2. If major, create ADR in `.github/decisions/`
3. Notify team via changelog

### Onboarding a New Team Member
1. Have them read `docs/_global/context.md`
2. Have them read domain-specific context for their role
3. Walk through a feature spec together with Copilot

### Fixing a Bug
1. Check `docs/{domain}/memory.md` for similar issues
2. Update memory after fix to prevent recurrence
3. Increment changelog

## When to Update Memory Files

**Add to memory when:**
- You encounter an unexpected behavior or limitation
- A performance issue surfaces and gets fixed
- A security concern gets resolved
- A pattern proves effective or problematic
- A workaround becomes necessary
- A decision gets reversed

**Format** (at top of relevant memory.md):
```markdown
## Recent Updates
- **YYYY-MM-DD**: Brief description (context: link to relevant file or PR)
```

## Copilot Chat Tips

✅ **DO**:
- Include full file paths when referencing docs
- Specify which context files Copilot should use
- Reference feature specs for requirements clarity
- Update memory after learning something new

❌ **DON'T**:
- Assume Copilot knows your project structure
- Skip domain context when switching roles
- Implement without referencing the spec
- Leave memory files stale

## Getting Help

**Question**: "What's our caching strategy?"
- Check: `docs/_global/memory.md` → Search "cache"

**Question**: "How should I structure the API response?"
- Check: `docs/domains/backend/context.md` → Look for "API" or "response"
- Check: Feature spec for the endpoint

**Question**: "We tried this before, why didn't it work?"
- Check: `docs/domains/{relevant-domain}/memory.md` → Look for date + keywords
- Check: `docs/archive/` for historical records

---

**Document Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: Team Lead
