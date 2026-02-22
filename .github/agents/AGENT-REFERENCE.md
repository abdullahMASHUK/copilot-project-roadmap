# Agent Reference Guide

Quick reference for when to use each agent in your workflow.

---

## Agent Overview

```
┌──────────────────────────────────────────────────────────────┐
│                      AGENT REFERENCE                         │
└──────────────────────────────────────────────────────────────┘

TWO TYPES OF AGENTS:

1. ORCHESTRATION AGENTS (Feature Development)
   ├─ Use: SRS → Feature Specs → Implementations → Review
   ├─ Location: .github/agents/orchestration/
   ├─ Agents:
   │  ├─ project-planner.agent.md (transforms SRS → specs)
   │  ├─ backend-implementation.agent.md (implement backend)
   │  ├─ frontend-implementation.agent.md (implement frontend)
   │  └─ review-qa.agent.md (verify implementations)
   └─ When: "I have an SRS and need to develop a feature"

2. ARCHITECTURAL AGENT (Design Decisions)
   ├─ Use: Make architectural decisions, create ADRs
   ├─ Location: .github/agents/plan.agent.md
   └─ When: "Should we use PostgreSQL or MongoDB?" or "How do we implement caching?"

3. INSTRUCTIONS + PROMPTS (For code work)
   ├─ Use: Code patterns, styling, testing approaches
   ├─ Locations:
   │  ├─ .github/instructions/ (role-specific + path-specific)
   │  ├─ .github/prompts/ (reusable workflows)
   │  └─ docs/domains/ (tech stack + learnings)
   └─ When: Writing components, fixing bugs, following conventions
```

---

## Decision Tree: Which Agent to Use?

```
START: What do I need to do?
│
├─ "I have new requirements (SRS) for a feature"
│  └─→ USE: Orchestration System
│      1. Read: .github/agents/orchestration/WORKFLOW.md
│      2. Start with SRS template: .github/templates/handoffs/srs-input-template.md
│      3. Use Agents: project-planner → backend/frontend-impl → review/qa
│
├─ "I need to decide on technical approach"
│  ├─ "For a feature I'm building" → USE: Project Planner Agent (in orchestration)
│  └─ "For system architecture/design" → USE: plan.agent.md (architectural decisions)
│
├─ "I need to write code RIGHT NOW"
│  └─→ USE: Domain Instructions + Prompts
│      1. Reference: .github/instructions/
│      2. Use: implement-from-spec, test-gen, security-review, refactor-slice
│      3. Follow: Project context + Domain context + Path context
│
├─ "I'm implementing a feature from an existing spec"
│  ├─ "Has full spec from planner" → USE: Implementation Agents (orchestration)
│  └─ "Quick feature without formal spec" → USE: Domain instructions + prompts
│
├─ "I found a bug or need to improve code"
│  └─→ USE: Domain Instructions + prompts
│      Reference .github/instructions/ for patterns
│      Update docs/domains/{domain}/memory.md with lessons
│
└─ "I need help understanding project decisions"
   └─→ READ:
       - docs/_global/memory.md (why decisions were made)
       - docs/projects/{project}/memory.md (project-specific decisions)
       - docs/domains/{backend|frontend}/memory.md (domain patterns)
```

---

## Agent Details

### Orchestration Agents (Feature Development)

**Use When**: You have a Software Requirements Specification and need to deliver a complete feature to production.

**Include**: SRS, diagrams, tech context, acceptance criteria

**Get**: Feature specs, backend guide, frontend guide, diagrams, roadmap

**Process**: 4-8 weeks
- Week 1-2: Planning → Feature spec + diagrams
- Week 3: Parallel backend + frontend implementation
- Week 4: Review & verification
- Week 4-5: Deployment

**Agents**:
- [project-planner.agent.md](./agents/orchestration/project-planner.agent.md) — Transforms SRS into specs
- [backend-implementation.agent.md](./agents/orchestration/backend-implementation.agent.md) — Implements backend
- [frontend-implementation.agent.md](./agents/orchestration/frontend-implementation.agent.md) — Implements frontend
- [review-qa.agent.md](./agents/orchestration/review-qa.agent.md) — Verifies implementations

**Start Here**: [.github/agents/orchestration/WORKFLOW.md](.github/agents/orchestration/WORKFLOW.md)

---

### plan.agent.md (Architectural Decisions)

**Use When**: Making system-wide design decisions, evaluating technologies, or creating ADRs.

**Include**: Current architecture, constraints, options to evaluate, context

**Get**: Decision analysis, recommendation, implementation plan, ADR template

**Examples**:
- "Should we switch from Express to Fastify?"
- "How should we implement caching in our API?"
- "What's the best approach for real-time notifications?"
- "Should we migrate from MongoDB to PostgreSQL?"

**Not For**: Individual feature development (use orchestration instead)

**File**: [.github/agents/plan.agent.md](.github/agents/plan.agent.md)

---

### Domain Instructions + Prompts

**Use When**: Writing code, following patterns, improving existing code.

**Include**: Code context, feature spec, relevant instructions

**Get**: Code following project patterns, tests, documentation

**Resources**:
- Instructions: [.github/instructions/](./instructions/)
  - [backend.instructions.md](./instructions/backend.instructions.md) — Backend patterns
  - [frontend.instructions.md](./instructions/frontend.instructions.md) — Frontend patterns
  - [paths/](./instructions/paths/) — Directory-specific patterns (api, components, services, tests, database)
  
- Prompts: [.github/prompts/](./prompts/)
  - `implement-from-spec` — Build from requirements
  - `test-gen` — Generate test coverage
  - `security-review` — Audit for vulnerabilities
  - `refactor-slice` — Improve code without changing behavior

---

## Common Scenarios

### Scenario 1: New Feature (Full Development)

```
SRS received
   ↓
Use: Orchestration System
   ├─ Project Planner Agent → Feature spec + diagrams
   ├─ Backend Implementation Agent → Backend PR
   ├─ Frontend Implementation Agent → Frontend PR
   └─ Review/QA Agent → Verification
   ↓
Deploy to production
```

**Time**: 4-8 weeks  
**Use Instructions**: [.github/agents/orchestration/WORKFLOW.md](.github/agents/orchestration/WORKFLOW.md)

---

### Scenario 2: Bug Fix or Quick Improvement

```
Issue identified
   ↓
Use: Domain Instructions + Prompts
   ├─ Reference relevant domain instruction
   ├─ Use refactor-slice or security-review prompt
   └─ Implement fix
   ↓
Update memory with lessons
```

**Time**: Hours to days  
**Use Instructions**: [.github/instructions/](./instructions/)

---

### Scenario 3: Code Review / Refactoring

```
Code needs review
   ↓
Use: security-review or refactor-slice prompt
   ├─ Reference domain instructions for patterns
   └─ Implement improvements
   ↓
Update memory with lessons
```

**Time**: Hours  
**Use Instructions**: [.github/prompts/](./prompts/)

---

### Scenario 4: Architectural Decision

```
Architecture question
   ↓
Use: plan.agent.md
   ├─ Reference global context and decisions
   └─ Analyze options and recommend approach
   ↓
Create ADR and update docs/_global/memory.md
```

**Time**: Days  
**Agent**: [.github/agents/plan.agent.md](.github/agents/plan.agent.md)

---

## Agent Communication Template

### For Orchestration Agents

```markdown
I'm implementing [Feature Name] using the orchestration system.

**SRS**: [Link to SRS.md]
**Project**: [backend-api | frontend-web]
**Agent**: [Specify which orchestration agent]

Reference:
- Project Context: [Link to project context.md]
- Domain Context: [Link to domain context.md]
- Original SRS: [Link]

[Specific instructions for the agent]
```

### For plan.agent.md

```markdown
I need to make an architectural decision.

**Decision**: [What decision needs to be made]

Reference:
- Global Context: [docs/_global/context.md]
- Global Memory: [docs/_global/memory.md]
- Domain Context: [Relevant domain context]

Consider:
- [Constraint 1]
- [Constraint 2]
- [Option 1 vs Option 2]

Provide analysis and recommendation with ADR template.
```

### For Instructions/Prompts

```markdown
I'm working on [Code Component].

**Spec**: [Link to feature spec or requirement]
**Context**: [Link to domain context]
**Instructions**: [Link to relevant instruction file]
**Prompt**: [Name of prompt: implement-from-spec | test-gen | etc.]

[Your code or request]
```

---

## Key Distinctions

### When to Use Orchestration vs. plan.agent

| Scenario | Orchestration | plan.agent |
|----------|---------------|-----------|
| "Build feature from SRS" | ✅ YES | ❌ NO (wrong agent) |
| "Decide on tech stack" | ❌ NO (wrong agent) | ✅ YES |
| "Implement component" | ✅ YES (if part of feature) | ❌ NO |
| "Evaluate caching approach" | ❌ NO (wrong level) | ✅ YES |
| "Write unit tests" | ✅ YES (if feature) | ❌ NO |
| "Plan deployment strategy" | ❌ NO (architectural) | ✅ YES |

---

## File Structure for Reference

```
.github/
├── agents/
│   ├── orchestration/              ← USE FOR FEATURE DEVELOPMENT
│   │   ├── WORKFLOW.md             (step-by-step guide)
│   │   ├── HANDOFF-PROTOCOL.md     (how agents pass work)
│   │   ├── project-planner.agent.md
│   │   ├── backend-implementation.agent.md
│   │   ├── frontend-implementation.agent.md
│   │   └── review-qa.agent.md
│   │
│   └── plan.agent.md               ← USE FOR DECISIONS (NOT FEATURES)
│
├── instructions/                   ← USE FOR CODE PATTERNS
│   ├── backend.instructions.md
│   ├── frontend.instructions.md
│   ├── actions.instructions.md
│   └── paths/
│       ├── api.instructions.md
│       ├── components.instructions.md
│       ├── services.instructions.md
│       ├── testing.instructions.md
│       └── database.instructions.md
│
├── prompts/                        ← REUSABLE WORKFLOWS
│   ├── implement-from-spec.prompt.md
│   ├── test-gen.prompt.md
│   ├── security-review.prompt.md
│   └── refactor-slice.prompt.md
│
└── templates/
    └── handoffs/
        └── srs-input-template.md   ← START HERE FOR NEW FEATURE

docs/
├── _global/
│   ├── context.md                  (company-wide decisions)
│   └── memory.md                   (why decisions made)
├── domains/
│   ├── backend/
│   │   ├── context.md
│   │   └── memory.md
│   └── frontend/
│       ├── context.md
│       └── memory.md
└── projects/
    ├── backend-api/
    │   ├── context.md              (project-specific decisions)
    │   └── memory.md
    └── frontend-web/
        ├── context.md
        └── memory.md
```

---

**Reference Version**: 1.0  
**Last Updated**: February 23, 2026
